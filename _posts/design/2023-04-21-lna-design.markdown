---
title:  "LNA Design"
date:   2023-04-22 20:03:35 -0700
layout: post
categories: examples
slug: "lna-design"
permalink: "/design/:categories/:slug/"
---

This example shows how to use Radio builder to derive specifications for WiFi low-noise amplifier (LNA). Radio builder is used to<!--more-->:
- Explore the design space
- Choose target specifications
- Verify target specifications are met by performing a power sweep

<!--* TOC
{:toc}-->

#### Design Constraints

In this example, our constraints are the desired LNA gain and transmitter signal quality (TX EVM). We use typical values for a state-of-the-art WiFi system

{% highlight matlab %}
%--- Design constraints (FEM typical values)
lnaGain = Ratio(16,'db');           % LNA max gain
txEVM = Ratio(-40,'db');            % TX signal EVM
{% endhighlight %}


#### Design Sweeps

Next, we set up design sweeps to explore the design space:
- Power sweep covers a typical sensitivity level (SENS) to maximum input level (MIL) range for 20MHz to 160MHz channel bandwidths. SENS and MIL are defined by the 802.11 standard at 10% packet-error rate (PER).

- Total SNR sweep covers typical MCS7 to MCS11 values at SENS level, plus some fading margin. Total SNR is the SNR eventually observed on the receiver side, including both TX and RX noise.

{% highlight matlab %}
totalSNR = Ratio(26:3:38,'db');     % Total SNR sweep (MCS7-11, plus fading margin)
level = Power(-61:6:-7,'dbm');      % Signal power sweep (20-160MHz SENS to MIL)
{% endhighlight %}

The desired LNA SNR is then calculated as the difference between total SNR and TX EVM

{% highlight matlab %}
lnaSNR = Ratio(-10*log10(10.^(-totalSNR.getValue/10) - 10^(+txEVM.getValue/10)), 'db');
{% endhighlight %}


#### Test Case

We are now ready to define our test case. A test case can include multiple signals (for example, a desired signal and a blocker), but in this example, the design is based on a single channel scenario. The carrier defined below corresponds to WiFi channel 52

{% highlight matlab %}
%--- Test signal (channel 52)
signalFreq = Frequency(5.25e9,'hz');
signalBW = Frequency(160e6,'hz');
signalPower = level;
signalSNR = Ratio(-txEVM.getValue,'db');
signalPAPR = Ratio(12,'db');
testSignal = ComponentCarrier(signalFreq, signalBW, signalPower, signalSNR, signalPAPR);

%--- Test case
testIn = Test(testSignal);
{% endhighlight %}


#### Design Budget

Next, we partition SNR across the different RF impairments. In this example, only NF and IIP3 are considered, so we budget for thermal noise and intermodulation distortion. We identify two partitioning cases:

- Toughest (minimum) NF requirement is at low signal levels. At low signal levels, distortion is negligible, so we allocate 100% of the budget to thermal noise.

- Toughest (maximum) IIP3 requirement is at high signal levels. At high signal levels, thermal noise contribution to the overall SNR is negligible, so we allocate 100% of the budget to third-order intermodulation distortion.

These two partitioning cases are represented as follows:

{% highlight matlab %}
impairmentBudget.thermalNoise = Ratio([100,nan],'%');
impairmentBudget.intermod3 = Ratio([nan,100],'%');
{% endhighlight %}

The overall impairment budget is then established based on the LNA SNR and the above partitioning. Since the LNA SNR is a swept variable, we would like to evaluate our partitioning at every point of the sweep. In order to do that, we set the `sweepFlag` to `1`

{% highlight matlab %}
budget = Budget.fromSNR(lnaSNR, impairmentBudget, 1);
{% endhighlight %}

As will be shown [later](#exploring-the-design-space), we can filter the results based on different criteria to look at different subsets of the full sweep.

#### Design Scenario

Finally, the test case and budget are combined into a design scenario. Again, since both `test` and `budget` are swept, we would like to explore all possible combinations of the two, so we set the `sweepFlag` to `1`

{% highlight matlab %}
scenario = Scenario(testIn, budget, 1);
{% endhighlight %}

`scenario` now defines a three-dimensional sweep: signal level versus LNA SNR versus partition budget


#### Exploring the Design Space <a id="exploring-the-design-space"></a>

We can now explore the LNA design space by calculating the amplifier specifications from `scenario`. The code line below provides one set of LNA specifications for every point of the three-dimensional sweep defined earlier

{% highlight matlab %}
lnaSweep = Amplifier.fromScenario(scenario, lnaGain);
{% endhighlight %}

The first subset of the results we explore is the NF and IIP3 as a function of signal level for a given total SNR. For example, the code below extracts the results for total SNR = 32 dB

{% highlight matlab %}
[~,idx] = scenario.getBudget.select('total',lnaSNR(3),'thermalNoise',lnaSNR(3));
nf_vs_power_snr32db = lnaSweep(idx).getSpecs.getNF.convertTo('db');

[~,idx] = scenario.getBudget.select('total',lnaSNR(3),'intermod3',lnaSNR(3));
iip3_vs_power_snr32db = lnaSweep(idx).getSpecs.getIIP3.convertTo('dbm');
{% endhighlight %}

Fig. 1 shows the NF as a function of signal level, for two cases of total SNR, 26dB and 32dB, roughly corresponding to MCS7 and MCS11 SENS, respectively. As expected, the NF requirement is toughest for the lowest signal levels (highest sensitivity requirement) and increasing the total SNR target from 26dB to 32dB for the same SENS reduces the NF required by about 6dB. In this example, the difference is not exactly 6dB because of the TX EVM contribution to the total SNR budget.

<div class="img-container">
  <div class="img-html" style="--size: 43%;">
    <figure>
      <img src="{{ site.baseurl }}/assets/img/lna_design_nf_vs_pin.png" alt="LNA design: NF vs input power">
      <figcaption>LNA NF versus input signal level for different total SNR values</figcaption>
    </figure>
  </div>
</div>

Similarly, Fig. 2 shows the IIP3 as a function of signal level, for the same two values of total SNR. In this case, the IIP3 requirement is toughest for the highest signal levels (higher linearity requirement). As expected, increasing the total SNR target by 6dB increases the IIP3 required by about 3dB. Again, the difference is not exactly 3dB because of the TX EVM is not zero.

<div class="img-container">  
  <div class="img-html" style="--size: 43%;">
    <figure>  
      <img src="{{ site.baseurl }}/assets/img/lna_design_iip3_vs_pin.png" alt="LNA design: IIP3 vs input power">
      <figcaption>LNA NF versus input signal level</figcaption>
    </figure>
  </div>
</div>

Alternatively, we can extract the NF and IIP3 as a function of SNR for a given signal level. For instance, the code below extracts the requirements for signal levels of -55dBm and -10dBm, respectively

{% highlight matlab %}
[~,idx] = scenario.getTest.getSignals.select('level',level(3));
lnaTemp = lnaSweep(idx);
[~,idx] = scenario(idx).getBudget.select('thermalNoise',lnaSNR);
nf_vs_snr_pinm55dbm = lnaTemp(idx).getSpecs.getNF.convertTo('db');

[~,idx] = scenario.getTest.getSignals.select('level',level(18));
lnaTemp = lnaSweep(idx);
[~,idx] = scenario(idx).getBudget.select('intermod3',lnaSNR);
iip3_vs_snr_pinm10dbm = lnaTemp(idx).getSpecs.getIIP3.convertTo('dbm');
{% endhighlight %}

In this case, Fig. 3 and 4 show that higher SNR requires lower NF and higher IIP3, an expected result. At too high an SNR, the required NF is less than unity (negative dB), which, of course, is not feasible.

<div class="img-container">
  <div class="img-html" style="--size: 43%;">
    <figure>
      <img src="{{ site.baseurl }}/assets/img/lna_design_nf_vs_snr.png" alt="LNA design: NF vs SNR">
      <figcaption>LNA NF versus total SNR</figcaption>
    </figure>
  </div>
</div>

<div class="img-container">
  <div class="img-html" style="--size: 43%;">
    <figure>
      <img src="{{ site.baseurl }}/assets/img/lna_design_iip3_vs_snr.png" alt="LNA design: IIP3 vs SNR">
      <figcaption>LNA IIP3 versus total SNR</figcaption>
    </figure>
  </div>
</div>


#### Setting LNA Target Specifications

From the above results, we choose the target SNR and SENS specifications to meet the toughest mode (MCS11). For MIL, the 802.11 standard specifies a minimum of -20dBm. Typically, a significantly better MIL is targeted.

The following are our target specifications:
- Total SNR = 32dB
- SENS = -55dBm
- MIL = -13dBm

{% highlight matlab %}
% Total SNR = 32dB & SENS = -55dBm
[~,idx] = scenario.getBudget.select('total',lnaSNR(4),'thermalNoise',lnaSNR(4));
lnaSens = lnaSweep(idx);
[~,idx] = scenario(idx).getTest.getSignals.select('level',level(3));
lnaSens = lnaSens(idx);

% Total SNR = 32dB & MIL = -55dBm
[~,idx] = scenario.getBudget.select('total',lnaSNR(4),'intermod3',lnaSNR(4));
lnaMil = lnaSweep(idx);
[~,idx] = scenario(idx).getTest.getSignals.select('level',level(12));
lnaMil = lnaMil(idx);
{% endhighlight %}

The above represents two LNA designs, the first `lnaSens` is designed for NF and the second `lnaMil` is designed for IIP3. We can print out the specs of both designs

{% highlight text %}
lnaSens.printOut()
lnaMil.printOut()
{% endhighlight %}

<div class="language-matlab matlab-printout">  
 Gain [ dB]:    16.0
   NF [ dB]:     4.0
Pi1dB [dBm]:     Inf
 IIP3 [dBm]:     Inf
 IIP2 [dBm]:     Inf

 Gain [ dB]:    16.0
   NF [ dB]:    -Inf
Pi1dB [dBm]:    -8.6
 IIP3 [dBm]:     1.0
 IIP2 [dBm]:     Inf
</div>

The final LNA design is obtained by combining the two designs using `combine()`

{% highlight matlab %}
lna = [lnaSens; lnaMil];
lna = lna.combine();
{% endhighlight %}

To verify, we can print out the specs of the final design

{% highlight text %}
lna.printOut()
{% endhighlight %}

<div class="language-matlab matlab-printout">  
 Gain [ dB]:    16.0
   NF [ dB]:     4.0
Pi1dB [dBm]:    -8.6
 IIP3 [dBm]:     1.0
 IIP2 [dBm]:     Inf
</div>


#### Power Sweep

To verify the design meets target specifications, we run a power sweep

{% highlight matlab %}
testOut = lna.process(testIn);
{% endhighlight %}

From `testIn` and `testOut` we can extract the input and output power levels, respectively, and plot the LNA gain (output-to-input power ratio) versus input power (Fig. 5). The LNA meets the 1-dB compression point target spec of -8.6dBm.

{% highlight matlab %}
pin = signalIn.getLevel.convertTo('dbm');
pout = signalOut.getLevel.convertTo('dbm');
{% endhighlight %}

<div class="img-container">
  <div class="img-html" style="--size: 43%;">
    <figure>
      <img src="{{ site.baseurl }}/assets/img/lna_design_gain_vs_pin_160M.png" alt="LNA design: gain vs. input power 160MHz">
      <figcaption>LNA gain versus input signal level</figcaption>
    </figure>
  </div>
</div>

Next, we extract the total SNR of the output signal, as well as the thermal noise and intermodulation distortion individual contributions

{% highlight matlab %}
snrTotal = signalOut.getSNR.convertTo('db');
snrThermalNoise = testOut.getSNR('thermalNoise').convertTo('db');
sd3r = testOut.getSNR('intermod3').convertTo('db');
{% endhighlight %}

Fig. 6 shows that the total SNR meets the target spec of 32dB at the desired SENS (-55dBm) and MIL (-13dBm). The asymptotic lines representing the individual contributors to the total noise floor show which impairments dominate at which region:
- At low power levels, the total SNR is dominated by thermal noise. The asymptotic slope in this region is 1dB/dB

- At high power levels, the total SNR is dominated by distortion. The asymptotic slope in this region is 2dB/dB

- At intermediate power levels, the total SNR hits a plateau (SNR ceiling) determined by the TX EVM (-40dB).

The difference between the minimum SNR (32dB) and the SNR ceiling (40dB) is the fading margin.

<div class="img-container">
  <div class="img-html" style="--size: 43%;">
    <figure>
      <img src="{{ site.baseurl }}/assets/img/lna_design_snr_vs_pin_160M.png" alt="LNA design: SNR vs. input power 160MHz">
      <figcaption>Total SNR at LNA output versus input signal level</figcaption>
    </figure>
  </div>
</div>

We also run a power sweep for a 20MHz bandwidth channel. Fig 7. shows the total SNR of the 20MHz channel compared to the earlier 160MHz channel result from Fig.6. At low power levels, the 20MHz channel shows better SNR, while the SNR ceiling and MIL remain unchanged.

<div class="img-container">
  <div class="img-html" style="--size: 43%;">
    <figure>
      <img src="{{ site.baseurl }}/assets/img/lna_design_snr_vs_pin_20M_160M.png" alt="LNA design: SNR vs. input power 20MHz and 160MHz">
      <figcaption>Total SNR at LNA output versus input signal level for different channel bandwidths</figcaption>
    </figure>
  </div>
</div>

### See also
[Amplifier][amplifier]  
[Budget][budget]  
[ComponentCarrier][component-carrier]  
[Scenario][scenario]  
[Test][test]  

[amplifier]: {{site.baseurl}}{%post_url /help/2023-04-28-amplifier %}  
[budget]: {{site.baseurl}}{%post_url /help/2023-04-28-budget %}  
[component-carrier]: {{site.baseurl}}{%post_url /help/2023-04-28-component-carrier %}  
[scenario]: {{site.baseurl}}{%post_url /help/2023-04-28-scenario %}  
[test]: {{site.baseurl}}{%post_url /help/2023-04-28-test %}  


<!-- You can also add images with markdown like this:

![test]({{ site.url }}/assets/img/test.jpg){:.img-css}
![test]({{ site.url }}/assets/img/test_dark.png){:.img-css} -->
