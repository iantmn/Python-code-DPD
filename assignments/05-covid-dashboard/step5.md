---
layout: default
title: Step 5 Dictionary
parent: '05 COVID Dashboard'
---

# Step 5 Dictionary

{: .no_toc }

## Table of contents

{: .no_toc .text-delta }

1. TOC
   {:toc}

---

We walked through the full cycle of setting up a visualisation, downloading data to inject it in the visualisation and render this visualisation as part of an HTML/CSS/JavaScript dashboard. In this step we repeat these steps to add two additional charts.

# Task 5.1 Display Netherlands History

This time we want an area chart mapping the case history in the Netherlands. First, we have the template, which includes the same elements as the previous chart. This time we look for an area chart

```json
{
  "$schema": "https://vega.github.io/schema/vega-lite/v5.json",
  "width": "container",
  "title": "Confirmed Cases",
  "data": {
    "values": []
  },
  "mark": {
    "type": "area",
    "tooltip": true
  },
  "encoding": {
    "x": {
      "field": "Date",
      "type": "temporal"
    },
    "y": {
      "field": "Cases",
      "type": "quantitative"
    },
    "color": {
      "field": "Province"
    }
  }
}
```

The process of combining template and data is the same as in Step 3, with a different template and different pick at the data.

```python
    # Load json template from history.json
    # Download the confirmed cases of the netherlands from the COVID API
    # Add the data (key "data") to the template (key "data" > "values")
    # Send the chart description to the client
```

Run the code and trigger the route `/netherlands` to see the template together with the data.

TODO Replit code here

In `static/index.html`, we can add the call to `vegaEmbed` at the bottom, next to existing `vegaEmbed` from the first chart.

```js
vegaEmbed('#netherlands', '/netherlands');
```

A styling details, as the chart is wide it would fit better on the full length of the page. We could add an id to the second section so that we can apply some CSS on that specific element. Another strategy is to add the class property. While an id must be unique, the class can be apply to many elements. As we might want to create several charts for other countries to compare, the class seems an appropriate choice. Add property `class="country"` to the second section. Then, in the CSS, add the following to increase the size of the grid for this particular section. Note the dot `.` in contrast with the hashtag `#` to target the name of a class instead of an id.

```css
.country {
  grid-template-columns: repeat(auto-fill, minmax(600px, 1fr));
  text-align: center;
}
```

TODO screenshot of results

# Task 5.2 Display New Cases

For the new cases, we use once again a similar vega template, this time with arc to represent a pie chart.

```json
{
  "$schema": "https://vega.github.io/schema/vega-lite/v5.json",
  "width": "container",
  "title": "New cases",
  "data": {
    "values": []
  },
  "mark": {
    "type": "arc",
    "innerRadius": 40,
    "tooltip": true
  },
  "encoding": {
    "theta": {
      "field": "value",
      "type": "quantitative"
    },
    "color": {
      "field": "category",
      "type": "nominal"
    }
  },
  "view": {
    "stroke": null
  }
}
```

The data regarding the new cases is part of the `/summary` API along the details of each countries. The new cases are specifically in the key `Global`. This is an opportunity to see how to loop through a `dict`. In the following example, we see the construction `for key in json_data["Global"]` which means we loop through all the keys as par of the nested `dict` 'Global'. This gives us the ability to filter keys that start with 'New',including 'NewConfirmed', 'NewDeaths' and 'NewRecovered'.

The function `serve_summary_new()` looks as follows:

```python
# Load json template from new.json
  json_template = json.load(open("templates/new.json"))
  # Download summary from the COVID API
  json_data = download_summary()
  # Create an empty list of value to receive filtered and formated data
  values = []
  # For all data in the "Global" structure
  for key in json_data["Global"]:
    # Keep the 'New' entries (skip overall totals)
    if key.startswith("New"):
      # Create a dictionary with 'category' and 'value'
      value = {"category": key, "value": json_data["Global"][key]}
      # Add the dictionary to the list of values
      values.append(value)
  # Add the data to the template
  json_template["data"]["values"] = values
  # Send the chart description to the client
  return json_template
```

Run the code and trigger the route `/new` to see the template together with the data.

TODO Replit code here

In `static/index.html`, we can add the call to `vegaEmbed` at the bottom, next to existing `vegaEmbed` from the first chart.

```js
vegaEmbed('#new', '/new');
```

TODO screenshot of results

[Check the code on Replit](https://repl.it/@IO1075/step5)

[Next: Step 6 - Recap and More]({{site.baseurl}}/assignments/05-covid-dashboard/step6){: .btn .btn-purple }