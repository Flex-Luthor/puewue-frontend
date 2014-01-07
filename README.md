# Power Dashboard

## Getting Started

1. Connect the dashboard to your API, by providing your server and metric settings (see **Configuration**)
2. Test your connect with a local HTTP server, e.g. `SimpleHTTPServer`.

## Configuration

### PowerDashboard

All exposed configuration is sent when instantiating `PowerDashboard`.

An example of how to instantiate this is available in `assets/javascripts/application.js`

```
{
	wheelGraphEndpointAlias: '24-hours', // See 'API notes > URL structure' for more details
	wheelGraph: true, // Set to 'false' to disable the wheel graph
	histograms: true, // Set to 'false' to disable the set of histograms
	apiConfig: {
		// This is your host. No trailing slash
		host: 'http://localhost:3000'

		// This is an optional prefix for the URI, with no leading or trailing slash
		// See 'API notes > URL structure' for more details
		uriPrefix: 'my/directory'
	},
	metrics: [
		{
			// This references a key in your API responses (see API notes)
			alias: 'humidity',


			name: 'Humidity',

			// By default, the range of data will use the min and max values from the points provided
			// ... `minDomainDifference` can compliment this by ensuring there's a minimum range
			minDomainDifference: 0.05
		},
		{
			alias: 'pue',
			name: 'PUE',

			// You can also define hard boundaries using `domain`
			domain: {
				min: 1.0,
				max: 1.2
			}
		}
		...
	]
}```

### Markup configuration

#### Accordion metrics
+ **DOM reference**: `.metric-display`
+ **Description**: This should reference one of your available metrics, as part of the overall group. These will display in an accordion-style UI.
+ **data- attributes**:
	+ **metric="your-metric-alias"**: This should match PowerDashboard configuration
	+ **view-mode="temperature"** or **data-view-mode="percentage"**: if one of your metrics is based around percentage or temperature, you can utilise these view modes to renders the values accordingly.

*4 example histograms are available in the `index.html` boilerplate.*

#### Date range filters
+ **DOM reference**: `.date-range-nav a`
+ **Description**: Its purpose is to pass a specific date range into the histogram and refresh the data, with the following attributes:
+ **data- attributes**:
	+ **range="12-days"**: This is your endpoint alias
	+ **unit="days"**: This is a natural language label, which must be parseable into a date range, e.g. days, months, years
	+ **range-value="12"**: An integer to compliment the data-unit, which is utilised by the graph to accurately render the data points
	+ **steps="4"**: How many dividing lines should be on the histogram's x-axis

*4 example date ranges are available in the `index.html` boilerplate.*

#### Histograms
+ **DOM reference**: '.histogram'
+ **Description**: Displays filtered metric data points on a histogram
+ **data- attributes**:
	+ **metric="your-metric-alias"**: This should match PowerDashboard configuration

*4 example histograms are available in the `index.html` boilerplate.*

### Theming

*@todo*

## API notes

### URL structure
URLs are concatenated on the client-side, using a combination of server settings, a metric alias, and an endpoint alias.

`[host]/[uriPrefix]/[endpoint alias].json`

+ **host**: see *Configuration > PowerDashboard > apiConfig*
+ **uriPrefix**: see *Configuration > PowerDashboard > apiConfig*
+ *endpoint alias*: If it is the Wheel Graph which is requesting data, this is controlled under *Configuration > PowerDashboard > apiConfig > wheelGraphEndpointAlias*. Otherwise, this comes from the date range filters (see *Markup configuration> Date range filters*)

### Data point structure
As a response from each endpoint (e.g. 1-year.json), Power Dashboard expects an JSON-formatted array of data point objects, ordered oldest-first. Each data point object expects the following data:

```
[
		{
		"timestamp": 1389087180000,
		"my_data_point": 135,
		"my_other_point": 4
	}, {
		"timestamp": 1389087190000,
		"my_data_point": 115,
		"my_other_point": 1
	}
	// ...
]
```

### Average data ranges
Histograms support average ranges, which show a transparent range line behind the average data, to show minimum and maximum values at a given time. To output this in the API, you format each data point object as the following:

```
{
	"my_point": 1.5,
	"min_my_point": 1,
	"max_my_point": 2,
	"my_other_point"
}
```

## Dependencies

Power Dashboard has the following dependencies:

+ jQuery & Lo-Dash — DOM and JS utilities
+ D3 — SVG library, used for the graphs
+ Backbone — interfaces with the graph to display the 'current metrics', and control the Accordion UI.
+ Sylvester — vector calculation for graphs
+ Tween.js — tweening for the graphs, utilising window.requestAnimationFrame
+ Simple Inheritance — inheritance for JS objects, used for the app's components
+ Moment — data/time parsing
+ Modernizr — browser feature detection
+ jQuery tinypubsub — a global publish/subscribe pattern for some components

## Todo

+ Remove throttling from the open source version
+ Look into reducing depedencies
+ Specificy the timestamp in the API notes
+ Script loader for PowerDashboard code.
+ Write about theming