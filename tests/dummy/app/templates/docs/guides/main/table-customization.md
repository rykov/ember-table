# Table Customization

There are two main ways to customize tables:

1. Customizing the cell templates
2. Customizing the table elements (`thead`, `tbody`, `tr`, `td`, etc)

Ember Table provides ways to customize both of these.

## Cell Templates

Tables yield down to the cell level, meaning you can customize the template of
each cell through yielding alone. No need to pass in components or specify
lookup paths:

{{#docs-demo as |demo|}}
  {{#demo.example name='expanded'}}
    <div class="demo-container">
      {{! BEGIN-SNIPPET table-customization-custom-cell-template.hbs }}
      <EmberTable as |t|>
        <t.head @columns={{this.columns}} as |h|>
          <h.row as |r|>
            <r.cell as |column|>
              Custom Header {{column.name}}
            </r.cell>
          </h.row>
        </t.head>

        <t.body @rows={{this.rows}} as |b|>
          <b.row as |r|>
            <r.cell as |value|>
              Custom Cell {{value}}
            </r.cell>
          </b.row>
        </t.body>
      </EmberTable>
      {{! END-SNIPPET }}
    </div>
  {{/demo.example}}

  {{demo.snippet name='table-customization-custom-cell-template.hbs'}}
  {{demo.snippet name='table-customization-custom-cell-template.js' label='component.js'}}
{{/docs-demo}}

If you want to customize a header cell but still want to include the elements to sort and
to resize a column, use the `ember-th/sort-indicator` and `ember-th/resize-handle`
components:

{{#docs-demo as |demo|}}
  {{#demo.example}}
    {{! BEGIN-SNIPPET table-customization-example-sorting.hbs }}
    <div class='demo-options'>
      <label>
        <input type='checkbox' checked={{this.showSortIndicator}} onclick={{fn (mut this.showSortIndicator) (not this.showSortIndicator)}}>
        Show Sort Indicator
        <span class='small'>(Click header to sort)</span>
      </label>
      <label>
        <input type='checkbox' checked={{this.showResizeHandle}} onclick={{fn (mut this.showResizeHandle) (not this.showResizeHandle)}}>
        Show Resize Handle <span class='small'>(Only appears on hover)</span>
      </label>
    </div>
    <div class="demo-container">
      <EmberTable as |t|>
        <t.head
          @columns={{this.columnsForSorting}}
          @sorts={{this.sorts}}

          @onUpdateSorts={{fn (mut this.sorts)}}
          @widthConstraint='gte-container'
          @fillMode='first-column'

          as |h|
        >
          <h.row as |r|>
            <r.cell as |columnValue columnMeta|>
              {{#if this.showSortIndicator}}
                <EmberTh::SortIndicator @columnMeta={{columnMeta}} />
              {{/if}}
              {{columnValue.name}}
              {{#if this.showResizeHandle}}
                <EmberTh::ResizeHandle @columnMeta={{columnMeta}} />
              {{/if}}
            </r.cell>
          </h.row>
        </t.head>

        <t.body @rows={{this.rowsForSorting}} />
      </EmberTable>
    </div>
    {{! END-SNIPPET }}
  {{/demo.example}}

  {{demo.snippet name='table-customization-example-sorting.hbs'}}
  {{demo.snippet label='component.js' name='table-customization-example-sorting.js'}}
{{/docs-demo}}

Oftentimes you'll want to provide custom components for use in table headers,
cells, and footers. It's also pretty common to want different types of
components used in each column. Ember Table solves this by passing the
unadulterated column definition to each cell, which gives you a place to put
information about the cell, including the name of the component it should use.

You can combine this with the `component` helper to render a different component
per cell:

{{#docs-demo as |demo|}}
  {{#demo.example name="custom-cell-component"}}
    <div class="demo-container small">
      {{! BEGIN-SNIPPET table-customization-custom-cell-component.hbs }}
      <EmberTable as |t|>
        <t.head @columns={{this.columnsWithComponents}} as |h|>
          <h.row as |r|>
            <r.cell as |column|>
              {{#if column.headerComponent}}
                {{#component column.headerComponent color=column.color}}
                  {{column.heading}}
                {{/component}}
              {{else}}
                {{column.heading}}
              {{/if}}
            </r.cell>
          </h.row>
        </t.head>

        <t.body @rows={{this.rows}} as |b|>
          <b.row as |r|>
            <r.cell as |cell column|>
              {{#if column.cellComponent}}
                {{#component column.cellComponent color=column.color}}
                  {{cell}}
                {{/component}}
              {{else}}
                {{cell}}
              {{/if}}
            </r.cell>
          </b.row>
        </t.body>
      </EmberTable>
      {{! END-SNIPPET }}
    </div>
  {{/demo.example}}

  {{demo.snippet name='table-customization-custom-cell-component.hbs'}}
  {{demo.snippet name='table-customization-custom-cell-component.js' label='component.js'}}
  {{demo.snippet name='custom-header.hbs' label='custom-header.hbs'}}
  {{demo.snippet name='custom-cell.hbs' label='custom-cell.hbs'}}
{{/docs-demo}}

It is also possible to customize the cell templates based on details of the row
itself. The row is also passed to cells (but not headers, which don't have
rows), allowing us to customize the template in the same way as columns:

{{#docs-demo as |demo|}}
  {{#demo.example name="rows-with-components"}}
    <div class="demo-container small">
      {{! BEGIN-SNIPPET table-customization-rows-with-components.hbs }}
      <EmberTable as |t|>
        <t.head @columns={{this.columnsForRowsWithComponents}} />

        <t.body @rows={{this.rowsWithComponents}} as |b|>
          <b.row as |r|>
            <r.cell as |cell column row|>
              {{#component row.cellComponent color=row.color}}
                {{cell}}
              {{/component}}
            </r.cell>
          </b.row>
        </t.body>
      </EmberTable>
      {{! END-SNIPPET }}
    </div>
  {{/demo.example}}

  {{demo.snippet name='table-customization-rows-with-components.hbs'}}
  {{demo.snippet name='table-customization-rows-with-components.js' label='component.js'}}
  {{demo.snippet name='custom-cell.hbs' label='custom-cell.hbs'}}
{{/docs-demo}}

## Table Components

Usually customizing table cells is all the flexibility you need. Sometimes
however it makes more sense to customize the actual table elements themselves.
Each level of the table can access the API object passed to it to do this,
so for instance row components can be customized using the row value, and
cell components can be customized using the cell value, column value, or row
value:

{{#docs-demo as |demo|}}
  {{#demo.example name="custom-table-components"}}
    <div class="demo-container small">
      {{! BEGIN-SNIPPET table-customization-custom-table-components.hbs }}
      <EmberTable as |t|>
        <t.head @columns={{this.columnsWithSelection}} as |h|>
          <h.row as |r|>
            <r.cell
              class="{{if r.columnValue.isSelected 'is-column-selected'}}"

              as |column|
            >
              {{column.name}}
            </r.cell>
          </h.row>
        </t.head>

        <t.body @rows={{this.rowsWithChildren}} as |b|>
          <b.row
            class="{{if b.rowValue.children 'has-children'}}"

            as |r|
          >
            <r.cell
              class="{{if r.columnValue.isSelected 'is-column-selected'}}"

              as |cell|
            >
              {{cell}}
            </r.cell>
          </b.row>
        </t.body>
      </EmberTable>
      {{! END-SNIPPET }}
    </div>
  {{/demo.example}}

  {{demo.snippet name='table-customization-custom-table-components.hbs'}}
  {{demo.snippet name='table-customization-custom-table-components.js' label='component.js'}}
{{/docs-demo}}

Table components also implement typical event handlers, such as `onClick` and
`onDoubleClick`. In general, it's not recommended that you extend table
components and is not considered public API to do so. If you would like extra
functionality added to table components, open an issue on Github!
