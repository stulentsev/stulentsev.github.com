---
layout: post
title: "DIY: funnel chart"
date: 2012-10-20 20:02
comments: true
keywords: funnel chart highcharts
categories: clientside charts
---

Recently I came across this excellent way of visualizing a funnel. It's simple, informative
and it doesn't look like a freaking funnel (with neck and all).

{% img /images/uploads/2012/10/funnel1.png %}

[link to source](http://chart.apis.google.com/chart?cht=bhs&chco=ffffff,FF9900&chxt=x,x,y&chxl=1%3a|Percentage%20converting|2%3a|Step%206|Step%205|Step%204|Step%203|Step%202|Step%201&chxp=1,50|3,50&chd=t:0,12.5,28,29,35.5,48.5|100,75,44,42,29,3&chbh=a&chs=800x230&chm=N%2a%2a%,000000,1,-1,11,,c&chds=0,100)


The only negative property of it is that it's implemented with Google Charts API. Sure, 
it's free and pretty, but it's also an external service call and a dependency. Today I'll 
show you how to make this kind of charts yourself in your charting library (I'll be using 
[highcharts.js](http://highcharts.com)).

<!-- more -->

This can be done with any charting library that supports stacked bar charts. Because that's
what it is, a stacked bar. The trick is that one of the stack parts is invisible and serves
as a padding. Look:


{% img /images/uploads/2012/10/funnel2.png %}
[link to source](http://chart.apis.google.com/chart?cht=bhs&chco=0000ff,FF9900&chxt=x,x,y&chxl=1%3a|Percentage%20converting|2%3a|Step%206|Step%205|Step%204|Step%203|Step%202|Step%201&chxp=1,50|3,50&chd=t:0,12.5,28,29,35.5,48.5|100,75,44,42,29,3&chbh=a&chs=800x230&chm=N%2a%2a%,000000,1,-1,11,,c&chds=0,100)


You see, the basic idea is pretty simple. The only hard part is to translate into series
of commands to your charting lib. I have made a small function for myself that does creates
a series for highcharts:

``` javascript
var makeSeriesConfig = function (values) {
    // we need max value to calculate padding correctly
    var max_value = null;
    values.forEach(function (v) {
        if (!max_value || max_value < v.value) {
            max_value = v.value;
        }
    });

    var padding = {
        name: 'padding',
        dataLabels: {
            enabled: false // don't show labels for padding
        },
        data: [] // will fill later
    };

    var centralPiece = {
        name: 'Value',
        data: [], // will fill later
        dataLabels: {
            enabled: true,
        }
    }

    values.forEach(function (v) {
        var w = (max_value - v.value) / 2;
        padding.data.push({
            y: w,
            color: 'white' // background color
        });
        
        centralPiece.data.push({
          y: v.value,
          name: v.name
        });
    });


    return [centralPiece, padding]
};

```

Later we use this function in a chart definition:

``` javascript
var chart;
$(document).ready(function () {
    chart = new Highcharts.Chart({
        chart: {
            renderTo: 'container',
            type: 'bar'
        },
        // ... other options go here ...
        series: makeSeriesConfig([
                                    {name: 'Step 1', value: 300},
                                    {name: 'Step 2', value: 240},
                                    {name: 'Step 3', value: 70},
                                    {name: 'Step 4', value: 40},
                                    {name: 'Step 5', value: 10}
                                ])
    });
});
```


Here's my final result which I'm happy with and which is going from proof-of-concept to
active development. 

## Final result
{% jsfiddle 4fpua %}