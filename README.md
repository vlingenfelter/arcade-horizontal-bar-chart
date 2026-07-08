# Making Bar Charts in ArcGIS Dashboards with Arcade

### Overview


### How it works

```
// This generates the little color patch for adding labels to a legend for the bar chart
// String: Hex Code for color -> String: HTML
function makePatch(color) {
  var div = '<span style="font-size:medium; color:' + color +';"> &#9632; </span>'
  return div
}

```
```

// This generates the chunk of color for the bar in the bar chart
// String: Hex color code, String: width (in percentage) -> String: HTML
function makeBarChunk(color, width) {
  var td = '<td style="background-color:' + color + '; width:' + width + '">&nbsp;</td>';
  return td
}

```
```
// This generates the html table that makes the bar chart visual
// Input Array: each item should be an array such that [String: hex color code, Float: width in decimal, String: label]
// Output String: HTML
// Example input: 
// [["maroon", 0.6, "Down"], ["peru", 0.35, "Partial"],  ["grey", 0, "Unknown"],  ["seagreen", 0.05, "Up"]]
function makeBarChart(colorArray) {
  var barHtml = ''
  var labelHtml = ''

  // add the beginning of the table that becomes the bar chart
  barHtml += '<table style="width:100%"><tbody><tr><td><table style="width:100%"><tbody><tr>';

  // add the beginning of the HTML paragraph that becomes the labels
  labelHtml = '<div style="text-align: center"><p style="display: inline-block; text-align: left;">';
  
  // loop through each item in the color array
  // if the value is too small to be meaningful, skip that chunk and that label
  for(var i in colorArray) {
    if (colorArray[i][1] > 0.01) {
      var percent = colorArray[i][1] * 100 + '%'
      barHtml += makeBarChunk(colorArray[i][0], percent);
      labelHtml +=  makePatch(colorArray[i][0]) + percent + " " + colorArray[i][2] + " ";
    }
  }

  // add the end of the table that becomes the bar chart
  barHtml += '<td style="font-size:0px" width="0%">&nbsp;</td></tr></tbody></table></td></tr></tbody></table>'

  // add the end of the paragraph tag that becomes the labels:
  labelHtml += "</p></div>"

  return barHtml + labelHtml
}
```

### Example in the Arcade Playground

