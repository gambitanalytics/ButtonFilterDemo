---
title: ButtonFilters Demo
---

Below is a demo of the `ButtonFilters` component. It's a barebones component that accepts one input, `items` which should be formatted as 
a comma-delimited string listing each individual value that should appear as a fitlerable value.

The component allows the user to quickly filter for one or more of the `items` by clicking them in the list, similar to the [slicer](https://support.microsoft.com/en-us/office/use-slicers-to-filter-data-249f966b-a9d5-4b0f-b31a-12651785d29d) in Excel. When none of the `items` are selected, the default behavior is to show 
all items.

<Details title="Demo use case">

  The demo below was mocked up from my actual use case, displaying various budget to actual metrics in a `DataTable`. The user wanted the ability to include/exclude 
  arbitrary months and have immediate visual feedback about which months were/weren't selected. For example, they wanted to be able to see budget to actuals for only the period January, March, and August, the months of their three largest campaigns. They found use of the `DropDown` for this functionality to be 
  cumbersome, and I agreed.

</Details>

The component was built for a much earlier version of Evidence and needs some work to make it complete. I'm not a JS developer and I am shit with Svelte so this 
is just a quick hack to make something that would work for me. Note that it is possible to use duckdb's `list()` to generate the list of `items` but my implementation of that causes some problems in local dev right now due to the bug being addressed by [this PR](https://github.com/evidence-dev/evidence/pull/3032). So for now, I'm just passing in the raw string to `items`.

<Dropdown
    data={fiscal_years}
    name=fiscal_year_filter
    value=fiscal_year
    title="Select a fiscal year"
    defalutValue={max_fiscal_year[0].max_fiscal_year}
/>

<ButtonFilters items="Jan,Feb,Mar,Apr,May,Jun,Jul,Aug,Sep,Oct,Nov,Dec" name="month_filter" />

<DataTable data={cashflow_to_budget} rows=all totalRow=true>
    <Column id=fiscal_month_name title="Fiscal Month" align=center totalAgg="Grand Total" />
    <Column id=budget fmt=usd0 align=center />
    <Column id=actual fmt=usd0 align=center />
    <Column id=variance fmt=usd0 align=center />
</DataTable>

```sql fiscal_years
select distinct
    substring(cast(fiscal_year as varchar),1,4) as fiscal_year
from cashflow.dummy_cashflow_data
order by fiscal_year desc
```

```sql max_fiscal_year
select max(substring(cast(fiscal_year as varchar),1,4)) as max_fiscal_year
from cashflow
```

```sql cashflow_to_budget
select
    fiscal_month
    ,fiscal_month_name
    ,sum(case when income_type = 'Budget' then amount else 0 end) as budget
    ,sum(case when income_type = 'Actual' then amount else 0 end) as actual
    ,sum(case when income_type = 'Actual' then amount else 0 end)-sum(case when income_type = 'Budget' then amount else 0 end) as variance
    ,case when sum(case when income_type = 'Budget' then amount else 0 end) = 0 then 0
    else (sum(case when income_type = 'Actual' then amount else 0 end)-sum(case when income_type = 'Budget' then amount else 0 end))
        /sum(case when income_type = 'Budget' then amount else 0 end) end as pct_variance
from cashflow.dummy_cashflow_data
where substring(cast(fiscal_year as varchar),1,4) = '${inputs.fiscal_year_filter.value}'
  and fiscal_month_name in (${inputs.month_filter})
group by fiscal_month
    ,fiscal_month_name
    ,fiscal_year
order by fiscal_month
```
