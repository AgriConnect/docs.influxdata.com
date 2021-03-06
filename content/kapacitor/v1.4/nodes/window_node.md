---
title: WindowNode (Kapacitor TICKscript node)
description: WindowNode caches data within a moving time range.
note: Auto generated by tickdoc

menu:
  kapacitor_1_4:
    name: WindowNode
    identifier: window_node
    weight: 340
    parent: TICKscript nodes
---
### Constructor

| Chaining Method | Description |
|:---------|:---------|
| **[window](#descr)&nbsp;(&nbsp;)** | Create a new node that windows the stream by time.  |

### Property Methods

| Setters | Description |
|:---|:---|
| **[align](#align)&nbsp;(&nbsp;)** | If the `align` property is not used to modify the `window` node, then the window alignment is assumed to start at the time of the first data point it receives. If `align` property is set, the window time edges will be truncated to the `every` property (For example, if a data point's time is 12:06 and the `every` property is `5m` then the data point's window will range from 12:05 to 12:10).  |
| **[every](#every)&nbsp;(&nbsp;`value`&nbsp;`time.Duration`)** | How often the current window is emitted into the pipeline. If equal to zero, then every new point will emit the current window.  |
| **[everyCount](#everycount)&nbsp;(&nbsp;`value`&nbsp;`int64`)** | EveryCount determines how often the window is emitted based on the count of points. A value of 1 means that every new point will emit the window.  |
| **[fillPeriod](#fillperiod)&nbsp;(&nbsp;)** | FillPeriod instructs the WindowNode to wait till the period has elapsed before emitting the first batch. This only applies if the period is greater than the every value.  |
| **[period](#period)&nbsp;(&nbsp;`value`&nbsp;`time.Duration`)** | The period, or length in time, of the window.  |
| **[periodCount](#periodcount)&nbsp;(&nbsp;`value`&nbsp;`int64`)** | PeriodCount is the number of points per window.  |



### Chaining Methods
[Alert](/kapacitor/v1.4/nodes/window_node/#alert), [Barrier](/kapacitor/v1.4/nodes/window_node/#barrier), [Bottom](/kapacitor/v1.4/nodes/window_node/#bottom), [Combine](/kapacitor/v1.4/nodes/window_node/#combine), [Count](/kapacitor/v1.4/nodes/window_node/#count), [CumulativeSum](/kapacitor/v1.4/nodes/window_node/#cumulativesum), [Deadman](/kapacitor/v1.4/nodes/window_node/#deadman), [Default](/kapacitor/v1.4/nodes/window_node/#default), [Delete](/kapacitor/v1.4/nodes/window_node/#delete), [Derivative](/kapacitor/v1.4/nodes/window_node/#derivative), [Difference](/kapacitor/v1.4/nodes/window_node/#difference), [Distinct](/kapacitor/v1.4/nodes/window_node/#distinct), [Ec2Autoscale](/kapacitor/v1.4/nodes/window_node/#ec2autoscale), [Elapsed](/kapacitor/v1.4/nodes/window_node/#elapsed), [Eval](/kapacitor/v1.4/nodes/window_node/#eval), [First](/kapacitor/v1.4/nodes/window_node/#first), [Flatten](/kapacitor/v1.4/nodes/window_node/#flatten), [GroupBy](/kapacitor/v1.4/nodes/window_node/#groupby), [HoltWinters](/kapacitor/v1.4/nodes/window_node/#holtwinters), [HoltWintersWithFit](/kapacitor/v1.4/nodes/window_node/#holtwinterswithfit), [HttpOut](/kapacitor/v1.4/nodes/window_node/#httpout), [HttpPost](/kapacitor/v1.4/nodes/window_node/#httppost), [InfluxDBOut](/kapacitor/v1.4/nodes/window_node/#influxdbout), [Join](/kapacitor/v1.4/nodes/window_node/#join), [K8sAutoscale](/kapacitor/v1.4/nodes/window_node/#k8sautoscale), [KapacitorLoopback](/kapacitor/v1.4/nodes/window_node/#kapacitorloopback), [Last](/kapacitor/v1.4/nodes/window_node/#last), [Log](/kapacitor/v1.4/nodes/window_node/#log), [Max](/kapacitor/v1.4/nodes/window_node/#max), [Mean](/kapacitor/v1.4/nodes/window_node/#mean), [Median](/kapacitor/v1.4/nodes/window_node/#median), [Min](/kapacitor/v1.4/nodes/window_node/#min), [Mode](/kapacitor/v1.4/nodes/window_node/#mode), [MovingAverage](/kapacitor/v1.4/nodes/window_node/#movingaverage), [Percentile](/kapacitor/v1.4/nodes/window_node/#percentile), [Sample](/kapacitor/v1.4/nodes/window_node/#sample), [Shift](/kapacitor/v1.4/nodes/window_node/#shift), [Sideload](/kapacitor/v1.4/nodes/window_node/#sideload), [Spread](/kapacitor/v1.4/nodes/window_node/#spread), [StateCount](/kapacitor/v1.4/nodes/window_node/#statecount), [StateDuration](/kapacitor/v1.4/nodes/window_node/#stateduration), [Stats](/kapacitor/v1.4/nodes/window_node/#stats), [Stddev](/kapacitor/v1.4/nodes/window_node/#stddev), [Sum](/kapacitor/v1.4/nodes/window_node/#sum), [SwarmAutoscale](/kapacitor/v1.4/nodes/window_node/#swarmautoscale), [Top](/kapacitor/v1.4/nodes/window_node/#top), [Union](/kapacitor/v1.4/nodes/window_node/#union), [Where](/kapacitor/v1.4/nodes/window_node/#where), [Window](/kapacitor/v1.4/nodes/window_node/#window)
<a id='descr'/><hr/><br/>
### Description

A `window` node caches data within a moving time range.
The `period` property of `window` defines the time range covered by `window`.

The `every` property of `window` defines the frequency at which the window
is emitted to the next node in the pipeline.

The `align` property of `window` defines how to align the window edges.
(By default, the edges are defined relative to the first data point the `window`
node receives.)

Example:


```javascript
    stream
        |window()
            .period(10m)
            .every(5m)
        |httpOut('recent')
```

This example emits the last `10 minute` period every `5 minutes` to the pipeline's `httpOut` node.
Because `every` is less than `period`, each time the window is emitted it contains `5 minutes` of
new data and `5 minutes` of the previous period's data.

NOTE: Because no `align` property is defined, the `window` edge is defined relative to the first data point.


<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

Properties
----------

Property methods modify state on the calling node.
They do not add another node to the pipeline, and always return a reference to the calling node.
Property methods are marked using the `.` operator.


### Align

If the `align` property is not used to modify the `window` node, then the
window alignment is assumed to start at the time of the first data point it receives.
If `align` property is set, the window time edges
will be truncated to the `every` property (For example, if a data point's time
is 12:06 and the `every` property is `5m` then the data point's window will range
from 12:05 to 12:10).


```javascript
window.align()
```

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>


### Every

How often the current window is emitted into the pipeline.
If equal to zero, then every new point will emit the current window.


```javascript
window.every(value time.Duration)
```

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>


### EveryCount

EveryCount determines how often the window is emitted based on the count of points.
A value of 1 means that every new point will emit the window.


```javascript
window.everyCount(value int64)
```

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>


### FillPeriod

FillPeriod instructs the [WindowNode](/kapacitor/v1.4/nodes/window_node/) to wait till the period has elapsed before emitting the first batch.
This only applies if the period is greater than the every value.


```javascript
window.fillPeriod()
```

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>


### Period

The period, or length in time, of the window.


```javascript
window.period(value time.Duration)
```

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>


### PeriodCount

PeriodCount is the number of points per window.


```javascript
window.periodCount(value int64)
```

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>


Chaining Methods
----------------

Chaining methods create a new node in the pipeline as a child of the calling node.
They do not modify the calling node.
Chaining methods are marked using the `|` operator.


### Alert

Create an alert node, which can trigger alerts.


```javascript
window|alert()
```

Returns: [AlertNode](/kapacitor/v1.4/nodes/alert_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### Barrier

Create a new Barrier node that emits a BarrierMessage periodically

One BarrierMessage will be emitted every period duration


```javascript
window|barrier()
```

Returns: [BarrierNode](/kapacitor/v1.4/nodes/barrier_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### Bottom

Select the bottom `num` points for `field` and sort by any extra tags or fields.


```javascript
window|bottom(num int64, field string, fieldsAndTags ...string)
```

Returns: [InfluxQLNode](/kapacitor/v1.4/nodes/influx_q_l_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### Combine

Combine this node with itself. The data are combined on timestamp.


```javascript
window|combine(expressions ...ast.LambdaNode)
```

Returns: [CombineNode](/kapacitor/v1.4/nodes/combine_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### Count

Count the number of points.


```javascript
window|count(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1.4/nodes/influx_q_l_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### CumulativeSum

Compute a cumulative sum of each point that is received.
A point is emitted for every point collected.


```javascript
window|cumulativeSum(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1.4/nodes/influx_q_l_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### Deadman

Helper function for creating an alert on low throughput, a.k.a. deadman's switch.

- Threshold -- trigger alert if throughput drops below threshold in points/interval.
- Interval -- how often to check the throughput.
- Expressions -- optional list of expressions to also evaluate. Useful for time of day alerting.

Example:


```javascript
    var data = stream
        |from()...
    // Trigger critical alert if the throughput drops below 100 points per 10s and checked every 10s.
    data
        |deadman(100.0, 10s)
    //Do normal processing of data
    data...
```

The above is equivalent to this
Example:


```javascript
    var data = stream
        |from()...
    // Trigger critical alert if the throughput drops below 100 points per 10s and checked every 10s.
    data
        |stats(10s)
            .align()
        |derivative('emitted')
            .unit(10s)
            .nonNegative()
        |alert()
            .id('node \'stream0\' in task \'{{ .TaskName }}\'')
            .message('{{ .ID }} is {{ if eq .Level "OK" }}alive{{ else }}dead{{ end }}: {{ index .Fields "emitted" | printf "%0.3f" }} points/10s.')
            .crit(lambda: "emitted" <= 100.0)
    //Do normal processing of data
    data...
```

The `id` and `message` alert properties can be configured globally via the 'deadman' configuration section.

Since the [AlertNode](/kapacitor/v1.4/nodes/alert_node/) is the last piece it can be further modified as usual.
Example:


```javascript
    var data = stream
        |from()...
    // Trigger critical alert if the throughput drops below 100 points per 10s and checked every 10s.
    data
        |deadman(100.0, 10s)
            .slack()
            .channel('#dead_tasks')
    //Do normal processing of data
    data...
```

You can specify additional lambda expressions to further constrain when the deadman's switch is triggered.
Example:


```javascript
    var data = stream
        |from()...
    // Trigger critical alert if the throughput drops below 100 points per 10s and checked every 10s.
    // Only trigger the alert if the time of day is between 8am-5pm.
    data
        |deadman(100.0, 10s, lambda: hour("time") >= 8 AND hour("time") <= 17)
    //Do normal processing of data
    data...
```



```javascript
window|deadman(threshold float64, interval time.Duration, expr ...ast.LambdaNode)
```

Returns: [AlertNode](/kapacitor/v1.4/nodes/alert_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### Default

Create a node that can set defaults for missing tags or fields.


```javascript
window|default()
```

Returns: [DefaultNode](/kapacitor/v1.4/nodes/default_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### Delete

Create a node that can delete tags or fields.


```javascript
window|delete()
```

Returns: [DeleteNode](/kapacitor/v1.4/nodes/delete_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### Derivative

Create a new node that computes the derivative of adjacent points.


```javascript
window|derivative(field string)
```

Returns: [DerivativeNode](/kapacitor/v1.4/nodes/derivative_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### Difference

Compute the difference between points independent of elapsed time.


```javascript
window|difference(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1.4/nodes/influx_q_l_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### Distinct

Produce batch of only the distinct points.


```javascript
window|distinct(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1.4/nodes/influx_q_l_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### Ec2Autoscale

Create a node that can trigger autoscale events for a ec2 autoscalegroup.


```javascript
window|ec2Autoscale()
```

Returns: [Ec2AutoscaleNode](/kapacitor/v1.4/nodes/ec2_autoscale_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### Elapsed

Compute the elapsed time between points


```javascript
window|elapsed(field string, unit time.Duration)
```

Returns: [InfluxQLNode](/kapacitor/v1.4/nodes/influx_q_l_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### Eval

Create an eval node that will evaluate the given transformation function to each data point.
A list of expressions may be provided and will be evaluated in the order they are given.
The results are available to later expressions.


```javascript
window|eval(expressions ...ast.LambdaNode)
```

Returns: [EvalNode](/kapacitor/v1.4/nodes/eval_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### First

Select the first point.


```javascript
window|first(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1.4/nodes/influx_q_l_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### Flatten

Flatten points with similar times into a single point.


```javascript
window|flatten()
```

Returns: [FlattenNode](/kapacitor/v1.4/nodes/flatten_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### GroupBy

Group the data by a set of tags.

Can pass literal * to group by all dimensions.
Example:


```javascript
    |groupBy(*)
```



```javascript
window|groupBy(tag ...interface{})
```

Returns: [GroupByNode](/kapacitor/v1.4/nodes/group_by_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### HoltWinters

Compute the holt-winters (https://docs.influxdata.com/influxdb/latest/query_language/functions/#holt-winters) forecast of a data set.


```javascript
window|holtWinters(field string, h int64, m int64, interval time.Duration)
```

Returns: [InfluxQLNode](/kapacitor/v1.4/nodes/influx_q_l_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### HoltWintersWithFit

Compute the holt-winters (https://docs.influxdata.com/influxdb/latest/query_language/functions/#holt-winters) forecast of a data set.
This method also outputs all the points used to fit the data in addition to the forecasted data.


```javascript
window|holtWintersWithFit(field string, h int64, m int64, interval time.Duration)
```

Returns: [InfluxQLNode](/kapacitor/v1.4/nodes/influx_q_l_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### HttpOut

Create an HTTP output node that caches the most recent data it has received.
The cached data are available at the given endpoint.
The endpoint is the relative path from the API endpoint of the running task.
For example, if the task endpoint is at `/kapacitor/v1/tasks/<task_id>` and endpoint is
`top10`, then the data can be requested from `/kapacitor/v1/tasks/<task_id>/top10`.


```javascript
window|httpOut(endpoint string)
```

Returns: [HTTPOutNode](/kapacitor/v1.4/nodes/http_out_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### HttpPost

Creates an HTTP Post node that POSTS received data to the provided HTTP endpoint.
HttpPost expects 0 or 1 arguments. If 0 arguments are provided, you must specify an
endpoint property method.


```javascript
window|httpPost(url ...string)
```

Returns: [HTTPPostNode](/kapacitor/v1.4/nodes/http_post_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### InfluxDBOut

Create an influxdb output node that will store the incoming data into InfluxDB.


```javascript
window|influxDBOut()
```

Returns: [InfluxDBOutNode](/kapacitor/v1.4/nodes/influx_d_b_out_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### Join

Join this node with other nodes. The data are joined on timestamp.


```javascript
window|join(others ...Node)
```

Returns: [JoinNode](/kapacitor/v1.4/nodes/join_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### K8sAutoscale

Create a node that can trigger autoscale events for a kubernetes cluster.


```javascript
window|k8sAutoscale()
```

Returns: [K8sAutoscaleNode](/kapacitor/v1.4/nodes/k8s_autoscale_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### KapacitorLoopback

Create an kapacitor loopback node that will send data back into Kapacitor as a stream.


```javascript
window|kapacitorLoopback()
```

Returns: [KapacitorLoopbackNode](/kapacitor/v1.4/nodes/kapacitor_loopback_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### Last

Select the last point.


```javascript
window|last(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1.4/nodes/influx_q_l_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### Log

Create a node that logs all data it receives.


```javascript
window|log()
```

Returns: [LogNode](/kapacitor/v1.4/nodes/log_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### Max

Select the maximum point.


```javascript
window|max(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1.4/nodes/influx_q_l_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### Mean

Compute the mean of the data.


```javascript
window|mean(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1.4/nodes/influx_q_l_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### Median

Compute the median of the data. Note, this method is not a selector,
if you want the median point use `.percentile(field, 50.0)`.


```javascript
window|median(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1.4/nodes/influx_q_l_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### Min

Select the minimum point.


```javascript
window|min(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1.4/nodes/influx_q_l_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### Mode

Compute the mode of the data.


```javascript
window|mode(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1.4/nodes/influx_q_l_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### MovingAverage

Compute a moving average of the last window points.
No points are emitted until the window is full.


```javascript
window|movingAverage(field string, window int64)
```

Returns: [InfluxQLNode](/kapacitor/v1.4/nodes/influx_q_l_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### Percentile

Select a point at the given percentile. This is a selector function, no interpolation between points is performed.


```javascript
window|percentile(field string, percentile float64)
```

Returns: [InfluxQLNode](/kapacitor/v1.4/nodes/influx_q_l_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### Sample

Create a new node that samples the incoming points or batches.

One point will be emitted every count or duration specified.


```javascript
window|sample(rate interface{})
```

Returns: [SampleNode](/kapacitor/v1.4/nodes/sample_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### Shift

Create a new node that shifts the incoming points or batches in time.


```javascript
window|shift(shift time.Duration)
```

Returns: [ShiftNode](/kapacitor/v1.4/nodes/shift_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### Sideload

Create a node that can load data from external sources


```javascript
window|sideload()
```

Returns: [SideloadNode](/kapacitor/v1.4/nodes/sideload_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### Spread

Compute the difference between `min` and `max` points.


```javascript
window|spread(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1.4/nodes/influx_q_l_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### StateCount

Create a node that tracks number of consecutive points in a given state.


```javascript
window|stateCount(expression ast.LambdaNode)
```

Returns: [StateCountNode](/kapacitor/v1.4/nodes/state_count_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### StateDuration

Create a node that tracks duration in a given state.


```javascript
window|stateDuration(expression ast.LambdaNode)
```

Returns: [StateDurationNode](/kapacitor/v1.4/nodes/state_duration_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### Stats

Create a new stream of data that contains the internal statistics of the node.
The interval represents how often to emit the statistics based on real time.
This means the interval time is independent of the times of the data points the source node is receiving.


```javascript
window|stats(interval time.Duration)
```

Returns: [StatsNode](/kapacitor/v1.4/nodes/stats_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### Stddev

Compute the standard deviation.


```javascript
window|stddev(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1.4/nodes/influx_q_l_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### Sum

Compute the sum of all values.


```javascript
window|sum(field string)
```

Returns: [InfluxQLNode](/kapacitor/v1.4/nodes/influx_q_l_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### SwarmAutoscale

Create a node that can trigger autoscale events for a docker swarm cluster.


```javascript
window|swarmAutoscale()
```

Returns: [SwarmAutoscaleNode](/kapacitor/v1.4/nodes/swarm_autoscale_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### Top

Select the top `num` points for `field` and sort by any extra tags or fields.


```javascript
window|top(num int64, field string, fieldsAndTags ...string)
```

Returns: [InfluxQLNode](/kapacitor/v1.4/nodes/influx_q_l_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### Union

Perform the union of this node and all other given nodes.


```javascript
window|union(node ...Node)
```

Returns: [UnionNode](/kapacitor/v1.4/nodes/union_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### Where

Create a new node that filters the data stream by a given expression.


```javascript
window|where(expression ast.LambdaNode)
```

Returns: [WhereNode](/kapacitor/v1.4/nodes/where_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>

### Window

Create a new node that windows the stream by time.

NOTE: Window can only be applied to stream edges.


```javascript
window|window()
```

Returns: [WindowNode](/kapacitor/v1.4/nodes/window_node/)

<a class="top" href="javascript:document.getElementsByClassName('article-heading')[0].scrollIntoView();" title="top"><span class="icon arrow-up"></span></a>
