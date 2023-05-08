---
title: "LocalOscillator"
layout: post
categories: help classes
slug: "local-oscillator"
permalink: "/:categories/:slug/"
---

### Syntax
{% highlight matlab %}
testSignal = ComponentCarrier(freq, bw, level, snr, papr, [sweepFlag])
{% endhighlight %}

### Description

| Parameter    | Type                 | Description                 | Optional | Default |
| ------------ | -------------------- | --------------------------- | -------- | ------- |
| `testSignal` | [ComponentCarrier]   | Modulated carrier           | No       |         |
| `freq`       | [Frequency]          | Carrier frequency           | No       |         |
| `bw`         | [Frequency]          | Occupied RF bandwidth       | No       |         |
| `level`      | [Power] or [Voltage] | Carrier level               | No       |         |
| `snr`        | [Ratio]              | Signal-to-noise Ratio       | No       |         |
| `papr`       | [Ratio]              | Peak-to-average power ratio | No       |         |
| `sweepFlag`  | Boolean              | Flag for enabling sweep     | Yes      | `false` |

### Examples


### See also

[ComponentCarrier][ComponentCarrier]  
[Frequency][Frequency]  
[Power][Power]  
[Ratio][Ratio]  
[Voltage][Voltage]  

[ComponentCarrier]: https://jekyllrb.com/docs/home
[Frequency]: https://jekyllrb.com/docs/home1
[Power]: https://jekyllrb.com/docs/home
[Ratio]: https://jekyllrb.com/docs/home
[Voltage]: https://jekyllrb.com/docs/home
