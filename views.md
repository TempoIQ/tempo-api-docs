---
layout: default
title: App views
---

## Introduction

Views allow you to visualize your data in TempoIQ as a line graph, bar graph,
gauge, or numerical metric widget. Multiple views can be 
added to an application dashboard to create an overview of your data at a glance.

## Creating a View

Create a new view by clicking the "Add New" button from the main app page:

![Add View button]({{ site.baseurl }}/images/view-add-button.png)

This launches a wizard to guide you through creating your view.

### Step 1

![Step 1]({{ site.baseurl }}/images/view-step1.png)

* **Realtime vs. Historical**: Realtime views display raw data over a short time range. Historical views show analytics over longer time ranges, for example, an hourly average over the past day.
* **Number of measurements**: Select *multiple* if you are visualizing multiple 
types of measurements on the same graph, such as temperature and humidity.
* **Number of identifiers**: Select *multiple* if you are visualizing data from
several different devices or groups. 

The choices in this step affect which view types and options are available. For
example, you must choose *historical* to configure analytics such as min/max.

### Step 2

![Step 2]({{ site.baseurl }}/images/view-step2.png)

Select the measurement field(s) and identifier(s) to add to the view. The dropdown
menus list the possible field names that TempoIQ has seen. If the list is empty or 
doesn't have the field you're looking for, make sure you've written an event with
the required fields so TempoIQ can learn it.

* **Measurement(s)**: The (dependent) y-axis variable that you want to visualize (e.g. temperature, power, speed)
* **Identifier category**: The field name of the identifier (e.g. device ID, state)
* **Identifier name**: The specific value(s) of the identifier category selected. (e.g. OEM-190-620 [for device ID], California [for state])

### Step 3

The available options for view types (line, bar, etc.) depend on the settings you
specified in Step 1.

#### Realtime view

![Step 3 - Realtime]({{ site.baseurl }}/images/view-step3-rt.png)

Add labels to make it clear what's contained in the view

* **Title**: Title for the view
* **Units**: Unit of measure (optional)

#### Historical view

![Step 3 - Historical]({{ site.baseurl }}/images/view-step3-historical.png)

Historical views have several additional settings to configure the analytics:

* **Time window**: What time range should be displayed in the view?
* **Calculate**: What value should be calculated for each group and measurement?
* **Calculation window**: How long should each calculation cover? For example: average over one hour. This should be less than the time window so you can see multiple calculated points in the view.
