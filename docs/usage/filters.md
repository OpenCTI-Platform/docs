# Filter knowledge

In OpenCTI, you can filter data to target or view entities that have particular attributes.

## Filters usages
Filters are used in different locations in the platform.
- in entities lists: to display only the entities matching the filters. If an export or a background task is generated, only the filtered data will be taken into account.
- in investigations and knowledge graphs: to display only the entities matching the filters.
- in workspaces: to create graphs with only the entities matching the filters.
- in feeds, taxii collections, triggers, streams, playbooks, background tasks: to process only the data  or events matching the filters.

There are two types of filters:
- dynamic filters: they are not stored in the database, they enable to filter view in the UI. Examples: filters in entities list, investigations, knowledge graphs;
- stored filters: They are attributes of an entity, they are stored as an attribute in the object. Examples: filters of workspaces, feeds, taxii collections, triggers, streams, playbooks.

## Filters format (since 5.12)
The filters format since OpenCTI 5.12 is of type FilterGroup (see below). This is the format to use when doing query via the API.
This enables to do complex filters imbrication with different operators ('and', 'or').

```
FilterGroup: {
    mode: 'and' | 'or',
    filters: Filter[],
    filterGroups: FilterGroup[],
}

Filter {
    key: string,
    values: string[],
    operator: string,
    mode: 'and' | 'or',
}
```

In a filter group, the 'mode' can be equal to 'and' or 'or' and represent the operator between the different filters and groups in the 'filters' and 'filterGroups' arrays.
The 'filters' and 'filterGroups' arrays are composed of objects of type Filter/FilterGroup.
The base entity of a filter group object is a filter with 4 attributes:
- a key, representing on what we want to do the filtering (exemple: 'objectLabel' to filter on labels, 'createdBy' to filter on the entity creation date, 'report_types', 'entity_type'... ).
- an array of values, representing the values we are interested in.
- an operator representing the operation we want to apply between the key and the values.
- a mode ('and' or 'or') to apply between the values if there are several ones.

### Operators
The available operators are:
- ```'eq'```  for 'equal',
  

    Exemple: ``entity_type = 'Report'``


- ```'not_eq'``` for 'unequal',
- ```'gt'/'gte'``` for 'greater than'/'greater than or equal',


    Exemple: ``confidence > 50``


- ```'lt'/'lte'``` for 'lower than'/'lower than or equal',
- ```'nil'``` for 'empty',


    Exemple: ``marking is empty``


- ```'not_nil'``` for 'not empty'.

### Examples
- entity_type = 'Report'
```
filters = {
    mode: 'and',
    filters: [{
        key: 'entity_type',
        values: ['Report'],
        operator: 'eq',
        mode: 'or', // useless here because values contains only 1 value
    }],
    filterGroups: [],
};
```

- (entity_type = 'Report') AND (label = 'label1' OR 'label2')
```
filters = {
    mode: 'and',
    filters: [
        {
            key: 'entity_type',
            values: ['Report'],
            operator: 'eq',
            mode: 'or',
        },
        {
            key: 'objectLabel',
            values: ['label1', 'label2'],
            operator: 'eq',
            mode: 'or',
        }
    ],
    filterGroups: [],
};
```

- (entity_type = 'Report') AND (confidence > 50 OR marking is empty)
```
filters = {
    mode: 'and',
    filters: [
        {
            key: 'entity_type',
            values: ['Report'],
            operator: 'eq',
            mode: 'or',
        }
    ],
    filterGroups: [
        {
            mode: 'or',
            filters: [
                {
                    key: 'confidence',
                    values: ['50'],
                    operator: 'gt',
                    mode: 'or',
                },
                {
                    key: 'objectMarking',
                    values: [],
                    operator: 'nil',
                    mode: 'or',
                },
            ],
            filterGroups: [],
        }
    ],
};
```

### Filter keys

#### Allowed filter keys
Filter keys are now of type string, but they can't be any string. Indeed only keys existing in the entities schema definition are allowed.

This checking prevents keys misswriting when constructing filters via the API: if a user write a non-existing key ('object-label' instead of 'objectLabel' for instance), the API will return an error instead of an empty list. Thus the user will understand he wrote a bad key, whereas an empty list can be interpreted as 'no result found for the given filtering'.

#### Supported filter keys in streams and triggers
In streams and triggers, only some keys are supported for the moment:
- ``objectAssignee``
- ``confidence``
- ``createdBy``
- ``creator``
- ``x_opencti_detection``
- ``indicator_types``
- ``objectLabel``
- ``x_opencti_main_observable_type``
- ``objectMarking``
- ``objects``
- ``pattern_type``
- ``priority``
- ``revoked``
- ``severity``
- ``x_opencti_score``
- ``entity_type``
- ``x_opencti_workflow_id``
- ``connectedToId`` (for instance triggers)
- ``fromId`` (the instance in the 'from' of a relationship)
- ``fromTypes`` (the entity type in the 'from' of a relationship)
- ``toId``
- ``toTypes``

#### Some important keys
Here are some important keys as an example. For a complete list of the available keys, see the attributes and relations schema definitions.
X refers here to the filtered entities.
- ``objectLabel``: label of X,
- ``objectMarking``: marking of X,
- ``createdBy``: author of X,
- ``creator_id``: technical creator of X,
- ``created_at``: date of creation of X in the platform,
- ``confidence``: confidence of X,
- ``entity_type``: X entity type ('Report', 'Stix-Cyber-Observable', ...),

#### Special keys
Some keys are a bit special: there are not existing in the schema definition, but are allowed. They describe a special behavior.
It is the case for:
- ``sightedBy``: entities to which X is linked via a stix sighting relationship,
- ``elementId``: entities to which X is related via a stix core relationship,
- ``ids``: any id of X (instance_id, standard_id, or stix_id),
- ``connectedToId``: the listened instances for an instance trigger.

For some keys, negative filtering is not supported yet (not_eq operator). It is the case for:
- ``elementId``
- ``fromId``
- ``fromTypes``
- ``toId``
- ``toTypes``

## Filters format migration done in 5.12

### Context: why this migration

Before 5.12, it was not possible to do some complex filters combinations: we couldn't imbricate filters with differents modes (and/or), filter on any available attribute or relation for a given entity type, or test empty fields.

The former format was not adapted for such complex filters, and a refacto was necessary.
Indeed:
- the filters frontend and backend formats were different, requiring conversions,
- the filter keys were enums, i.e. the keys should belong to the static list of the available keys for the given entity type,
- in the frontend format, the operator was contained in the key, limiting the operators combinations and creating useless complexity of parsing,
- the frontend format didn't contained a mode attribute, avoiding modes (and/or) customization,
- the flat structure didn't enable filters imbrication at several levels.

```
// old filters format
FrontendFilters: Map<string, { id: string | null, value: string }>

BackendFilters {
    key: string, // an enum that should be in the list of the available filter keys for given the entity type
    values: string[],
    operator: string,
    filterMode: 'and' | 'or',
}
```

Because changing filters format impacts almost everything in the platform, we decided to do a complete refacto once and for all, a refacto that would enable almost all complex combinations and compatible with our long term vision of filters in OpenCTI.

### Content: what has been done

The filters refacto bring major changes in the way filtering is done.

- We change the filters formats (see FilterGroup type above):
  - In the frontend, an operator and a mode are stored for each key,
  - The new format enables filters imbrication thanks to the new attribute 'filterGroups',
  - The keys are of type string (no more static list of enums).
  - The 'values' attribute can no longer contain null values (use the 'nil' operator instead).

- We also rename some filter keys, to be consistant with the entities schema definitions.

- We implemented the handling of the different operators and modes in the backend.

- We introduced a new operator: 'nil' and 'not_nil', enabling to test wether an attribute is empty or not.

### Warnings: what you should do to ensure a correct migration

We wrote a migration to convert all the old stored filters (filters contained in streams, taxii collections, feeds, triggers, workspaces) in the new format.
But you might have things to change for your own bits of code (your own connectors, queries, python scripts...). For each filter you hardcoded, you should change the filters format in the new one.

To convert an old filters (in the old backend format) in the new format:
- rename the key if it is a key that has been changed in the migration.
- replace the 'filterMode' attribute name by 'mode'.
- if 'values' = [null]: change the filter with:
  - values = []
  - operator = 'nil' if operator was 'eq', operator = 'not_nil' if operator was 'not_eq',
- if 'values' contained null:
  - remove the null value of the values array,
  - create a new filter with
    - values = []
    - operator = 'nil' if operator was 'eq', operator = 'not_nil' if operator was 'not_eq',
- create an object with 3 attributes:
  - mode = 'and',
  - filters = an array of your old filters after having applied the previous steps,
  - filterGroups = [].
```
const oldBackendFilter = {
    key: 'old_key',
    values: ['value1', 'value2'],
    operator: 'XX',
    filterMode: 'XX',
}

const convertedOldBackendFilter = {
    key: 'converted_key_if_necessary',
    values: ['value1', 'value2'],
    operator: 'XX',
    mode: 'XX',
}

const newFilters = {
    mode: 'and',
    filters: [convertedOldBackendFilter1, convertedOldBackendFilter2...], // an array of all the convertedOldBackendFilter
    filterGroups: [],
}
```


#### List of filter keys that have been renamed
```
const keyConvertor = [ // array of [oldKey, newKey] for the renamed keys
    ['labelledBy', 'objectLabel'],
    ['markedBy', 'objectMarking'],
    ['objectContains', 'objects'],
    ['killChainPhase', 'killChainPhases'],
    ['assigneeTo', 'objectAssignee'],
    ['participant', 'objectParticipant'],
    ['creator', 'creator_id'],
    ['hasExternalReference', 'externalReferences'],
    ['hashes_MD5', 'hashes.MD5'],
    ['hashes_SHA1', 'hashes.SHA-1'],
    ['hashes_SHA256', 'hashes.SHA-256'],
    ['hashes_SHA512', 'hashes.SHA-512']
]
```

#### Examples of filters in new format with nil operator and key renaming

Below is an example of the new filters format for an old filters indicating:

(entity_type = Report) AND (label = No label OR label1)

```
const oldBackendFilter = [
    {
      key: 'labelledBy',
      values: [label1_id, null],
      operator: 'eq',
      filterMode: 'or',
    },
    {
      key: 'entity_type',
      values: ['Report'],
      operator: 'eq',
      filterMode: 'or',
    },
];

const newFilters = {
    mode: 'and',
    filters: [
      {
        key: 'objectLabel',
        values: [label1_id],
        operator: 'eq',
        mode: 'or',
      },
      {
        key: 'objectLabel',
        values: [],
        operator: 'nil',
        mode: 'or',
      },
      {
        key: 'entity_type',
        values: ['Report'],
        operator: 'eq',
        mode: 'or',
      }
    ],
    filterGroups: [],
};
```