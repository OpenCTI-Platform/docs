# Filters format migration done in 5.12

## Context: why this migration

Before 5.12, it was not possible to do some complex filters combinations: we couldn't imbricate filters with differents modes (and/or), filter on any available attribute or relation for a given entity type, or test empty fields.

The former format was not adapted for such complex filters, and a refactoring was necessary.
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

Because changing filters format impacts almost everything in the platform, we decided to do a complete refactoring once and for all. This refactoring will enable almost all complex combinations and will be compatible with our long term vision of filters in OpenCTI.

## Content: what has been done

The new filter implementation bring major changes in the way filtering is done.

- We change the filters formats (see FilterGroup type above):
    - In the frontend, an operator and a mode are stored for each key,
    - The new format enables filters imbrication thanks to the new attribute 'filterGroups',
    - The keys are of type string (no more static list of enums).
    - The 'values' attribute can no longer contain null values (use the 'nil' operator instead).

- We also renamed some filter keys, to be consistant with the entities schema definitions.

- We implemented the handling of the different operators and modes in the backend.

- We introduced a new operator: 'nil' and 'not_nil', enabling to test if an attribute is empty or not.

## Warnings: what you should do to ensure a correct migration

We wrote a migration to convert all filters created prior version 5.12 (filters contained in streams, taxii collections, feeds, triggers, workspaces) in the new format.
But you might have to change your own custom code (your own connectors, queries, python scripts...). For each filter that is hardcoded, you should change the filters format in the new one.

To convert filters prior to version 5.12 in the new format:
- rename the key if it is a key that has been changed in the migration. (The exhaustive list is in the "List of filter keys that have been renamed" section)
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


### List of filter keys that have been renamed
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

### Examples of filters in new format with nil operator and key renaming

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

## Url with old filters
When going to an url with filters in the old format, the user will be redirected to the page but all the settings stored in local (filters, ordering mode...) will be deleted. A warning message is displayed, indicating the url contains filters in the old format.
If you want to share an url that works after 5.12 migration, you need to copy an url from an OpenCTI instance after 5.12.