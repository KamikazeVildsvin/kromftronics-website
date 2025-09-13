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
featured_image: '/images/solstrejf/control-interface/mod-matrix-and-voiceboard-control.png'
---

Solstrejf, roughly translated from Danish to "sun streak" or "sun touch", is a four-voice analog sine wave drone oscillator. 

I was on a hike in Sweden out near a beautiful forest lake just around the break of dawn. 
The way the sun interacted with nature gave me a real sense of calm and ease. 
At that moment I thought, what if I could create an instrument that would be able to replicate this feeling? 
A sine wave drone synthesizer came to my mind, similar to the early Musique Concrète test equipment techniques.

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

## AS3372 - The Heart of Solstrejf
TODO

## Modulation Matrix
TODO





## Conclusion
