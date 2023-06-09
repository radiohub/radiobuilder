---
title: "Frequency"
layout: help
categories: help classes
slug: "frequency"
permalink: "/:categories/:slug/"
publish: true
---

### Instantiation
{% highlight matlab %}
freqOut = Frequency(freq [, unit] [, refFreq] [, sweepFlag])
{% endhighlight %}

| Properties   | Description                     | Type    | Optional | Supported values
| ------------ | ------------------------------- | ------- | -------- | -------------------------------
| `freq`       | Carrier frequency               | double  | No       |         
| `unit`       | Occupied RF bandwidth           | char    | Yes      | `'hz'` (default) <br /> `'dbhz'`       
| `refFreq`    | Carrier level                   | double  | Yes      |         
| `sweepFlag`  | Flag for enabling nested sweeps | logical | Yes      |         
{: .help-props-table}


### Methods

| Method name    | Description                      | Argument(s) | Supported argument values
| -------------- | -------------------------------- | ----------- | -------------------------
| `convertTo()`  | Get carrier peak level           | unit        | `'hz'` <br/> `'dbhz'`
| `getAbsFreq()` | Get carrier frequency            |             |
| `getRefFreq()` | Get occupied RF bandwidth        |             |
| `getValue()`   | Get carrier average or rms level |             |
{: .help-methods-table}


### Examples

<details class="collapsible" markdown="1"><summary>Define frequency offset</summary>

Define a 20 MHz offset relative to a 5160 MHz carrier

{% highlight matlab %}
freqOut = Frequency(20e6,'hz',5.16e9);
{% endhighlight %}

Get the offset value using `getValue`

{% highlight matlab %}
freqOut.getValue()
{% endhighlight %}

which gives

<div class="language-matlab matlab-printout">  
ans =
    20000000

</div>

We can also convert the relative offset to absolute frequency

{% highlight matlab %}
freqOutAbs = freqOut.getAbsFreq();
freqOutAbs.getValue()
{% endhighlight %}

Which, in this case, gives

<div class="language-matlab matlab-printout">  
ans =
   5.1800e+09
</div>

</details>

### See also
[Supported Quantities][supported-quantities]

[supported-quantities]: {% post_url /help/2023-05-03-supported-quantities %}  
