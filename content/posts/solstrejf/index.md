---
draft: true
math: true
layout: 'single'
title: 'Solstrejf'
date: '2025-08-13'
categories:
    - 'electronics'
    - 'synthesizers'
tags:
    - 'drone'
    - 'modulation-matrix'
    - 'fm'
    - 'frequency-modulation'
    - 'four-voice'
    - 'sinewave'
    - 'oscillators'
featured_image: '/images/solstrejf/cover-contrast.jpg'
--- 
Can you imaging what four sinewave oscillators would sound like, if they cross modulate the amplitude, pitch, and pan of each other?
I couldn't, until I finished my latest DIY synthesizer project --- 🌞 **Solstrejf** 🌞.

Solstrejf, which roughly translates from Danish to "sun streak" or "sun touch", is a four-voice analog sinewave drone oscillator that I designed and build from scratch.
I was on a hike in Sweden, out near a beautiful forest lake just around the break of dawn. 
The way the sun interacted with nature gave me a real sense of calm and ease. 
In that moment, I thought, "what if I could create an instrument that would be able to replicate this feeling"? 
A sine wave drone synthesizer came to my mind, similar to the early Musique Concrète test equipment techniques.

{{< snap-gallery
    src="images/solstrejf/intro-gallery"
    mode=slideshow
    slideshowfitcontent=false
    maxwidth=640px
>}}

In this article, I'll be describing the ideas behind the design, and the different building blocks that makes up Solstrejf.
As always, all of the design files are available (see [this]({{< relref "#schematic-pcb--simulation-files" >}}) section below)

## Table of Contents

{{< custom-toc >}}

## Audio Examples
TODO

## Introduction

I wanted the instrument to be playful and interactive. 
Four sine wave oscillators in itself are maybe not that interesting and can become static. 
A modulation matrix, which would allow for all four voices to modulate parameters like frequency and amplitude on all other voices would create more complex soundscapes. 

{{< figure 
    src="/images/solstrejf/blockdiagrams/blokdiagram-solstrejf.jpeg" 
    caption="Figure 1: hand drawing of the initial Solstrejf block diagram" 
    width=480px
>}}

The basic idea and block diagram can be seen above in figure 1, where four identical voice boards are connected to the mod-matrix board and a power supply/mixer board for output summation.

## Schematic, PCB, & Simulation Files
All of simulation files, schematics, and PCB files can be found on my [GitHub](https://github.com/KamikazeVildsvin/Solstrejf).
All of the files that you need in order to produce PCB's for this design can be found there.

Basically, the I decided to split the design four different PCBs:

- Voice board PCB:
    - One sinewave oscillator.
    - Three CV mixers (amp, frequency, pan).
    - An envelope generator chip.

- Voice control PCB:
    - Potentiometers controlling parameters for a single voice board.

- PSU/Mixer PCB:
    - Stereo master mixer for all four voices
    - Linear +/-15V AC/DC PSU

- Modulation matrix PCB:
    - df

## AS3372 - The Heart of Solstrejf
TODO

## Modulation Matrix
TODO





## Conclusion
