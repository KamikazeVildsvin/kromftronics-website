---
draft: false
toc: true
tocBorder: true
title: "The Kiss"
date: 2024-07-25
categories: 
  - "electronics"
  - "synthesizers"
tags: 
  - "alfa"
  - "as3320"
  - "as3340"
  - "as3360"
  - "envgen-8"
  - "monophonic"
  - "projects"
  - "synth-diy"
---

Ever since I started the journey of making electronic music some 10 years ago now, I've always been fascinated with how synthesizers and audio equipment work. This turned into the idea of making my own analog synthesizer, which I named "The Kiss" (I ended up giving it as a gift to my good friend by the nickname Kiss, hence the name). In this article, I will try to describe the design process and the errors I made along the way, which will hopefully serve as an inspiration for other people wanting to create their own analog synthesizer.

![](images/front-panel-left.jpeg)
![](images/front-panel-right-side.jpeg)

![](images/front-panel-switches.jpeg)
![](images/perfboard-as3340-and-mixer.jpeg)
![](images/backside-view-4122946550-e1726937027869.jpeg)

![](images/perfboard-as3360-and-envgen-3213277879-e1726937001645.jpeg)
![](images/perfboard-mixer-and-cv-with-labels.jpeg)

![](images/perfboard-psu.jpeg)
![](images/perfrboard-as3320-2165360096-e1726936927550.jpeg)

## Audio Examples

"Acid bass" example with plucky bass: 

{{< audio src="audio/the-kiss-example-acid-resonance.mp3" >}}

Filter sweep with sawtooth:

{{< audio src="audio/the-kiss-example-sawtooth-sweep.mp3" >}}

Envelope in free running mode:

{{< audio src="audio/the-kiss-example-free-running.mp3" >}}

Triangle wave lead with reverb:

{{< audio src="audio/the-kiss-example-lead.mp3" >}}

## Table of Content

1. [Audio Examples](https://kromftronics.com/2024/07/25/the-kiss/#audio-examples)
2. [Table of Content](https://kromftronics.com/2024/07/25/the-kiss/#table-of-content)
3. [Introduction](https://kromftronics.com/2024/07/25/the-kiss/#introduction)
4. [The VCO - AS3340](https://kromftronics.com/2024/07/25/the-kiss/#the-vco-as3340)
    1. [Improvement #1 - Fixing pitch drift with applied PWM](https://kromftronics.com/2024/07/25/the-kiss/#improvement-1-fixing-pitch-drift-with-applied-pwm)
    2. [Improvement #2 - Stable tuning and temperature compensation circuitry](https://kromftronics.com/2024/07/25/the-kiss/#improvement-2-stable-tuning-and-temperature-compensation-circuitry)
5. [The VCF - AS3320](https://kromftronics.com/2024/07/25/the-kiss/#the-vcf-as3320)
    1. [Modification compared to the datasheet](https://kromftronics.com/2024/07/25/the-kiss/#modification-compared-to-the-datasheet)
    2. [Frequency CV Summing](https://kromftronics.com/2024/07/25/the-kiss/#frequency-cv-summing)
6. [The VCA - AS3360](https://kromftronics.com/2024/07/25/the-kiss/#the-vca-as3360)
7. [Envelope/ADSR Generators - ENVGEN8](https://kromftronics.com/2024/07/25/the-kiss/#envelope-adsr-generators-envgen8)
    1. [Control Inputs & CV Potentiometer Circuit](https://kromftronics.com/2024/07/25/the-kiss/#control-inputs-cv-potentiometer-circuit)
    2. [VCA & VCF Envelope Control Voltage](https://kromftronics.com/2024/07/25/the-kiss/#vca-vcf-envelope-control-voltage)
    3. [Looping Modes & Time Scaling](https://kromftronics.com/2024/07/25/the-kiss/#looping-modes-time-scaling)
8. [Power Supply Unit](https://kromftronics.com/2024/07/25/the-kiss/#power-supply-unit)
9. [Conclusion](https://kromftronics.com/2024/07/25/the-kiss/#conclusion)

## Introduction

The idea started when I saw a video by [Look Mum No Computer](https://www.lookmumnocomputer.com/projects/#/cem-3340-diy-simple) (LMNC). He has been a great inspiration with all his crazy projects and DIY mentality. The video describes how to make a simple 1V/oct voltage controlled oscillator (VCO) using the CEM3340 chip (video below).

{{< youtube 4Kz8YopLTCQ >}}

By using the CEM3340 as the foundation for the synthesizer, creating my own seemed more realistic with the limited knowledge I had about analog circuits at that point in time.

One of the difficulties of making a synth was knowing were to start. I knew kinda what type of synth I wanted. A monophonic synth using the CEM3340 VCO plus a voltage controlled filter (VCF) and a voltage controlled amplifier (VCA), both of which had their own ADSR envelope generator. In other words, a very standard an classic analog monophonic synthesizer. The block diagram below shows the basic architecture I ended up with, consisting of the aforementioned VCO, VCF, and VCA (plus some ADSR's and some mixers).

<figure>

[![](images/thekissblockdiagram.drawio.png)](https://kromftronics.com/wp-content/uploads/2024/07/thekissblockdiagram.drawio.png)

<figcaption>

_Figure 1: The Kiss simplified block diagram_

</figcaption>

</figure>

Since this design was one of my first, and the fact that I also made the entire circuit on stripboard, I unfortunately don't have any detailed schematics or PCB files available (🥲). But I should be able to explain the important design choices for each part of the synthesizer for the reader to be able to create a synthesizer with similar results.

In the rest of this article, I am going to talk more about the individual parts of _The Kiss_, the VCO, the VCF, the VCA, and the envelope ADSR's. In each of the sections I am also going to discuss some of the problems that occurred when I had to design and test the synth, hopefully helping others avoid the mistakes I made ( ‾ʖ̫‾)

## The VCO - AS3340

Since the [CEM3340](https://nebula.wsimg.com/1c34939ca17fdcf07c8ceee4661ba253?AccessKeyId=E68C2B1C2930EF53D3A4&disposition=0&alloworigin=1) chip is quite hard to find, and also expensive, the cheaper [AS3340](https://alfarzpp.lv/eng/sc/AS3340.php) showed up as an alternative. A quick search around the internet for suppliers of the AS3340 led to [Electric Druid](https://electricdruid.net/), which I discorvered as a great webshop and resource for synth DIY stuff. Electric Druid also has a lot of other CEM chip clones from Alfa, including a VCF ([AS3320](https://alfarzpp.lv/eng/sc/AS3320.php)) and a VCA ([AS3360](https://alfarzpp.lv/eng/sc/AS3360.php)).

A block diagram of the AS3340 chip and its associated pins can be seen in the figure below. The cool thing about this chip is that it contains three different classic oscillator waveforms in one single chip. A Triangle wave at pin 10, a sawtooth wave at pin 8, and pulse wave at pin 4. The frequency of the oscillator is controlled with 1V/oct control voltage, by applying the control voltage to one of the 100k resistors that are connected to pin 15.

<figure>

![](images/as3340-blockdiagram-4068132025-e1714589842511.png)

<figcaption>

_Figure 2: AS3340 block diagram – source: AS3340 datasheet_

</figcaption>

</figure>

The chip is also temperature compensated, which means that the tuning of the oscillator is pretty stable across different ambient temperatures. This is usually something that is very difficult with analog oscillators. All in all, this chip makes it a lot easier to design a synthesizer if you are not that experienced.

The datasheet example in the figure above could almost be used right out of the box. The three oscillator waveforms could be routed directly to an op-amp mixing circuit without much hassle. But, I wanted to be able to use a standard Eurorack power supply, which meant that I had to modify the circuit from using a +/-15V supply voltage to +/-12V. This meant resistors connected to the +/-15V supplies needed to be scaled down to prove the correct current. But, instead of giving the exact numbers here, I am going to describe two "upgrades" to the datasheet circuit that I implemented in order for the circuit to function correctly and adequate.

#### Improvement #1 - Fixing pitch drift with applied PWM

A common issue with the datasheet circuit, where the negative supply is -15V, is a noticeable pitch shift of the VCO when Pulse Width Modulation (PWM) is applied to pin 5. I fixed this problem by having a local -5V regulated supply (like the 79L05 IC) at pin 3, which also means that the current limiting resistor REE can be omitted. Having a regulated -5V supply is also recommend by Rob Hordijk in his [article](https://alfarzpp.lv/eng/sc/Tuning%20the%20AS3340.pdf), which will be discussed in improvement #2.

#### Improvement #2 - Stable tuning and temperature compensation circuitry

In the original AS3340 datasheet circuit, only one trimming potentiometer in RZ is used to "tune" the VCO, which makes the tuning process of the VCO very cumbersome and not as stable as it could be.

Luckily, Rob Hordijk has made an excellent [article](https://alfarzpp.lv/eng/sc/Tuning%20the%20AS3340.pdf) which describes more in-depth how the insides of the AS3340 works and suggests a different calibration circuit with more trim-pots, which makes the tuning and temperature compensation more efficient. The circuit and the calibration process can be seen below in figure 3, which is what I ended up using in the design of The Kiss. With this circuit, the PWM drifting is removed and the calibration of the AS3340 is a lot more systematical! (YAY) The only down-side is more trim-pots.

<figure>

[![](images/as3340-tuning-circuit-3098683545-e1721921110189.png)](https://kromftronics.com/wp-content/uploads/2023/04/as3340-tuning-circuit-3098683545-e1721921110189.png)

<figcaption>

_Figure 3: Improved AS3340 calibration circuit by Rob Hordijk - source: article by Rob_

</figcaption>

</figure>

As described in the [article](https://alfarzpp.lv/eng/sc/Tuning%20the%20AS3340.pdf) by Rob, the modified calibration circuitry affects the core of the AS3340 that is responsible for the temperature compensation and the 1V/oct control voltage. Figure 4, which represents the core of the AS3340, shows a band gap reference, an analog multiplier (known as a Gilbert Cell), and a classic exponential converter. The combination of the three is what generates a temperature stable 1V/oct VCO.

NOTE: It is not important to understand how all of this circuitry works in order to be able to use the chip! This is just "nice-to-know", in case you would like to have a better understanding of what is happening inside the chip, if you potentially want to be able to make your own discrete VCO some day.

<figure>

[![](images/as3340-tempco-expo-core.png)](https://kromftronics.com/wp-content/uploads/2023/04/as3340-tempco-expo-core.png)

<figcaption>

_Figure 4: AS3340 core, consisting of a band gap reference, an analog multiplier, and an exponential converter - source: AS3340 datasheet_

</figcaption>

</figure>

If you are interested in getting a better understanding of the inner workings of the chip, how the exponential converter works, and WHY temperature is a big problem in analog VCO's, then I would recommend you check out the video below by Professor Aaron Lanterman. All of his videos are gold and has help me a lot in getting a better understanding of how analog synthesizer circuits work.

https://www.youtube.com/watch?v=ZWJhApUmfEU

For the VERY nerdy readers, who REALLY wants to understand the exponential converter and how the analog multiplier can "fix" the temperature drift, I can highly recommend the article ["Thermal Compensation of Analog Exponential Converters"](http://www.openmusiclabs.com/files/expotemp.pdf) by Open Music Labs (check out section VIII). It does involve some math, but it gets around a lot of different exponential converter circuits.

## The VCF - AS3320

The VCO was the hardest part debugging and getting right, which is why I spend SO much time describing it. But now is the time for an equally important part of the synthesizer. The VCF!

The filter in The Kiss is a classic 24dB/oct lowpass filter with resonance control (all the way into self-oscillation) implemented using the [AS3320](https://alfarzpp.lv/eng/sc/AS3320.php) chip from Alfa. As mentioned earlier in the article, Alfa has made clones of most of the original CEM chips, including the [CEM3320](https://nebula.wsimg.com/f89efadae26b3ee39839dc6b51665370?AccessKeyId=E68C2B1C2930EF53D3A4&disposition=0&alloworigin=1), which is a four-pole VCF that has been used in synths like the Sequential Circuits Pro-One and the Oberheim OB-Xa (see the following article by [Electric Druid](https://electricdruid.net/cem3320-filter-designs/)).

<figure>

[![](images/as3320-datasheet-lowpass-1.png)](https://kromftronics.com/wp-content/uploads/2024/07/as3320-datasheet-lowpass-1.png)

<figcaption>

_Figure 5: The AS3320 datasheet 24dB/oct lowpass filter from the application note - source: AS3320 datasheet_

</figcaption>

</figure>

Figure 5 above shows the datasheet 24dB/oct lowpass filter. It looks quite overwhelming, but in essence, it is four identical 6dB/oct voltage-controlled lowpass filter stages connected in series with the output of the last stage being fed back into the first stage to create the resonance. The amount of resonance in the filter can be controlled by the internal feedback VCA (named RES VCA in figure 5), where the gain of the feedback VCA is determined by the input current on pin 9. Each lowpass filter consist of one variable gain amplifier (∆A), an external capacitor, and a buffer (B). For more details on how all of these different elements works together, the original [CEM3320](https://nebula.wsimg.com/f89efadae26b3ee39839dc6b51665370?AccessKeyId=E68C2B1C2930EF53D3A4&disposition=0&alloworigin=1) datasheet is a great resource in terms of understanding how the exponential converter, the variable gain amplifiers, and the capacitors "creates" a voltage-controlled filter. The datasheet also derives the equations that describes the operation of the filter. To get a basic understanding of the operation of the circuit, I'm going to give a short summary of the essential equations from the datasheet.

The cutoff frequency of the filter fc is controlled by the voltage at pin 12 (FREQ CV) which expects a 60mV/decade control voltage or 18mV/octave. The reason can be found in the definition of the cutoff frequency derived in the datasheet which can be expressed as

$$f\_{c} = \\frac{A\_{IO}}{2\\pi\\cdot R\_{EQ}\\,C\_P}\\cdot e^{-V\_{\\textit{PIN12}}/26\\textnormal{mV}}$$,

which for the resistors in figure 5 and the typical operating conditions simplifies to

$latex {f\_{c} \\approx \\frac{0.9}{2\\pi\\cdot 91\\textnormal{k}\\Omega\\cdot C\_P}\\cdot e^{-V\_{\\textit{PIN12}}/26\\textnormal{mV}}} &bg=000000&fg=ffffff&s=1$,

where $latex C\_P &bg=000000&fg=ffffff$ is the value of the capacitors connected to pin 4, 5, 11, and 16. The typical standard in control voltage for synthesizers is 1V/octave, which is why the 100kΩ and the 1k8Ω resistive divider are put before pin 12 since this converts a 1V change to an 18mV change:

$latex {V\_{\\textit{PIN12}} = \\frac{1\\textnormal{k}8}{100\\textnormal{k}+1\\textnormal{k}8}\\cdot1\\textnormal{V} \\approx 17.68\\textnormal{mV}} &bg=000000&fg=ffffff&s=1$.

Adding 18mV to $latex V\_{\\textit{PIN12} &bg=000000&fg=ffffff}$ halves the cutoff frequency because of the negative exponential function, which allow us to get the 1V/oct scaling. So it all makes sense.

#### Modification compared to the datasheet

All in all, this datasheet circuit just worked out of the box when I did some testing on a breadboard, but I wanted the range of the cutoff filter to be a bit more "bright". This was achieved by scaling the four 300pF filter capacitors CP down to 150pF, effectively increasing the baseline cutoff frequency by an octave as indicated by the cutoff frequency equation above. I also added a buffer between the SIGNAL OUTPUT and the resonance feedback (pin 8), since this was suggested by the [Electric Druid](https://electricdruid.net/cem3320-filter-designs/) article to improve the frequency response of the filter at lower frequencies. And it is never a bad idea to add a buffer if you have an extra opamp lying around ( ‾ ʖ̫ ‾)

#### Frequency CV Summing

As described above, the cutoff frequency of the filter can be controlled by applying a control voltage to pin 12 of the AS3320. The easy way to achieve a controllable voltage is by connecting a potentiometer between +12V and ground, but, as indicated by figure 1 at the start of this article, both the VCF and the VCA can be controlled by an ADSR envelope generator which also needs to be added somehow. So to be able to control the cutoff frequency with both a potentiometer and the envelope generator, I designed a frequency CV summing circuit, which can seen below in figure 6 with figure 7 showing a picture of the actual stripboard circuit.

<figure>

![](images/the-kiss-freq-cv-mixer.drawio.png)

<figcaption>

_Figure 6: Schematic of the frequency CV summing circuit for The Kiss._

</figcaption>

</figure>

The circuit is an inverting opamp mixer with a diode in the feedback path, which makes sure that the output voltage doesn't go lower than one diode drop (400mV to 700mV depending on the diode) since the AS3320 doesn't "like" large negative voltages on PIN 12. The 100kΩ resistor connected to -12V sets the output offset so when both the frequency potentiometer and envelope voltages are 0V, the output is at 12V. This sets the lowest cutoff frequency of the filter. The 51kΩ resistors that connect to the frequency potentiometer and the envelope voltage create a gain of -2 which seemed to be sufficient for a nice response and feel.

You might wonder why the circuit is inverting the control voltage from the potentiometer and the envelope generator. The reason is that the FREQ CV input on the AS3320 actually expects an inverted input, meaning that a drop of 1V on the FREQ CV input results in a rise in cutoff frequency of one octave due to the minus sign in the exponential conversion in the equation for the cutoff frequency which was described above.

<figure>

![](images/perfboard-mixer-and-cv-with-labels.jpeg)

<figcaption>

_Figure 7: picture of the stripboard circuit for the frequency CV summing ciruit and the waveform mixer._

</figcaption>

</figure>

But there you go, that is essentially the VCF! The ASS3320 made it very easy to construct a nice sound 24dB/oct analog lowpass filter without having to deal with the tedious process of matching discrete transistor and temperature compensation.

## The VCA - AS3360

The last part of the signal path in The Kiss is the VCA, again, implemented using a chip from Alfa, the [AS3360](https://alfarzpp.lv/eng/sc/AS3360.pdf), which can be seen below in figure 8 (you get the trend). The AS3360 is a dual VCA chip which have the possibility to control the gain with a linear or exponential scaling by using a build-in linear to logarithmic converter.

<figure>

![](images/as3360-datasheet-2.png)

<figcaption>

_Figure 8: Block diagram of the AS3360 dual VCA - source: AS3360 datasheet_

</figcaption>

</figure>

The AS3360 is a current-in current-out VCA, which means that the input voltage needs to be converted to a current with a resistor and the output of the VCA needs to be converted to a voltage by dumping the current into a resistor or a [transimpedance amplifier](https://en.wikipedia.org/wiki/Transimpedance_amplifier) (TIA). The implementation used in The Kiss is a 47kΩ input resistor and an output TIA with the same 47kΩ resistor in the feedback path, which is the configuration used for channel one in figure 8. The TIA keeps the output voltage of the VCA constant and buffers the output which provides a low impedance output for The Kiss. Super simple and not a lot of external components!

The linear control input was used together with a ENVGEN8 ADSR envelope generator to control the amplitude of the VCA, which will be the next topic.

## Envelope/ADSR Generators - ENVGEN8

You can't design a classic monophonic synthesizer without some sort of envelope generator. I chose to design The Kiss with two ADSR envelope generators; an optional envelope generator for the VCF cutoff frequency, and a hardwired envelope generator for the output VCA gain (as indicated by the block diagram in figure 1). Instead of using an Alfa clone, like I have used in the rest of the synth (I know, wild!), I came across an interesting IC designed by Electric Druid; the [ENVGEN8](https://electricdruid.net/product/envgen8/). In this section, I'm going to explain how the ENVGEN8 functions and how I used the ENVGEN8 in The Kiss to control the VCA and VCF, but I would recommend that you take a look at the [datasheet](https://electricdruid.net/datasheets/ENVGEN8Datasheet.pdf) as well, since you can find more details and goodies that I'm not covering.

<figure>

![](images/perfboard-as3360-and-envgen-1.jpeg)

<figcaption>

_Figure 9: ENVGEN8, AS3360, and a opamp chip serving as the TIA for the VCA._

</figcaption>

</figure>

The pinout of the chip can be seen below in figure 10. The ENVGEN8 is a digital envelope generator chip that does the classic ADSR envelope thing with controllable attack, decay, sustain, and release, BUT, it has some extra features that the analog Alfa ADSR envelope generator ([AS3310](https://alfarzpp.lv/eng/sc/AS3310.pdf)) doesn't have, like time scaling and loop/LFO modes (this will be covered below). It also only needs one +5V supply, which is nice compared to all of the other dual-rail chips used in the rest of The Kiss.

<figure>

![](images/envgen8-pinout.png?w=1024)

<figcaption>

_Figure 10: pinout of the ENVGEN8 - source: ENVGEN8 datasheet_

</figcaption>

</figure>

The envelope output can be triggered by setting the input signal on the "TRIGGER" or "GATE" to +5V. The difference between the two is that the gate signal let's you settle on the sustain level specified by the CV on the "SUSTAIN CV" input if the gate signal is kept high. In contrast, the trigger signal only triggers the attack/decay envelope. For The Kiss, I tied the trigger and the gate signal together, which is the same as only using the gate signal. I found this to be the best use of the envelope generator for my taste.

#### Control Inputs & CV Potentiometer Circuit

All of the pins labeled “CV” expect a voltage between 0V and 5V. These voltages can be controlled with the circuit in Figure 11, which creates a variable voltage between 0V and +5V that is lowpass filtered by the 1kΩ resistor and 100nF capacitor before reaching the input on the ENVGEN8. The lowpass filtering is added to remove noise and glitches that could appear from a noisy +5V supply and was suggested in the datasheet of the ENVGEN8.

<figure>

![](images/envgen8-cv-circuit.drawio.png)

<figcaption>

_Figure 11: CV circuit used for the ENVGEN8_

</figcaption>

</figure>

A total of five of these CV circuits were implemented in The Kiss. One for each of the ADSR CV’s and one for the “TIME CV”. The attack, decay, and release CV inputs set the time scale between 1ms and 10s in combination with the time CV, which is a quite large range to work with. The “LEVEL CV” signal was tied to +5V since I wanted to use the full range of the envelope to control either the VCF or the VCA. The “MODE CV” input will described in one of the following subsections ( ‾ ʖ̫ ‾)

#### VCA & VCF Envelope Control Voltage

As shown above in the block diagram of the AS3360 VCA in figure 8, the linear control voltage input expects a voltage between 0-2V to get the minimum and maximum gain. Since the output of the ENVGEN8 goes between 0-5V, I added a simple resistive divider like the one shown below in figure 12. Since the VCA was configured to use the linear control input, the ENVGEN8 was configured with exponential envelope shapes. This is done by leaving pin 4 on the chip unconnected.

<figure>

![](images/envgen8-output-scaling.drawio.png)

<figcaption>

_Figure 12 Voltage divider used to scale the 0-5V output down to the 0-2V range needed by the VCA_.

</figcaption>

</figure>

For the VCF, the 0-5V output of the ENVGEN8 was routed without attenuation directly to the frequency CV summing circuit, since the frequency CV summing circuit in figure 6 already scales the voltage appropriately for the VCF.

#### Looping Modes & Time Scaling

What sets the ENVGEN8 apart from a standard ADSR envelope is that it has three different modes of operation and a time scaling feature that scales all of the attack, decay, and release times by factor between 1x and 0.01x. These three envelope modes are quite powerful and essentially allows you to turn your ADSR envelope generator into an LFO! The three different modes are visualized below in figure 13, where the gate input control signal is shown in the top row and the equivalent output envelope voltage is shown in the bottom row.

<figure>

![](images/envgen8-modes.drawio-1.png)

<figcaption>

_Figure 13: The three different modes for the ENVGEN8_

</figcaption>

</figure>

The three different can be summarized as follows:

- Normal ADSR Mode:
    - The gate signal triggers an attack/decay envelope, settling on the sustain level until the gate signals goes low, at which the release phase starts.

- Gated Loop Mode:
    - When the gate signal is high, the ADSR routine keeps repeating until the gate signal goes low again, which starts the release phase.

- LFO/Looping Mode:
    - The gate signal is disregarded completely and the ADSR repeats forever, which turns it into a pseudo-LFO where the wave shape is controlled by the ADR duration.

The time scaling CV also works in all of these modes, which means that it can be seen as a sort of "LFO rate" when the ENVGEN8 is in one of the looping modes. Very handy! This is also what you heard in the audio example in the start of the articled named "free running mode".

The three different modes are selected by using the circuit in figure 14 below. An on-off-on switch and two resistors is used to generate 5V, 2.5V and 0V, which are the voltage levels that tells the ENVGEN8 which mode it should be in. You could actually have used the same continuous CV circuit in figure 10 that the rest of the CV inputs used. But, having a switch seemed to be a more "clean" solution. This was also the circuit that the ENVGEN8 datasheet suggested as the solution.

<figure>

![](images/envgen8-mode-cv.drawio.png)

<figcaption>

_figure 14: Mode CV switching circuit._

</figcaption>

</figure>

So that is basically the ENVGEN8 ADSR chip! Again, quite easy to use with only a few external components and potentiometers. And now for the grand finale ( ‾ ʖ̫ ‾)

## Power Supply Unit

The final (and more boring) part of The Kiss is the power supply unit (PSU). This is not the most "sexy" and exciting part of building a synthesizer, but is still very critical in order to get a good-performing and clean synthesizer. If you have a noisy PSU, you will probably end up with a noisy synthesizer, which of course can be a "feature" if you like noisy unstable synthesizers ( ‾ ʖ̫ ‾)

The PSU for The Kiss can be seen below in Figure 15 which is a stripboard implementation of the [Frequency Central FC Power](https://frequencycentral.co.uk/wp-content/uploads/FC-Power-Build-Doc.pdf) PSU with slight modifications. It generates +12V, -12V, and +5V regulated voltages which are used as the supplies for the rest of the circuitry. The three supplies are generated by rectifying and filtering a 12VAC input supply. You can buy these 12VAC wall wart supplies online or in most hobby electronics stores.

<figure>

![](images/perfboard-psu.jpeg)

<figcaption>

_Figure 15: The Kiss power supply unit, +/-12V & +5V_

</figcaption>

</figure>

The PSU is acctually just an Eurorack PSU, which is why I designed The Kiss to use +/-12V instead of the +/-15V that the datasheet specified for the VCO, VCF, and VCA chips. This means that in theory I could have used a 16 pin Eurorack supply that includes a +5V supply (notice the ribbon cable and IDC connector in the PSU below). I went for the discrete stripboard version to save time and money and also learn a little bit along the way. If you don't want to bother making a PSU yourself, you could probally get away with buying an Eurorack PSU instead.

## Conclusion

It may not be the most innovative synthesizer made in history, but the fact that I have my it myself (and that it works) makes me very happy! The process of using stripboard was not the most easy affair, which is also why I started to use KiCad in all of my newer projects. But using the Alfa chips made the process much easier!

To me, the analog sound of the oscillators and the filter is something very special. Even with the basic combination of a VCO, a VCF, and a VCA (plus the envelope generators), you actually have a very powerful and inspiring synthesizer to play with. And since I did it myself, the price for an analog synthesizer wasn't that big.

Hopefully, this article have been useful in helping you getting a better starting point for building your own monophonic synthesizer. If you have any comments, question, or feedback, don't hesitate to reach out or leave a comment ( ‾ ʖ̫ ‾)
