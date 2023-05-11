---
title: "Amplifier"
layout: help
categories: help classes
slug: "amplifier"
permalink: "/:categories/:slug/"
publish: false
---

### Instantiation

{% highlight matlab %}
ampObj = Amplifier([specs])
ampObj = Amplifier.fromScenario(scenario, gain [, sweepFlag])
{% endhighlight %}

| Properties   | Description                     | Type                        | Optional | Supported values
| ------------ | ------------------------------- | --------------------------- | -------- | ----------------
| `specs`      | RF/analog specifications        | [AnalogSpecs][analog-specs] | Yes      |         
| `scenario`   | Design scenario including test signals and SNR budgeting | [Scenario][scenario] | No       |         
| `gain`       | Amplifier gain                  | [Ratio][ratio]              | No       |         
| `sweepFlag`  | Flag for enabling nested sweeps | logical                     | Yes      | `0` (default) <br/> `1`
{: .help-props-table}


### Methods

| Method name | Description                               | Argument(s)     | Supported argument values
| ----------- | ----------------------------------------- | --------------- | -------------------------
| `process()` | Process (amplify) input signal(s)         | test            |
| `combine()` | Combine multiple amplifiers for toughest specifications |   |
{: .help-methods-table}


### Examples


### See also

[AnalogSpecs][analog-specs]  
[Ratio][ratio]  
[Scenario][scenario]  

[analog-specs]: {{site.baseurl}}{%post_url /help/2023-04-28-analog-specs %}  
[ratio]: {{site.baseurl}}{%post_url /help/2023-04-28-ratio %}  
[scenario]: {{site.baseurl}}{%post_url /help/2023-04-28-scenario %}  
