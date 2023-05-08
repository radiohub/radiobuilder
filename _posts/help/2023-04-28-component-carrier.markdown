---
title: "ComponentCarrier"
layout: help
categories: help classes
slug: "component-carrier"
permalink: "/:categories/:slug/"
---

### Instantiation

{% highlight matlab %}
signalOut = ComponentCarrier(freq, bw, level, snr, papr [, sweepFlag])
{% endhighlight %}

| Properties   | Description                     | Type                 | Optional | Supported values
| ------------ | ------------------------------- | -------------------- | -------- | ----------------
| `freq`       | Carrier frequency               | [Frequency]          | No       |         
| `bw`         | Occupied RF bandwidth           | [Frequency]          | No       |         
| `level`      | Carrier level                   | [Power] or [Voltage] | No       |         
| `snr`        | Signal-to-noise ratio           | [Ratio]              | No       |         
| `papr`       | Peak-to-average power ratio     | [Ratio]              | No       |         
| `sweepFlag`  | Flag for enabling nested sweeps | logical              | Yes      | `0` (default) <br/> `1`
{: .help-props-table}


### Methods

| Method name      | Description                               | Argument(s)     | Supported argument values
| ---------------- | ----------------------------------------- | --------------- | -------------------------
| `getPeakLevel()` | Get carrier peak level                    |                 |
| `select()`       | Select signal(s) based on key-value pairs | key-value pairs <br/> (at least one pair) | `'frequency', freqVal` <br/> `'bw', bwVal` <br/> `'level', levelVal` <br/> `'snr', snrVal` <br/> `'papr', paprVal` |
| `printOut()`     | Print signal properties                   | orientation (optional)    | `'h'` (default) <br/>  `'v'`
| `getFrequency()` | Get carrier frequency                     |                 |
| `getBW()`        | Get occupied RF bandwidth                 |                 |
| `getLevel()`     | Get carrier average or rms level          |                 |
| `getSNR()`       | Get signal-to-noise ratio                 |                 |
| `getPAPR()`      | Get Peak-to-average ratio                 |                 |
{: .help-methods-table}


### Examples

<details class="collapsible" markdown="1"><summary>Define a signal</summary>

Define signal parameters

{% highlight matlab %}
freq = Frequency(5.25e9,'hz');  % Channel center frequency
bw = Frequency(160e6,'hz');     % Channel bandwidth
level = Power(-56,'dbm');       % Channel power
snr = Ratio(32,'db');           % Channel signal-to-noise ratio
papr = Ratio(12,'db');          % Peak-to-average ratio I+jQ
{% endhighlight %}

Generate the signal and check its parameters using `printOut`

{% highlight matlab %}
signalOut = ComponentCarrier(freq, bw, level, snr, papr);
signalOut.printOut()
{% endhighlight %}

which prints out the following

<div class="language-matlab matlab-printout">  
 Freq [MHz]:  5250.0
   BW [MHz]:   160.0
Level [dBm]:   -56.0
  SNR [ dB]:    32.0
 PAPR [ dB]:    12.0
</div>

</details>


<details class="collapsible" markdown="1"><summary>Single sweep</summary>

We can sweep any of the signal parameters, for example, signal power

{% highlight matlab %}
freq = Frequency(5.25e9,'hz');
bw = Frequency(160e6,'hz');
level = Power([-60:2:-50],'dbm');
snr = Ratio(32,'db');
papr = Ratio(12,'db');
{% endhighlight %}

Generate the signal and check its properties using `printOut`

{% highlight matlab %}
signalOut = ComponentCarrier(freq, bw, level, snr, papr);
signalOut.printOut('v')
{% endhighlight %}

which prints out the following

{% highlight tex %}
Freq [MHz]   BW [MHz]  Level [dBm]   SNR [dB]   PAPR [dB]  
  5250.0      160.0       -60.0        32.0        12.0    
  5250.0      160.0       -58.0        32.0        12.0    
  5250.0      160.0       -56.0        32.0        12.0    
  5250.0      160.0       -54.0        32.0        12.0    
  5250.0      160.0       -52.0        32.0        12.0    
  5250.0      160.0       -50.0        32.0        12.0   
{% endhighlight %}

</details>

<details class="collapsible" markdown="1"><summary>Nested sweeps</summary>

We can also create nested sweeps of two or more properties using `sweepFlag`. For example, here we sweep both the signal level and signal bandwidth

{% highlight matlab %}
freq = Frequency(5.25e9,'hz');
bw = Frequency([20,160]*1e6,'hz');
level = Power([-60:2:-50],'dbm');
snr = Ratio(32,'db');
papr = Ratio(12,'db');
{% endhighlight %}

Generate the nested sweeps by settings `sweepFlag` to 1 (or alternatively `true`) and check the results using `printOut`

{% highlight matlab %}
signalOut = ComponentCarrier(freq, bw, level, snr, papr, 1);
signalOut.printOut('v')
{% endhighlight %}

which prints out the following

{% highlight tex %}
Freq [MHz]   BW [MHz]  Level [dBm]   SNR [dB]   PAPR [dB]  
  5250.0       20.0       -60.0        32.0        12.0    
  5250.0       20.0       -58.0        32.0        12.0    
  5250.0       20.0       -56.0        32.0        12.0    
  5250.0       20.0       -54.0        32.0        12.0    
  5250.0       20.0       -52.0        32.0        12.0    
  5250.0       20.0       -50.0        32.0        12.0    
  5250.0      160.0       -60.0        32.0        12.0    
  5250.0      160.0       -58.0        32.0        12.0    
  5250.0      160.0       -56.0        32.0        12.0    
  5250.0      160.0       -54.0        32.0        12.0    
  5250.0      160.0       -52.0        32.0        12.0    
  5250.0      160.0       -50.0        32.0        12.0   
{% endhighlight %}

We can then select the 20 MHz power sweep and printout the results

{% highlight matlab %}
signalOut.select('bw',Frequency(20e6)).printOut('v')
{% endhighlight %}

which then prints out

{% highlight tex %}
Freq [MHz]   BW [MHz]  Level [dBm]   SNR [dB]   PAPR [dB]  
  5250.0       20.0       -60.0        32.0        12.0    
  5250.0       20.0       -58.0        32.0        12.0    
  5250.0       20.0       -56.0        32.0        12.0    
  5250.0       20.0       -54.0        32.0        12.0    
  5250.0       20.0       -52.0        32.0        12.0    
  5250.0       20.0       -50.0        32.0        12.0   
{% endhighlight %}

</details>

### See also
[Frequency][frequency]  
[Power][power]  
[Ratio][ratio]  
[Voltage][voltage]  

[frequency]: {% post_url /help/2023-04-28-frequency %}  
[power]: {% post_url /help/2023-04-28-power %}  
[ratio]: {% post_url /help/2023-04-28-ratio %}  
[voltage]: {% post_url /help/2023-04-28-voltage %}  
