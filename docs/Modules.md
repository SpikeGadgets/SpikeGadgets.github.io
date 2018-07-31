---
layout: page
title: Modules
---
- [Modules](#modules)
    - [Camera Module](#camera-module)
    - [StateScript](#statescript)
    - [Simple Communicator](#simple-communicator)
    - [Workspace GUI](#workspace-gui)
    - [Writing your own](#writing-your-own)

# Modules 

Trodes is made up of modules, each running as a separate process and serving a specialized purpose. The modules all communicate with the central graphical interface of Trodes that performs the most crucial tasks associated with visualizing and saving data. With this architecture, the Trodes suite is readily extendable without needing detailed understanding of existing code. This allows Trodes and its users to benefit from a community of talented engineers and scientists.

Trodes is bundled with two modules, the camera module and the StateScript module. 

## [Camera Module](CameraModule)

The Camera Module is used to acquire video that is synchronized to the neural recording.

Track the animal's position either during the experiment or afterward as you play the video back, and monitor events as they occur. Most USB webcams are supported, and for precise frame timing control the module also supports Allied Vision Tech's GigE cameras on all three operating systems.

## [StateScript](StateScript)

StateScript allows you to define temporally precise sequences of events and their triggers, which is useful with devices such as lights, levers, beam breaks, lasers, stimulation sources, audio, and solenoid pumps. Scripts are sent to our Environmental Control Unit and compiled to run in real-time in hardware. Controlling the experimental environment has never been so easy.

## Simple Communicator

## Workspace GUI

## Writing your own