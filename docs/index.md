# ActBlue Ticker Tracker

```js
Plot.plot({
  title: "Contributions to ActBlue since Sept. 2020",
  width,
  height,
  marginLeft: 50,
  x: {
    round: true,
    label: timeFrame === "Weekly" ? "week" : "day",
    type: "time"
  },
  y: {
    grid: true,
    label: "amount"
  },
  marks: [
    Plot.axisX({label: null}),
    Plot.axisY({
      label: `↑ ${timeFrame} amount raised`,
      tickFormat(d, i, values) {
        const s = d / 1e6;
        return i + 1 < values.length ? `\xa0${s}M` : `$${s}M`;
      }
    }),
    Plot.rectY(
      data,
      Plot.binX(
        {y: "sum"},
        {
          x: "timestamp",
          y: "difference",
          thresholds: timeFrame === "Weekly" ? "week" : "day",
          fill: "#00a9e0",
          inset: 0,
          tip: {
            format: {
              x2: null,
              y: "$,"
            }
          }
        }
      )
    ),
    Plot.ruleY([0])
  ]
})
```

```js
const timeFrame = view(
  Inputs.radio(["Daily", "Weekly"], {
    label: "Time period:",
    value: "Weekly"
  })
);
```

```js
const height = width < 576 ? 300 : 500;
```

```js
const data = FileAttachment("amounts.csv")
  .csv()
  .then((data) => {
    return data.map((d, i) => {
      return {
        amount: Number(d.amount),
        timestamp: new Date(d.timestamp * 1000),
        difference: i < data.length - 1 ? d.amount - data[i + 1].amount : null
      };
    });
  });
```
