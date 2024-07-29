# ActBlue Ticker Tracker

```js
const timeFrame = view(Inputs.radio(["Daily", "Weekly"], {
  label: "Select time frame",
  value: "Weekly"
}));
```

```js
Plot.plot({
  width,
  height,
  marginLeft: 50,
  x: {
    round: true,
    label: null,
    type: "time"
  },
  y: {
    grid: true,
    tickFormat(d, i, values) {
      const s = d / 1e6;
      return i + 1 < values.length ? `\xa0${s}M` : `$${s}M`;
    },
    label: `↑ ${timeFrame} amount raised`
  },
  marks: [
    Plot.rectY(
      data,
      Plot.binX(
        {y: "sum"},
        {
          x: "timestamp",
          y: "difference",
          thresholds: timeFrame === "Weekly" ? d3.timeWeek : d3.timeDay,
          fill: "#00a9e0",
          inset: 0
        }
      )
    ),
    Plot.ruleY([0])
  ]
})
```

```js
const height = width < 576 ? 300 : 500;
```

```js
const data = FileAttachment("amounts.csv").csv().then((data) => {
  return data.map((d, i) => {
    return {
      amount: Number(d.amount),
      timestamp: new Date(d.timestamp * 1000),
      difference: i < data.length - 1 ? d.amount - data[i + 1].amount : null
    };
  });
});
```
