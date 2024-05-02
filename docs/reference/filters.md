# Filter knowledge

In OpenCTI, you can filter data to target or view entities that have particular attributes.


## Filters usages

There are two types of filters that are used in many locations in the platform:

- in entities lists: to display only the entities matching the filters. If an export or a background task is generated, only the filtered data will be taken into account,
- in investigations and knowledge graphs: to display only the entities matching the filters,
- in dashboards: to create widget with only the entities matching the filters,
- in feeds, TAXII collections, triggers, streams, playbooks, background tasks: to process only the data or events matching the filters.

### Dynamic filters

Dynamic filters are not stored in the database, they enable to filter view in the UI, e.g. filters in entities list, investigations, knowledge graphs.

However, they are still persistent in the platform frontend side. The filters used in a view are saved as URL parameters, so you can save and share links of these filtered views.

Also, your web browser saves in local storage the filters that you are setting in various places of the platform, allowing to retrieve them when you come back to the same view. You can then keep working from where you left of.

![Filtering entities](./assets/filters-migration-example1.png)

<a id="stored-filter-section"></a>
### Stored filters

Stored filters are attributes of an entity, and are therefore stored in the database. They are stored as an attribute in the object itself, e.g. filters in dashboards, feeds, TAXII collections, triggers, streams, playbooks.


## Create a filter

To create a filter, add every key you need using the 'Add filter' select box. It will give you the possible attributes on which you can filter in the current view.

A grey box appears and allows to select:

1. the operator to use, and
2. the values to compare (if the operator is not "empty" or "not_empty").

You can add as many filters as you want, even use the same key twice with different operators and values.

The boolean modes (and/or) are either **global** (between every attribute filters) or **local** (between values inside a filter). Both can be switched with a single click, changing the logic of your filtering.


## Filters format

Since OpenCTI 5.12, the OpenCTI platform uses a new filter format called `FilterGroup`. The `FilterGroup` model enables to do complex filters imbrication with different boolean operators, which extends greatly the filtering capabilities in every part of the platform.

### Structure

The new format used internally by OpenCTI and exposed through its API, can be described as below:

```ts
// filter formats in OpenCTI >= 5.12

type FilterGroup = {
  mode: 'and' | 'or'
  filters: Filter[]
  filterGroups: FilterGroup[] // recursive definition
}

type Filter  = {
  key: string[] // or single string (input coercion)
  values: string[]
  operator: 'eq' | 'not_eq' | 'gt' // ... and more
  mode: 'and' | 'or',
}
```

#### Properties

In a given filter group, the `mode` (`and` or `or`) represents the boolean operation between the different `filters` and `filterGroups` arrays. The `filters` and `filterGroups` arrays are composed of objects of type Filter and FilterGroup.

The `Filter` has 4 properties:

- a `key`, representing the kind of data we want to target (example: `objectLabel` to filter on labels or `createdBy` to filter on the author),
- an array of `values`, representing the values we want to compare to,
- an `operator` representing the operation we want to apply between the `key` and the `values`,
- a `mode` (`and` or `or`) to apply between the values if there are several ones.

#### Operators

The available operators are:

| Value   | Meaning               | Additional information                                    |
|---------|-----------------------|-----------------------------------------------------------|
| eq      | equal                 |                                                           |
| not_eq  | different             |                                                           |
| gl      | greater than          | against textual values, the alphabetical ordering is used |
| gte     | greater than or equal | against textual values, the alphabetical ordering is used |
| lt      | lower than            | against textual values, the alphabetical ordering is used |
| lte     | lower than or equal   | against textual values, the alphabetical ordering is used |
| nil     | empty / no value      | `nil` do not require anything inside `values`             |
| not_nil | non-empty / any value | `not_nil` do not require anything inside `values`         |

In addition, there are operators:

- `starts_with` / `not_starts_with` / `ends_with` / `not_ends_with` / `contains` / `not contains`, available for searching in short string fields (name, value, title, etc.),
- `search`, available in short string and text fields.

There is a small difference between `search` and `contains`. `search` finds any occurrence of specified words, regardless of order, while "contains" specifically looks for the exact sequence of words you provide.

!!! note "Always use single-key filters"

    Multi-key filters are not supported across the platform and are reserved to specific, internal cases.


#### Examples

**entity_type = 'Report'**

```ts
filters = {
    mode: 'and',
    filters: [{
        key: 'entity_type',
        values: ['Report'],
        operator: 'eq',
        mode: 'or', // useless here because values contains only one value
    }],
    filterGroups: [],
};
```

**(entity_type = 'Report') AND (label = 'label1' OR 'label2')**

```ts
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

**(entity_type = 'Report') AND (confidence > 50 OR marking is empty)**

```ts
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

### Keys

#### Filter keys validation

Only a specific set of key can be used in the filters.

Automatic key checking prevents typing error when constructing filters via the API. If a user write an unhandled key (`object-label` instead of `objectLabel` for instance), the API will return an error instead of an empty list. Doing so, we make sure the platform do not provide misleading results.

#### Regular filter keys

For an extensive list of available filter keys, refer to the attributes and relations schema definitions.

Here are some of the most useful keys as example. NB: X refers here to the filtered entities.

* ``objectLabel``: label applied on X,
* ``objectMarking``: marking applied on X,
* ``createdBy``: author of X,
* ``creator_id``: technical creator of X,
* ``created_at``: date of creation of X in the platform,
* ``confidence``: confidence of X,
* ``entity_type``: entity type of X ('Report', 'Stix-Cyber-Observable', ...),

#### Special filter keys

Some keys do not exist in the schema definition, but are allowed in addition. They describe a special behavior.

It is the case for:

- ``sightedBy``: entities to which X is linked via a STIX sighting relationship,
- ``workflow_id``: status id of the entities, or status template id of the status of the entities,
- ``connectedToId``: the listened instances for an instance trigger.

For some keys, negative equality filtering is not supported yet (`not_eq` operator). For instance, it is the case for:

- ``fromId``
- ``fromTypes``
- ``toId``
- ``toTypes``

The ``regardingOf`` filter key has a special format and enables to target the entities having a relationship of a certain type with certain entities. Here is an example of filter to fetch the entities related to the entity X:

```ts
filters = {
  mode: 'and',
  filters: [
    {
      key: 'regardingOf',
      values: [
        { key: 'id', values: ['id-of-X'] },
        { key: 'relationship_type', values: ['related-to'] },
      ],
    },
  ],
  filterGroups: [],
};
```

#### Limited support in stream events filtering

Filters that are run against the event stream are not using the complete schema definition in terms of filtering keys.

This concerns:

- Live streams,
- CSV feeds,
- TAXII collection,
- Triggers,
- Playbooks.

For filters used in this context, only some keys are supported for the moment:

- ``confidence``
- ``objectAssignee``
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
- ``connectedToId`` (for the instance triggers)
- ``fromId`` (the instance in the "from" of a relationship)
- ``fromTypes`` (the entity type in the "from" of a relationship)
- ``toId`` (the instance in the "to" of a relationship)
- ``toTypes`` (the entity type in the "to" of a relationship)
