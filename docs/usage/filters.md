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
Note: some operator may not be allowed for some key, for additional information please navigate to the "Special keys" section.
### Examples of filter using OpenCTI version 5.12+
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
Filter keys are of type string, but they can't be any string. Indeed only keys existing in the entities schema definition are allowed.

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
Some keys does not exist in the schema definition, but are allowed in addition. They describe a special behavior.
It is the case for:
- ``sightedBy``: entities to which X is linked via a stix sighting relationship,
- ``elementId``: entities to which X is related via a stix core relationship,
- ``connectedToId``: the listened instances for an instance trigger.

For some keys, negative filtering is not supported yet (not_eq operator). For instance, it is the case for:
- ``fromId``
- ``fromTypes``
- ``toId``
- ``toTypes``
