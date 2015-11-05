---
layout: default
title: Analysis API
---

## Introduction

TempoIQ's analysis API enables you to read data from your environment programmatically.
This lets you build custom visualizations, reports, or other tools as an alternative to
our built-in views. 

Our API uses HTTPS for historical queries. For realtime streaming features, we use
the Websocket protocol. In the examples below, we will illustrate making API calls with the
`curl` command line tool, but they should be easily transferrable to the HTTP library in your
language of choice. If you have any questions or uncertainty about using our API in your application,
just [email us](mailto:support@tempoiq.com)!

In order to use the API, you need your **key**, **secret**, and **host**. You
can find these on the Connect IQ page.

## Analysis APIs

### Reading historical data

Get the history for a view. In order to access stored data, the view must 
have a "grouping" field defined. This is the field in the event which you use to
address subsets of the data. This is often a device or user ID.

**Endpoint**:  `GET https://<HOST>/api/views/id/<VIEW_ID>/history`

**Query string parameters:**

* **key** (String) -- The grouping key to read from
* **start** (ISO8601 timestamp) -- The start of the time interval for the desired data
* **end** (ISO8601 timestamp) -- The end of the time interval for the desired data
* **limit** (Integer) -- Maximum number of events to return (optional, default=10000)

**Returns:** JSON object with *events* array

**Example:**

    curl -i -u $(API_KEY):$(API_SECRET) \
        'https://acme-inc.pipelines.tempoiq.com/api/views/id/00000000-0000-0001-0000-000000000001/history?key=device1&start=2015-09-14&end=2015-09-16'

    ---- Response ----
    HTTP/1.1 200 OK
    Content-Type: application/json;charset=utf-8
    Vary: Origin
    Content-Length: 178

    {"events":[{"power":23.0,"voltage":13.0,"device_id":"device1","_$_ts":"2015-09-15T19:12:51.534Z"},{"power":23.0,"voltage":15.0,"device_id":"device1","_$_ts":"2015-09-15T19:13:03.105Z"}]}%

