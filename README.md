# Vega_lite
Introduction to Vega-Lite
This tutorial will guide through the process of writing a visualization specification in Vega-Lite. We will walk you through all main components of Vega-Lite by adding each of them to an example specification one-by-one. After creating the example visualization, we will also guide you how to embed the final visualization on a web page.

We suggest that you follow along the tutorial by building a visualization in the online editor. Extend your specification in the editor as you read through this tutorial. If something does not work as expected, compare your specifications with ones inside this tutorial.

Tutorial Overview
Tutorial Overview
The Data
Encoding Data with Marks
Data Transformation: Aggregation
Customize your Visualization
Publish your Visualization Online
Next Steps
The Data
Let’s say you have a tabular data set with a categorical variable in the first column a and a numerical variable in the second column b.

a	b
C	2
C	7
C	4
D	1
D	2
D	6
E	8
E	4
E	7
We can represent this data as a JSON array in which each row is an object in the array.

[
  {"a": "C", "b": 2},
  {"a": "C", "b": 7},
  {"a": "C", "b": 4},
  {"a": "D", "b": 1},
  {"a": "D", "b": 2},
  {"a": "D", "b": 6},
  {"a": "E", "b": 8},
  {"a": "E", "b": 4},
  {"a": "E", "b": 7}
]
To visualize this data with Vega-Lite, we can add it directly to the data property in a Vega-Lite specification. (Note that we reformatted the array to make it more compact.)

{
  "data": {
    "values": [
      {"a": "C", "b": 2}, {"a": "C", "b": 7}, {"a": "C", "b": 4},
      {"a": "D", "b": 1}, {"a": "D", "b": 2}, {"a": "D", "b": 6},
      {"a": "E", "b": 8}, {"a": "E", "b": 4}, {"a": "E", "b": 7}
    ]
  }
}
The data property defines the data source of the visualization. In this example, we embed the data inline by directly setting values property. Vega-Lite also supports other types of data sources besides inline data.

Encoding Data with Marks
Now we have a data source but we haven’t defined yet how the data should be visualized.

Basic graphical elements in Vega-Lite are marks. Marks provide basic shapes whose properties (such as position, size, and color) can be used to visually encode data, either from a data field (or a variable), or a constant value.

To show the data as a point, we can set the mark property to point.

Open in Vega Editor
{
  "data": {
    "values": [
      {"a": "C", "b": 2}, {"a": "C", "b": 7}, {"a": "C", "b": 4},
      {"a": "D", "b": 1}, {"a": "D", "b": 2}, {"a": "D", "b": 6},
      {"a": "E", "b": 8}, {"a": "E", "b": 4}, {"a": "E", "b": 7}
    ]
  },
  "mark": "point",
  "encoding": {}
}
Now, it looks like we get a point. In fact, Vega-Lite renders one point for each object in the array, but they are all overlapping since we have not specified each point’s position.

To visually separate the points, data variables can be mapped to visual properties of a mark. For example, we can encode the variable a of the data with x channel, which represents the x-position of the points. We can do that by adding an encoding object with its key x mapped to a channel definition that describes variable a.

...
"encoding": {
  "x": {"field": "a", "type": "nominal"}
}
...
C
D
E
a
Open in Vega Editor
{
  "data": {
    "values": [
      {"a": "C", "b": 2}, {"a": "C", "b": 7}, {"a": "C", "b": 4},
      {"a": "D", "b": 1}, {"a": "D", "b": 2}, {"a": "D", "b": 6},
      {"a": "E", "b": 8}, {"a": "E", "b": 4}, {"a": "E", "b": 7}
    ]
  },
  "mark": "point",
  "encoding": {
    "x": {"field": "a", "type": "nominal"}
  }
}
The encoding object is a key-value mapping between encoding channels (such as x, y) and definitions of the mapped data fields. The channel definition describes the field’s name (field) and its data type (type). In this example, we map the values for field a to the encoding channel x (the x-location of the points) and set a’s data type to nominal, since it represents categories. (See the documentation for more information about data types.)

In the visualization above, Vega-Lite automatically adds an axis with labels for the different categories as well as an axis title. However, 3 points in each category are still overlapping. So far, we have only defined a visual encoding for the field a. We can also map the field b to the y channel.

...
"y": {"field": "b", "type": "quantitative"}
...
This time we set the field type to be quantitative because the values in field b are numeric.

C
D
E
a
0
2
4
6
8
b
Open in Vega Editor
{
  "data": {
    "values": [
      {"a": "C", "b": 2}, {"a": "C", "b": 7}, {"a": "C", "b": 4},
      {"a": "D", "b": 1}, {"a": "D", "b": 2}, {"a": "D", "b": 6},
      {"a": "E", "b": 8}, {"a": "E", "b": 4}, {"a": "E", "b": 7}
    ]
  },
  "mark": "point",
  "encoding": {
    "x": {"field": "a", "type": "nominal"},
    "y": {"field": "b", "type": "quantitative"}
  }
}
Now we can see the raw data points. Note that Vega-Lite automatically adds grid lines to the y-axis to facilitate comparison of the b values.

Data Transformation: Aggregation
Vega-Lite also supports data transformation such as aggregation. By adding "aggregate": "average" to the definition of the y channel, we can see the average value of a in each category. For example, the average value of category D is (1 + 2 + 6)/3 = 9/3 = 3.

C
D
E
a
0
1
2
3
4
5
6
Average of b
Open in Vega Editor
{
  "data": {
    "values": [
      {"a": "C", "b": 2}, {"a": "C", "b": 7}, {"a": "C", "b": 4},
      {"a": "D", "b": 1}, {"a": "D", "b": 2}, {"a": "D", "b": 6},
      {"a": "E", "b": 8}, {"a": "E", "b": 4}, {"a": "E", "b": 7}
    ]
  },
  "mark": "point",
  "encoding": {
    "x": {"field": "a", "type": "nominal"},
    "y": {"aggregate": "average", "field": "b", "type": "quantitative"}
  }
}
Great! You computed the aggregate values for each category and visualized the resulting value as a point. Typically aggregated values for categories are visualized using bar charts. To create a bar chart, we have to change the mark type from point to bar.

- "mark": "point"
+ "mark": "bar"
C
D
E
a
0
1
2
3
4
5
6
Average of b
Open in Vega Editor
{
  "data": {
    "values": [
      {"a": "C", "b": 2}, {"a": "C", "b": 7}, {"a": "C", "b": 4},
      {"a": "D", "b": 1}, {"a": "D", "b": 2}, {"a": "D", "b": 6},
      {"a": "E", "b": 8}, {"a": "E", "b": 4}, {"a": "E", "b": 7}
    ]
  },
  "mark": "bar",
  "encoding": {
    "x": {"field": "a", "type": "nominal"},
    "y": {"aggregate": "average", "field": "b", "type": "quantitative"}
  }
}
Since the quantitative value is on y, you automatically get a vertical bar chart. If we swap the x and y channel, we get a horizontal bar chart instead.

0
1
2
3
4
5
6
Average of b
C
D
E
a
Open in Vega Editor
{
  "data": {
    "values": [
      {"a": "C", "b": 2}, {"a": "C", "b": 7}, {"a": "C", "b": 4},
      {"a": "D", "b": 1}, {"a": "D", "b": 2}, {"a": "D", "b": 6},
      {"a": "E", "b": 8}, {"a": "E", "b": 4}, {"a": "E", "b": 7}
    ]
  },
  "mark": "bar",
  "encoding": {
    "y": {"field": "a", "type": "nominal"},
    "x": {"aggregate": "average", "field": "b", "type": "quantitative"}
  }
}
Customize your Visualization
Vega-Lite automatically provides default properties for the visualization. You can further customize these values by adding more properties. For example, to change the title of the x-axis from MEAN(b) to Average of b, we can set the title property of the axis in the x channel.

0
1
2
3
4
5
6
Average of b
C
D
E
a
Open in Vega Editor
{
  "data": {
    "values": [
      {"a": "C", "b": 2}, {"a": "C", "b": 7}, {"a": "C", "b": 4},
      {"a": "D", "b": 1}, {"a": "D", "b": 2}, {"a": "D", "b": 6},
      {"a": "E", "b": 8}, {"a": "E", "b": 4}, {"a": "E", "b": 7}
    ]
  },
  "mark": "bar",
  "encoding": {
    "y": {"field": "a", "type": "nominal"},
    "x": {
      "aggregate": "average", "field": "b", "type": "quantitative",
      "axis": {"title": "Average of b"}
    }
  }
}
Publish your Visualization Online
You have learned about basic components of a Vega-Lite specification. Now, let’s see how to publish your visualization.

You can use Vega-Embed to embed your Vega-Lite visualization in a webpage. For example, you can create a web page with the following content:

<!DOCTYPE html>
<head>
  <title>Vega Lite Bar Chart</title>
  <meta charset="utf-8">

  <script src="https://cdn.jsdelivr.net/npm/vega@4.3.0/build/vega.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/vega-lite@3.0.0-rc8/build/vega-lite.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/vega-embed@3.20.0/build/vega-embed.js"></script>

  <style media="screen">
    /* Add space between Vega-Embed links  */
    .vega-actions a {
      margin-right: 5px;
    }
  </style>
</head>
<body>
  <h1>Template for Embedding Vega-Lite Visualization</h1>
  <!-- Container for the visualization -->
  <div id="vis"></div>

  <script>
  // Assign the specification to a local variable vlSpec.
  var vlSpec = {
    "$schema": "https://vega.github.io/schema/vega-lite/v3.json",
    "data": {
      "values": [
        {"a": "C", "b": 2}, {"a": "C", "b": 7}, {"a": "C", "b": 4},
        {"a": "D", "b": 1}, {"a": "D", "b": 2}, {"a": "D", "b": 6},
        {"a": "E", "b": 8}, {"a": "E", "b": 4}, {"a": "E", "b": 7}
      ]
    },
    "mark": "bar",
    "encoding": {
      "y": {"field": "a", "type": "nominal"},
      "x": {
        "aggregate": "average", "field": "b", "type": "quantitative",
        "axis": {
          "title": "Average of b"
        }
      }
    }
  };

  // Embed the visualization in the container with id `vis`
  vegaEmbed("#vis", vlSpec);
  </script>
</body>
</html>
In this webpage, we first load the dependencies for Vega-Lite (Vega-Embed, Vega, and Vega-Lite) in the <head/> tag of the document. We also create an HTML <div/> element with id vis to serve as a container for the visualization.

In the JavaScript code, we create a variable vlSpec that holds the Vega-Lite specification in JSON format. The vl.embed method translates a Vega-Lite specification into a Vega specification and then calls the Vega Runtime to display visualization in the container <div/> element.

If viewed in a browser, this page displays our bar chart like on our demo page. You can also fork our Vega-Lite Block example.
