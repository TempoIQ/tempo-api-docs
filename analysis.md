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

## Creating Endpoints

Configure analytics for later querying by creating endpoints in your app. The list of endpoints can found at `<HOST>/apps/name/<APPNAME>/endpoints`.
Navigate to 'Add new' to create a new endpoint:

![API endpoint editor]({{ site.baseurl }}/images/endpoint-editor.png)

* **Title** -- Required. Name of the endpoint.
* **Slug** -- Shortened version of the title that is part of the endpoint's URL.
* **Group By** -- Required. Identifier to use for querying subsets of data. For instance, specifying
"device_id" would enable getting history for one or more device IDs without creating a separate endpoint
per device.
* **Calculate** -- Field name + computation pairs. For example, "voltage" with "mean" would calculate the mean voltage
for each group over the specified time window.
* **Windows** -- Periods over which to perform the above calculations. Required if any calculations are specified.
* **Timezone** -- Timezone to use for determining day boundaries. Used for windows at least 1 day long.

Once the endpoint is configured, any data written from that point forward will be accessible via the API.


## Using Endpoints

**Endpoint**:  `GET https://<HOST>/api/apps/id/<APP_ID>/endpoints/<SLUG>/history`

**Query string parameters:**

* **groups**: array of groups you wish to read from. These are the values corresponding to the 'Group By' key. Required.
* **interval.start, interval.end**: ISO-8601 formatted timestamps for the beginning and end of the time range you wish to read.
* **interval.since**: ISO-8601 duration -- Will read this amount of time in the past, until the current time. Must specify either start/end or since.
* **window.period**: ISO-8601 duration -- Must be one of the windows configured in the endpoint: PT1S (1 second), PT1M (minute), PT1H (hour), P1D (day), P1W (week), P1M (month), or P1Y (year). Omitting the window is equivalent to requesting raw data.
* **limit**: Integer -- Maximum number of events to return (optional, default=1000)

**Returns:** JSON object with *events* array

## Example

An endpoint for calculating hourly min and max `temperature` for each `thermostat_id`.

* Title: Temp Summary
* Group by: thermostat_id
* Calculate: temperature with 'min', temperature with 'max'
* Windows: 1 hour

Example API call:

    curl -i -u $(API_KEY):$(API_SECRET) \
        'https://acme-inc.pipelines.tempoiq.com/api/apps/id/00000000-0000-0001-0000-000000000001/endpoints/temp-summary/history?groups\[\]=dev1&interval.since=PT6H&window.period=PT1H'

This requests the last six hours of data for thermostat_id `dev1`. Note that the square brackets after 'groups' usually must be escaped when running from the command line, but are not necessary in the actual request URL.
