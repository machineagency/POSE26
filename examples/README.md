# POSE 2026 Workshop!
*April 22–24, 2026 @ The University of Washington, Seattle, WA*

This repository contains code for the 2026 Pathways to Open-Source Hardware for Laboratory Automation Workshop at the University of Washington! Read on to learn about the workshop and the contents of this repository.

## About the Workshop
We are planning a third annual gathering of scientists and engineers interested in using open-source technologies for automating scientific experiments in Seattle, WA! This workshop will be held April 22–24, 2026. You can find more information about the workshop [here](https://depts.washington.edu/machines/scienceautomation/).


## Workshop Hackathon
During the workshop, we will have hands-on activity sessions to set up automation workflows using [Science Jubilee](https://science-jubilee.readthedocs.io/en/latest/), a custom toolchanging machine.

### Materials
We'll have 6 machines and the following tools:

- usb camera, for imaging the bed plate
- 10cc syringe, for liquid handling or gel extrusion 3D printing
- spectral sensor, for data collection
- OT-2 pipette, for precision liquid handling
- additionally, 1 machine has 2 FFF 3D printing heads equipped

We'll provide laptops with all relevant software already installed. If you'd prefer to try to install on your own laptop, see the [installation instructions](#installation) below.

### What Should I Make?
Whatever you want! You don't necessarily have to use Jubilee, if you and your team have other interests. Here are some ideas:

- Implementing a color matching algorithm
- Tuning (plastic or gel) 3D printing parameters using a video feed
- Integrating a new tool for Jubilee, if you're CAD-inclined
- Working on optimization algorithms that might be suited to Jubilee, if you're software-inclined

Whatever you work on, be sure to take some notes along the way so you can share with others what you've worked on!

### Schedule

#### Wednesday, April 22
- **6–9p:** Introductions and community social at the eScience Institute WRF Data Science Studio

#### Thursday, April 23
- **9–9:30a:** Light breakfast and light conversations in HUB 334
- **9:30–10:20a:** Workshop kick-off (Nadya Peek and Lilo Pozzo) in HUB 334
- **10:30a–12p:** Hands-on activity in Sieg 118 and HUB 334
- **12–1:30p:** Cross-group conversation and lunch in Sieg 329
- **1:30–2:20p:** Small-group discussions 1 in Sieg 420/429
- **2:30–4:20p:** Hands-on activity in Sieg 118
- **4:30–5:30p:** Small-group discussions 2 in Sieg 420/429
- **5:30p:** Remix time
- **6–8p:** Day 1 wrap-up conversation and dinner at Big Time

#### Friday, April 24
- **9–9:30a:** Light breakfast and light conversations in Sieg 329
- **9:30–10:20a:** Small-group discussions 3 in Sieg 329
- **10:30a–12p:** Hands-on activity in Sieg 118
- **12–1:30p:** Cross-group conversation and lunch in Sieg 429
- **1:30–2:20p:** Small-group discussions 4 in Sieg 433
- **2:30–4:20p:** Final hackathon session in Sieg 118
- **4:30–5:30p:** Final discussion and report back in Sieg 433
- **6–8p:** Workshop wrap-up conversation and dinner at Schultzy's

### Connecting to Jubilee
Much of this workshop involves doing various things with Jubilee, so the first step is to connect with the machine and open Jupyter Lab to talk to it. Here's how:

1. Turn on the machine.
2. Connect with the Ethernet cable.
3. Open Jupyter Lab: you can do this via the Terminal by typing `jupyter lab` or through VS Code.

### Repository Overview
This repository has introductory notebooks for each tool, along with some other relevant documentation. They probably won't do exactly what you'd like, but will give you a starting point to work from! There's no need to run through every notebook; feel free to only use the ones relevant to your interests/your project idea. Here's a brief overview of the notebooks:

#### [Start Here](./start-here/)
Start here! This folder contains a general introduction to using the machine (connecting, moving around, etc).

#### [Labware](./labware/)
Notebooks about using a laboratory automation deck to house labware. Check out these notebooks if you plan on doing liquid handling/navigating labware!

#### [Pipette](./pipette/)
Notebooks for liquid handling with the OT-2 Pipette. Run through the [labware](#labware) notebooks for more details on setting up labware.

#### [Syringe](./syringe/)
Notebooks for both liquid handling with the syringe tool, as well as printing gels. If you want to do liquid handling with the syringe tool, run through the [labware](#labware) notebooks for more details on setting up labware.

#### [Spectral Sensor](./spectral-sensor/)
Notebooks for data collection with the spectral sensor.

#### [Camera](./camera/)
Notebooks for taking pictures and video using the camera tool.

#### [Calibration](./calibration/)
Some helper notebooks for various calibration tasks, in case your project calls for them.

#### [Extending Science Jubilee](./extending-science-jubilee/)
Resources on how to add a new tool to science jubilee.

## Installation
We will have laptops with the relevant software installed so you don't have to deal with any installation issues. But if you'd like to use your personal computer:

- Install `science-jubilee` using the instructions [here](https://science-jubilee.readthedocs.io/en/latest/getting_started/installation.html#installation)