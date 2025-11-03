---
title: Climate Portfolio
layout: default
---

# Climate Portfolio
A compact, interactive portfolio exploring climate indicators across countries and years.

> Data source: inline sample (you can replace/expand later from a CSV or API).  
> Tip: Keep this page focused on *clear questions* and *simple visuals*.

---

## What’s inside
- **Trends**: CO₂ emissions and sea level rise over time  
- **Comparisons**: Average temperature vs rainfall  
- **Trade-offs**: Renewable energy vs CO₂, sized by population  
- **Snapshot**: Mean forest area by country  

---

## Interactive Visualizations

<div id="viz1"></div>
<div id="viz2" style="margin-top:28px"></div>
<div id="viz3" style="margin-top:28px"></div>
<div id="viz4" style="margin-top:28px"></div>
<div id="viz5" style="margin-top:28px"></div>

---

## Data (inline)
<details>
<summary>Click to expand the data used on this page</summary>

| Year | Country | AvgTempC | CO2TonsCapita | SeaLevelMM | RainfallMM | Population | RenewablePct | ExtremeEvents | ForestPct |
|---:|:---|---:|---:|---:|---:|---:|---:|---:|---:|
| 2006 | UK | 8.9 | 9.3 | 3.1 | 1441 | 530911230 | 20.4 | 14 | 59.8 |
| 2019 | USA | 31 | 4.8 | 4.2 | 2407 | 107364344 | 49.2 | 8 | 31 |
| 2014 | France | 33.9 | 2.8 | 2.2 | 1241 | 441101758 | 33.3 | 9 | 35.5 |
| 2010 | Argentina | 5.9 | 1.8 | 3.2 | 1892 | 1069669579 | 23.7 | 7 | 17.7 |
| 2007 | Germany | 26.9 | 5.6 | 2.4 | 1743 | 124079175 | 12.5 | 4 | 17.4 |
| 2020 | China | 32.3 | 1.4 | 2.7 | 2100 | 1202028857 | 49.4 | 12 | 47.2 |
| 2006 | Argentina | 30.7 | 11.6 | 3.9 | 1755 | 586706107 | 41.9 | 10 | 50.5 |
| 2018 | South Africa | 33.9 | 6 | 4.5 | 827 | 83947380 | 17.7 | 1 | 56.6 |
| 2022 | UK | 27.8 | 16.6 | 1.5 | 1966 | 980305187 | 8.2 | 4 | 43.4 |
| 2010 | Australia | 18.3 | 1.9 | 3.5 | 2599 | 849496137 | 7.5 | 5 | 48.7 |
| 2010 | Indonesia | 14.4 | 4.9 | 3.3 | 2466 | 1345958932 | 10.4 | 13 | 10.8 |
| 2023 | South Africa | 14.6 | 14.7 | 1.5 | 875 | 1311714067 | 31.1 | 14 | 19.7 |
| 2020 | Brazil | 26.7 | 12.2 | 1.8 | 762 | 1106863791 | 46.8 | 0 | 69.1 |
| 2003 | USA | 27.4 | 1.8 | 2.2 | 766 | 798905927 | 27.4 | 10 | 14.6 |
| 2007 | Germany | 11 | 17.6 | 1.7 | 2906 | 1179622755 | 42.9 | 3 | 50.7 |
| 2023 | India | 31 | 17.6 | 2 | 2444 | 365927452 | 25.1 | 8 | 20.6 |
| 2002 | Russia | 26.4 | 6.9 | 2.1 | 2393 | 29715260 | 36.9 | 1 | 53 |
| 2021 | France | 21.9 | 7.1 | 2.3 | 2401 | 680925261 | 37.9 | 4 | 52.4 |
| 2020 | China | 19.1 | 19.8 | 4 | 2152 | 1074542897 | 12.8 | 0 | 24.1 |
| 2001 | Canada | 21.3 | 12.7 | 3.3 | 1944 | 1189649940 | 21.3 | 3 | 26.2 |
| 2023 | France | 9.8 | 10.9 | 4.9 | 545 | 1311466901 | 33.7 | 3 | 49.3 |
| 2011 | Mexico | 11.1 | 16.8 | 2.7 | 1099 | 250843348 | 8.2 | 5 | 33.4 |
| 2005 | India | 7.7 | 6.1 | 4 | 1219 | 588745515 | 21.3 | 9 | 21.7 |
| 2001 | Indonesia | 9.6 | 8.9 | 4.5 | 2412 | 300442653 | 29.1 | 8 | 53.9 |
| 2020 | Russia | 18.6 | 5.7 | 3.7 | 1792 | 11186886 | 49.8 | 2 | 19.2 |
| 2000 | India | 20.9 | 15.9 | 2.6 | 1100 | 1237433800 | 26.4 | 8 | 61.3 |

</details>

---

## Reproduce with R
You can generate similar charts in R (ggplot2). This page renders them in-browser using Vega-Lite for portability on GitHub Pages.

---

<!-- Vega & Vega-Lite -->
<script src="https://cdn.jsdelivr.net/npm/vega@5"></script>
<script src="https://cdn.jsdelivr.net/npm/vega-lite@5"></script>
<script src="https://cdn.jsdelivr.net/npm/vega-embed@6"></script>

<script>
const data = [
  {Year:2006,Country:"UK",AvgTempC:8.9,CO2TonsCapita:9.3,SeaLevelMM:3.1,RainfallMM:1441,Population:530911230,RenewablePct:20.4,ExtremeEvents:14,ForestPct:59.8},
  {Year:2019,Country:"USA",AvgTempC:31,CO2TonsCapita:4.8,SeaLevelMM:4.2,RainfallMM:2407,Population:107364344,RenewablePct:49.2,ExtremeEvents:8,ForestPct:31},
  {Year:2014,Country:"France",AvgTempC:33.9,CO2TonsCapita:2.8,SeaLevelMM:2.2,RainfallMM:1241,Population:441101758,RenewablePct:33.3,ExtremeEvents:9,ForestPct:35.5},
  {Year:2010,Country:"Argentina",AvgTempC:5.9,CO2TonsCapita:1.8,SeaLevelMM:3.2,RainfallMM:1892,Population:1069669579,RenewablePct:23.7,ExtremeEvents:7,ForestPct:17.7},
  {Year:2007,Country:"Germany",AvgTempC:26.9,CO2TonsCapita:5.6,SeaLevelMM:2.4,RainfallMM:1743,Population:124079175,RenewablePct:12.5,ExtremeEvents:4,ForestPct:17.4},
  {Year:2020,Country:"China",AvgTempC:32.3,CO2TonsCapita:1.4,SeaLevelMM:2.7,RainfallMM:2100,Population:1202028857,RenewablePct:49.4,ExtremeEvents:12,ForestPct:47.2},
  {Year:2006,Country:"Argentina",AvgTempC:30.7,CO2TonsCapita:11.6,SeaLevelMM:3.9,RainfallMM:1755,Population:586706107,RenewablePct:41.9,ExtremeEvents:10,ForestPct:50.5},
  {Year:2018,Country:"South Africa",AvgTempC:33.9,CO2TonsCapita:6,SeaLevelMM:4.5,RainfallMM:827,Population:83947380,RenewablePct:17.7,ExtremeEvents:1,ForestPct:56.6},
  {Year:2022,Country:"UK",AvgTempC:27.8,CO2TonsCapita:16.6,SeaLevelMM:1.5,RainfallMM:1966,Population:980305187,RenewablePct:8.2,ExtremeEvents:4,ForestPct:43.4},
  {Year:2010,Country:"Australia",AvgTempC:18.3,CO2TonsCapita:1.9,SeaLevelMM:3.5,RainfallMM:2599,Population:849496137,RenewablePct:7.5,ExtremeEvents:5,ForestPct:48.7},
  {Year:2010,Country:"Indonesia",AvgTempC:14.4,CO2TonsCapita:4.9,SeaLevelMM:3.3,RainfallMM:2466,Population:1345958932,RenewablePct:10.4,ExtremeEvents:13,ForestPct:10.8},
  {Year:2023,Country:"South Africa",AvgTempC:14.6,CO2TonsCapita:14.7,SeaLevelMM:1.5,RainfallMM:875,Population:1311714067,RenewablePct:31.1,ExtremeEvents:14,ForestPct:19.7},
  {Year:2020,Country:"Brazil",AvgTempC:26.7,CO2TonsCapita:12.2,SeaLevelMM:1.8,RainfallMM:762,Population:1106863791,RenewablePct:46.8,ExtremeEvents:0,ForestPct:69.1},
  {Year:2003,Country:"USA",AvgTempC:27.4,CO2TonsCapita:1.8,SeaLevelMM:2.2,RainfallMM:766,Population:798905927,RenewablePct:27.4,ExtremeEvents:10,ForestPct:14.6},
  {Year:2007,Country:"Germany",AvgTempC:11,CO2TonsCapita:17.6,SeaLevelMM:1.7,RainfallMM:2906,Population:1179622755,RenewablePct:42.9,ExtremeEvents:3,ForestPct:50.7},
  {Year:2023,Country:"India",AvgTempC:31,CO2TonsCapita:17.6,SeaLevelMM:2,RainfallMM:2444,Population:365927452,RenewablePct:25.1,ExtremeEvents:8,ForestPct:20.6},
  {Year:2002,Country:"Russia",AvgTempC:26.4,CO2TonsCapita:6.9,SeaLevelMM:2.1,RainfallMM:2393,Population:29715260,RenewablePct:36.9,ExtremeEvents:1,ForestPct:53},
  {Year:2021,Country:"France",AvgTempC:21.9,CO2TonsCapita:7.1,SeaLevelMM:2.3,RainfallMM:2401,Population:680925261,RenewablePct:37.9,ExtremeEvents:4,ForestPct:52.4},
  {Year:2020,Country:"China",AvgTempC:19.1,CO2TonsCapita:19.8,SeaLevelMM:4,RainfallMM:2152,Population:1074542897,RenewablePct:12.8,ExtremeEvents:0,ForestPct:24.1},
  {Year:2001,Country:"Canada",AvgTempC:21.3,CO2TonsCapita:12.7,SeaLevelMM:3.3,RainfallMM:1944,Population:1189649940,RenewablePct:21.3,ExtremeEvents:3,ForestPct:26.2},
  {Year:2023,Country:"France",AvgTempC:9.8,CO2TonsCapita:10.9,SeaLevelMM:4.9,RainfallMM:545,Population:1311466901,RenewablePct:33.7,ExtremeEvents:3,ForestPct:49.3},
  {Year:2011,Country:"Mexico",AvgTempC:11.1,CO2TonsCapita:16.8,SeaLevelMM:2.7,RainfallMM:1099,Population:250843348,RenewablePct:8.2,ExtremeEvents:5,ForestPct:33.4},
  {Year:2005,Country:"India",AvgTempC:7.7,CO2TonsCapita:6.1,SeaLevelMM:4,RainfallMM:1219,Population:588745515,RenewablePct:21.3,ExtremeEvents:9,ForestPct:21.7},
  {Year:2001,Country:"Indonesia",AvgTempC:9.6,CO2TonsCapita:8.9,SeaLevelMM:4.5,RainfallMM:2412,Population:300442653,RenewablePct:29.1,ExtremeEvents:8,ForestPct:53.9},
  {Year:2020,Country:"Russia",AvgTempC:18.6,CO2TonsCapita:5.7,SeaLevelMM:3.7,RainfallMM:1792,Population:11186886,RenewablePct:49.8,ExtremeEvents:2,ForestPct:19.2},
  {Year:2000,Country:"India",AvgTempC:20.9,CO2TonsCapita:15.9,SeaLevelMM:2.6,RainfallMM:1100,Population:1237433800,RenewablePct:26.4,ExtremeEvents:8,ForestPct:61.3}
];

// 1) CO2 over time by Country (line)
vegaEmbed('#viz1', {
  $schema: 'https://vega.github.io/schema/vega-lite/v5.json',
  description: 'CO2 over time by country',
  data: { values: data },
  mark: { type: 'line', point: true },
  encoding: {
    x: { field: 'Year', type: 'quantitative' },
    y: { field: 'CO2TonsCapita', type: 'quantitative', title: 'CO₂ tons/capita' },
    color: { field: 'Country', type: 'nominal' },
    tooltip: [
      {field:'Country', type:'nominal'},
      {field:'Year', type:'quantitative'},
      {field:'CO2TonsCapita', type:'quantitative'}
    ]
  },
  width: 'container',
  height: 320,
  title: { text: 'CO₂ Emissions Over Time by Country', fontWeight: 'bold' }
});

// 2) Rainfall vs Temperature (scatter + trend)
vegaEmbed('#viz2', {
  $schema: 'https://vega.github.io/schema/vega-lite/v5.json',
  data: { values: data },
  layer: [
    {
      mark: { type:'point', filled:true },
      encoding: {
        x: { field: 'RainfallMM', type: 'quantitative', title: 'Rainfall (mm)' },
        y: { field: 'AvgTempC', type: 'quantitative', title: 'Average Temperature (°C)' },
        color: { field: 'Country', type: 'nominal' },
        tooltip: [
          {field:'Country', type:'nominal'},
          {field:'Year', type:'quantitative'},
          {field:'RainfallMM', type:'quantitative'},
          {field:'AvgTempC', type:'quantitative'}
        ]
      }
    },
    {
      mark: { type:'regression', color:'#2E86AB' },
      encoding: {
        x: { field: 'RainfallMM', type: 'quantitative' },
        y: { field: 'AvgTempC', type: 'quantitative' }
      }
    }
  ],
  width: 'container',
  height: 320,
  title: { text: 'Rainfall vs Temperature', fontWeight: 'bold' }
});

// 3) Renewable vs CO2 (bubble sized by population)
vegaEmbed('#viz3', {
  $schema: 'https://vega.github.io/schema/vega-lite/v5.json',
  data: { values: data },
  mark: { type: 'point', filled: true },
  encoding: {
    x: { field: 'RenewablePct', type: 'quantitative', title: 'Renewable Energy (%)' },
    y: { field: 'CO2TonsCapita', type: 'quantitative', title: 'CO₂ tons/capita' },
    size: { field: 'Population', type: 'quantitative', legend: { title: 'Population' } },
    color: { field: 'Country', type: 'nominal' },
    tooltip: [
      {field:'Country', type:'nominal'},
      {field:'Year', type:'quantitative'},
      {field:'RenewablePct', type:'quantitative'},
      {field:'CO2TonsCapita', type:'quantitative'},
      {field:'Population', type:'quantitative'}
    ]
  },
  width: 'container',
  height: 320,
  title: { text: 'Renewables vs CO₂ (bubble = population)', fontWeight: 'bold' }
});

// 4) Sea Level Rise over time (line, per country)
vegaEmbed('#viz4', {
  $schema: 'https://vega.github.io/schema/vega-lite/v5.json',
  data: { values: data },
  mark: { type: 'line', point: true },
  encoding: {
    x: { field: 'Year', type: 'quantitative' },
    y: { field: 'SeaLevelMM', type: 'quantitative', title: 'Sea Level Rise (mm)' },
    color: { field: 'Country', type: 'nominal' },
    tooltip: [
      {field:'Country', type:'nominal'},
      {field:'Year', type:'quantitative'},
      {field:'SeaLevelMM', type:'quantitative'}
    ]
  },
  width: 'container',
  height: 300,
  title: { text: 'Sea Level Rise Over Time by Country', fontWeight: 'bold' }
});

// 5) Mean Forest Area by Country (bar)
vegaEmbed('#viz5', {
  $schema: 'https://vega.github.io/schema/vega-lite/v5.json',
  data: { values: data },
  transform: [
    { aggregate: [{ op: 'mean', field: 'ForestPct', as: 'MeanForest' }], groupby: ['Country'] }
  ],
  mark: { type: 'bar', tooltip: true },
  encoding: {
    x: { field: 'Country', type: 'nominal', sort: '-y' },
    y: { field: 'MeanForest', type: 'quantitative', title: 'Mean Forest Area (%)' },
    color: { field: 'Country', type: 'nominal', legend: null },
    tooltip: [
      {field:'Country', type:'nominal'},
      {field:'MeanForest', type:'quantitative'}
    ]
  },
  width: 'container',
  height: 320,
  title: { text: 'Mean Forest Area by Country', fontWeight: 'bold' }
});
</script>

