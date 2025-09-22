<style>
@import url('https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap');

body {
  font-family: 'Roboto', sans-serif;
}
h1 {
  font-family: 'Roboto', sans-serif;
  font-weight: 700;
}
</style>`
```js
const monthToMonth = await FileAttachment("month-to-month.json").json({typed: true}).then(data => {
  // Loop through the data and return a polished up, trimmed down version
  return data.map(d => {
    return {
      month: new Date(d.date),
      change: d.change
    }
  });
});
```
```js
const monthlyFood = await FileAttachment("monthly-food.json").json({typed: true}).then(data => {
  // Loop through the data and return a polished up, trimmed down version
  return data.map(d => {
    return {
      month: new Date(d.date),
      change: d.change
    }
  });
});
```
```js
const yearOverYear = await FileAttachment("year-over-year.json").json({typed: true}).then(data => {
  console.log(data)
  const nameLookup = {
    "CUUR0000SA0": "All items",
    "CUUR0000SA0L1E":"All items except food and energy"
  }
  return data.map(d => {
    return {
      month: new Date(d.date),
      change: d.change,
      series_id: d.series_id,
      series_items_name: nameLookup[d.series_id],
    }
  });
});
```
# Consumer Price Index – ${d3.utcFormat("%B %Y")(latest.month)}

The Consumer Price Index for All Urban Consumers (CPI-U) ${describe(latest.change)} on a seasonally
adjusted basis, after it ${describe(previous.change)} in ${d3.utcFormat("%B")(previous.month)}, the U.S. Bureau of Labor Statistics reported today.

```js
Plot.plot({
  title: "One-month percent change in CPI for All Urban Consumers (CPI-U), seasonally adjusted",
  marks: [
    Plot.barY(monthToMonth, {
        x: "month",
        y: "change",
        dx:2,
        dy:2,
        tip: true
    }),
    Plot.barY(monthToMonth, {
        x: "month",
        y: "change",
        fill: "orange",
        dx: -2,
        dy: -1,
        tip: true
    })
  ],
  x: {label: null},
  y: {label: "Percent Change", tickFormat: d => `${d}%`}
})
```
Food prices for all urban consumers (CPI-U) ${describe(latestFood.change)} on a seasonally
adjusted basis, after it ${describe(previousFood.change)} in ${d3.utcFormat("%B")(previousFood.month)}.
```js
Plot.plot({
  title: "One-month percent change in CPI for Food for All Urban Consumers (CPI-U), seasonally adjusted",
  marks: [
    Plot.barY(monthlyFood, {
        x: "month",
        y: "change",
        dx:2,
        dy:2,
        tip: true
    }),
    Plot.barY(monthlyFood, {
        x: "month",
        y: "change",
        fill: "pink",
        dx: -2,
        dy: -1,
        tip: true
    })
  ],
  x: {label: null},
  y: {label: "Percent Change", tickFormat: d => `${d}%`}
})
```
```js
const latest = monthToMonth.at(-1);
const previous = monthToMonth.at(-2);
const describe = (change) => {
  if (change > 0) {
    return `rose ${change} percent`;
  } else if (change < 0) {
    return `fell ${Math.abs(change)} percent`;
  } else {
    return "stayed unchanged";
  }
}
```
```js
const latestFood = monthlyFood.at(-1);
const previousFood = monthlyFood.at(-2);
```
```js
const latestAllItems = yearOverYear.filter(d => d.series_id === "CUUR0000SA0").at(-1);
const latestCore = yearOverYear.filter(d => d.series_id === "CUUR0000SA0L1E").at(-1);
console.log("yearOverYear", yearOverYear)
```

Over the last 12 months, the all items index ${describe(latestAllItems.change)} before seasonal adjustment. The index for all items less food and energy ${describe(latestCore.change)}.

```js
Plot.plot({
  title: " 12-month percent change in CPI for All Urban Consumers (CPI-U), not seasonally adjusted",
  marks: [
    Plot.line(yearOverYear, {
        x: "month",
        y: "change",
        stroke: "series_items_name",
    }),
    Plot.dot(yearOverYear, {x: "month", y: "change", fill: "series_items_name"})
  ],
  color: {legend: true},
  x: {label: null},
  y: {label: "Percent Change", tickFormat: d => `${d}%`, grid: true}
})
```