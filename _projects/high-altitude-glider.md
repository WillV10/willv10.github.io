---
layout: project
title: "High Altitude Glider"
description: "High altitude tethered balloon deployed glider."
hero: /assets/images/projects/flight-path.png
order: 1
tags: [Python, PX4, C++]
---


# Version 1.0 AE512 Class Project

<iframe
  src="/assets/pdfs/High Altitude Glider.pdf"
  width="100%"
  height="1000px"
  style="border:1px solid rgba(255,255,255,0.2); border-radius:12px;">
</iframe>

# Version 2.0 Rebuild
- After the completion of the AE512 variant of the vehicle, a new vehicle was constructed incorporating lessons learned from the first
- This vehicle went through the same drone drop tests as the first. It incorporated a 1.2 GHz FPV video system, Crossfire 915 MHz long range radios for telemetry and control, an APRS packet transmission system, and an independent GPS locator. It was controlled by the same Pixhawk 4 mini as used in the first vehicle running PX4 for primary control, as well as a Raspberry Pi Zero W which ran a mission python script which controlled the drop release, parachute deployment, and performance calculations to select a suitable landing location. 
- Many drone drop tests were performed, but only a single untethered balloon flight was ever flown.

![Glider 2.0 Design](/assets/images/projects/IMG_8634.jpeg)

![Glider 2.0 Internals](/assets/images/projects/IMG_8641.HEIC)

![Glider 2.0 Untethered Flight Path](/assets/images/projects/longitudinal-state-space.png)