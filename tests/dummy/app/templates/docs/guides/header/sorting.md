# Sorting

Ember Table ships with sorting built in to the table. Default sorting is a
standard merge sort which does not affect the original ordering of the rows
passed into the table. Users can sort by a column and toggle sort direction by
clicking on its header, and can sort by multiple columns by clicking with `cmd`
or `ctrl`.

{{#docs-demo as |demo|}}
  {{#demo.example name="docs-example-sortings"}}
    {{! BEGIN-SNIPPET docs-example-sortings.hbs }}
    <div class="demo-container">
      <EmberTable as |t|>
        <t.head
          @columns={{this.columns}}
          @sorts={{this.sorts}}

          @onUpdateSorts={{fn (mut this.sorts)}}

          @widthConstraint='gte-container'
          @fillMode='first-column'
        />

        <t.body @rows={{this.rows}} />
      </EmberTable>
    </div>
    {{! END-SNIPPET }}
  {{/demo.example}}

  {{demo.snippet name='docs-example-sortings.hbs'}}
  {{demo.snippet label='component.js' name='docs-example-sortings.js'}}
{{/docs-demo}}

## Activating Sorting

Sorting in tables is a DDAU process - a new sort order is passed to
`onUpdateSorts` and can then be passed back into the table, updating the sort
order. Because sorting cannot occur without the `onUpdateSorts` action, it will
effectively be disabled unless the action is set - headers will not be
clickable, and nothing will change when users select them.

The sort order is passed into the table via the `sorts` argument. This should be
an array of sort objects should correspond to any of the `valuePaths` in the
columns for the table. When multiple sort objects are passed, columns are sorted
by each sort in order:

```js
let sorts = [
  {
    valuePath: 'name',
    isAscending: false,
  },
  {
    valuePath: 'price',
    isAscending: true,
  },
]
```

Note that you can control the sort order externally this way as well, for
instance if you have a different UX than clicking on headers to control the sort
order.

Table headers can be passed a `sortFunction` and `compareFunction` as well. If
you want to sort the content of the table asynchronously, you can unset the
`sortFunction` and handle the async request yourself.

```hbs
<EmberTable as |t|>
  <t.head
    sortFunction=null
  />
</EmberTable>
```

## Disabling Sorting

As mentioned before, sorting is disabled by default unless the table is given an
`onUpdateSorts` action. Sorting can also be disabled by setting the `isSortable`
option on a particular column to `false`:

```js
let columns = [
  {
    valuePath: 'name',
    isSortable: false,
  }
];
```

## Sorting States

By default, when a user repeatedly clicks on a column header to change sorting, ember-table
cycles through these states:

  * Unsorted (rows are in the same order as provided to the table)
  * Sort Descending
  * Sort Ascending

You can use the `onUpdateSorts` action to change this behavior. For example, to cycle through 2 states
(Ascending, Descending) rather than the default 3, you can modify the `onUpdateSorts` action as follows:
  * If it is passed an empty `sorts` array, get the current sort array and toggle the `isAscending` property
  * If it is passed anything else, set `this.sorts` to the passed-in `sorts` array (default behavior).

This demo shows that in action:

{{#docs-demo as |demo|}}
  {{#demo.example name="docs-example-2-state-sortings"}}
    {{! BEGIN-SNIPPET docs-example-2-state-sortings.hbs }}
    <div class="demo-container" data-test-demo="docs-example-2-state-sortings">
      <EmberTable as |t|>
        <t.head
          @columns={{this.columns}}
          @sorts={{this.sorts}}

          @onUpdateSorts={{this.twoStateSorting}}

          @widthConstraint='gte-container'
          @fillMode='first-column'
        />

        <t.body @rows={{this.rows}} />
      </EmberTable>
    </div>
    {{! END-SNIPPET }}
  {{/demo.example}}

  {{demo.snippet name='docs-example-2-state-sortings.hbs'}}
  {{demo.snippet label='component.js' name='docs-example-2-state-sortings.js'}}
{{/docs-demo}}

## Sorting empty values

Empty values can be treated differently depending on the needs by using the `sortEmptyLast` option.
To see its effect, try sorting the "Material" column in ascending order with and without "sortEmptyLast" checked.

{{examples/sorting-empty-values}}
