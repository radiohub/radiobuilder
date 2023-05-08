---
title: "Supported Quantities"
layout: post
categories: help general
slug: "supported-quantities"
permalink: "/:categories/:slug/"
---

### Instantiation
{% highlight matlab %}
freqOut = Frequency(freq, unit, refFreq [, sweepFlag])
{% endhighlight %}

| Properties  | Type      | Description                     | Optional | Default |
| ----------- | --------- | ------------------------------- | -------- | ------- |
| `freq`      | double    | Frequency                       | No       |         |
| `unit`      | char      | unit                            | Yes      | `'hz'`  |
| `refFreq`   | double    | Reference frequency             | No       | `0`     |
| `sweepFlag` | logical   | Flag for enabling nested sweeps | Yes      | `false` |


### Methods

<!-- {% highlight matlab %}
peakLevel = testSignal.getPeakLevel();
signalOut = testSignal.select([key1,value1] [, key2,value2] [, key3,value3] ..);
testSignal.printOut([orientation]);    
freq = testSignal.getFrequency();
bw = testSignal.getBW();
level = testSignal.getLevel();
snr = testSignal.getSNR();
papr = testSignal.getPAPR();
{% endhighlight %} -->

| Methods        | Description                               | Arguments       |
| -------------- | ----------------------------------------- | --------------- |
| `convertTo`    | Convert unit                              | `'hz'` <br /> `'dbhz'` |
| `getAbsFreq`   | Get absolute frequency                    |                 |
| `getRefFreq`   | Get reference frequency                   |                 |
| `getValue`     | Get frequency value                       |                 |


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

We can also convert an offset to absolute frequency

{% highlight matlab %}
freqOutAbs = freqOut.getAbsFreq();
freqOutAbs.getValue()
{% endhighlight %}

Which, in this case gives

<div class="language-matlab matlab-printout">  
ans =
   5.1800e+09
</div>

</details>

### See also
