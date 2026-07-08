# Making Bar Charts in ArcGIS Dashboards with Arcade

Generate lightweight horizontal bar charts using only **Arcade** and **HTML**. These charts work in ArcGIS Dashboards, web map popups, lists, tables, and anywhere Arcade expressions can return HTML.

## Overview

Arcade doesn't include built-in charting capabilities, but you can create simple stacked bar charts by generating HTML tables with colored cells.

This repository provides a reusable `makeBarChart()` function that:

* Creates horizontal stacked bar charts from arrays of values.
* Automatically converts whole numbers to percentages (optional).
* Omits very small segments to keep charts readable.
* Generates a matching legend beneath each chart.
* Works anywhere Arcade supports HTML output.

---

## How It Works

The chart is built from a few small helper functions.

### `makePatch(color)`

Creates a colored square used in the chart legend.

**Input**

* `color` *(String)* — HTML color name or hexadecimal color code.

**Returns**

* HTML string

```javascript
function makePatch(color) {
    var div = '<span style="font-size:medium; color:' + color + ';"> &#9632; </span>';
    return div;
}
```

---

### `makeBarChunk(color, width)`

Creates a single colored segment of the stacked bar.

**Inputs**

| Parameter | Type   | Description                                 |
| --------- | ------ | ------------------------------------------- |
| `color`   | String | HTML color or hex code                      |
| `width`   | String | Width as a percentage (for example `"35%"`) |

**Returns**

* HTML string

```javascript
function makeBarChunk(color, width) {
    var td = '<td style="background-color:' + color + '; width:' + width + '">&nbsp;</td>';
    return td;
}
```

---

### `convertArrayToDecimal(array)`

Converts an array of counts into decimal proportions that sum to 1.

For example:

```text
[50, 30, 20]
```

becomes

```text
[0.5, 0.3, 0.2]
```

Use this when your input values are counts instead of percentages.

```javascript
function convertArrayToDecimal(array) {
    var decimalArray = [];
    var denominator = Sum(array);

    for (var i in array) {
        decimalArray[i] = array[i] / denominator;
    }

    return decimalArray;
}
```

---

### `makeBarChart(colors, values, labels, convert)`

Generates the complete bar chart and legend.

If `convert` is `True`, the values are first converted into percentages using `convertArrayToDecimal()`.

### Parameters

| Parameter | Type    | Description                     |
| --------- | ------- | ------------------------------- |
| `colors`  | Array   | Color for each category         |
| `values`  | Array   | Counts or decimal percentages   |
| `labels`  | Array   | Label for each category         |
| `convert` | Boolean | Convert counts into percentages |

### Returns

A string containing HTML that can be returned from an Arcade expression.

Example input:

```javascript
colors = ["maroon", "peru", "grey", "seagreen"]

values = [60, 35, 0, 5]

labels = ["Down", "Partial", "Unknown", "Up"]
```

or, if already normalized:

```javascript
values = [0.60, 0.35, 0.00, 0.05]
```

The function automatically:

* Converts counts to percentages (optional)
* Skips segments smaller than 1%
* Creates the stacked bar
* Builds a matching legend

---

# Examples

## Web Map Popup

The following Arcade expression creates a stacked bar chart showing the distribution of fruit counts.

![Bar chart popup in a web map](popup/popup_with_bar.png)

```javascript
var featureArray = [$feature.Apples, $feature.Bananas, $feature.Kiwis, $feature.Oranges ];
var colorArray = ["maroon", "yellow", "green", "orange"];
var labelArray = ["Apples", "Bananas", "Kiwis", "Oranges" ];

var chart = makeBarChart(
    colorArray,
    featureArray,
    labelArray,
    True
);

return {
    type: "text",
    text: chart
};
```

---

## ArcGIS Dashboards

### Table Cells

The chart can be returned from an Arcade expression and displayed directly inside dashboard table cells.

![Bar chart element in a table](dashboard/dashboard_table_example.png)

---

### Lists

The same function also works inside Dashboard List elements.

![Bar chart element in a list](dashboard/dashboard_list_example.png)

---

## Notes

* Values should either be counts or decimal percentages that sum to **1.0**.
* Segments smaller than **1%** are hidden to improve readability.
* Colors can be HTML color names (for example, `"red"` or `"seagreen"`) or hexadecimal values (for example, `"#2E8B57"`).
* Since the output is pure HTML, it works anywhere Arcade supports HTML text output.
