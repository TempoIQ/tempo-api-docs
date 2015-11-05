---
layout: default
title: Concepts
redirect_from: "/"
---

## Events

You send data to TempoIQ as a sequence of events. An event is
a JSON object, which can contain any number of attributes containing measurements, 
metadata, or nested objects.

For example:

    {
      "deviceType": "thermostat",
      "serialNumber": "X3251821",
      "userInfo": {
        "id": 1423,
        "region": "south"
      },
      "currentTemp": 71.0,
      "currentHumidity": 65.0
    }

There are very few limitations on how you structure events---they're meant
to work seamlessly with data formats that you already use in your application.
There's no need to pre-define devices or channels with this event model. All
data is contained within the event itself.

When you send an event to TempoIQ, we automatically add a timestamp. If you
want to provide your own timestamp, for instance, if you are batching events
over time and writing many at once, you can override it through a special timestamp
field. 

Typically events are sent as JSON over HTTPS, but we also support MQTT and other
protocols.


## Analytics

Analytics are the heart of TempoIQ--they calculate specific metrics from the raw stream of input
events. They can filter for a subset of events, group into 
sub-streams based on data in the events, and aggregate values
over time or across devices. 

Example: For each *userInfo.region*, find the average *currentTemp* of all
events so far this hour.

Analytics are persistent operations. In other words, after you've 
defined an analytic, it will continue to process new events as they come in.


## Views

Once you set up your analytics, you can view the output in a realtime visualization.
This chart can be added to a dashboard, and even shared with people outside your 
organization.

If you have analytics that perform a grouping operation, for example, average temperature per region,
you can create a chart to display one group or all groups.


## Alerts

TempoIQ enables you to define rules to send an alert if an analytic crosses a 
threshold. These alerts can be sent to other applications via HTTPS, which can then be used to send
notifications via email or SMS.


## Examples

A solar company collects data from its inverters and sends it to TempoIQ in the following format:

    {
      "_$_ts": "2015-04-29T13:01:00Z",  // Optional
      "inverter_id": "asdf12345", 
      "installation": "3425",
      "measurements": {
        "voltage": 118.2,
        "ac_power": 410.0,
        "dc_power": 429.5,
        "energy": 0.35
      }
    }

They want to have access to several realtime metrics:

### Single device dashboard

A dashboard for an inverter which continuously updates with the latest value for 
each measurement. Given an inverter ID, they can subscribe to raw events to display
in gauges or graphs.


### Instantaneous totals

Track the total power being generated for the entire install base by summing the latest 
*ac_power* value across all devices.


### Cumulative production

A feed showing an installation's total energy production for the day. Given an 
installation ID, sums all *energy* values from events today from that installation. 
This will continuously increase throughout the day and reset at midnight.
