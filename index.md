---
scripts:
  - https://cdn.jsdelivr.net/npm/arquero@latest
  - https://d3js.org/d3-dsv.v1.min.js
imports:
  - https://irydium.dev/components/plotlyjs.md#Plotly
data:
  - big_mac_raw: https://raw.githubusercontent.com/TheEconomist/big-mac-data/master/output-data/big-mac-adjusted-index.csv
---

# Welcome to Irydium!

This is a basic irydium notebook template, designed to get you and up running quickly.

## The Big Mac Index 🍔

The [Economist]'s Big Mac Index tracks the price of the "Big Mac" across various regions over time. [According to wikipedia](https://en.wikipedia.org/wiki/Big_Mac_Index):

> The Big Mac Index is published by The Economist as an informal way of measuring the purchasing power parity (PPP) between two currencies and provides a test of the extent to which market exchange rates result in goods costing the same in different countries. It "seeks to make exchange-rate theory a bit more digestible."

Let's slice some of the data cited over a few different categories, using [Arquero].
We'll take the first ten countries in the list (alphabetically) and then track their results year-by-year.

```{code-cell} js
---
id: big_mac_data_grouped
inputs: [ big_mac_raw ]
inline: true
---
const dt = aq.fromCSV(await big_mac_raw.text());
// re-group data in a form that plotly expects (fixme: could probably do this in a more elegant way with arquero)
return Object.entries(
  (dt.groupby('name')
    .count()
    .select('name')
    .slice(0, 10)
    .join(dt.select(['date', 'name', 'dollar_price']))
    .objects())
  .reduce((acc, {name, date, dollar_price}) => {
    if (!acc[name]) {
      acc[name] = [];
    }
    acc[name].push({date, dollar_price});
    return acc;
  }, {})).map(([name, values]) => {
    return {name, x: values.map(v=>v.date), y: values.map(v=>v.dollar_price)}
  });
```

<Plotly data={big_mac_data_grouped} />

Note that you can remove the rendering of the code cell by removing the `inline: true` property from the above code cell.

[economist]: https://www.economist.com
[arquero]: https://uwdata.github.io/arquero
