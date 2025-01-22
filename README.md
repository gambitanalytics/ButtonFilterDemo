# ButtonFilter

This is a demo of the `ButtonFilters` component. It's a barebones component that accepts one input, `items` which should be formatted as 
a comma-delimited string listing each individual value that should appear as a fitlerable value.

The component allows the user to quickly filter for one or more of the `items` by clicking them in the list, similar to the [slicer](https://support.microsoft.com/en-us/office/use-slicers-to-filter-data-249f966b-a9d5-4b0f-b31a-12651785d29d) in Excel. When none of the `items` are selected, the default behavior is to show 
all items.

The component was built for a much earlier version of Evidence and needs some work to make it complete. I'm not a JS developer and I am shit with Svelte so this 
is just a quick hack to make something that would work for me. Note that it is possible to use duckdb's `list()` to generate the list of `items` but my implementation of that causes some problems in local dev right now due to the bug being addressed by [this PR](https://github.com/evidence-dev/evidence/pull/3032). So for now, I'm just passing in the raw string to `items`.

## Usage

Create a "components" directory in your project if one does not already exist. Copy ButtonFilters.svelte into that directory. Then, create a ButtonFilters component on your page like so:

`<ButtonFilters items="comma,separated,list,of,items,for,filtering" name="month_filter" />`

Finally, in the SQL that selects data you want to be filterable, use the filter like so:

`select my_data
from my_table
where column_to_be_filtered in (${inputs.month_filter})`

That's it.