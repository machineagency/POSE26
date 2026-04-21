# POSE 2026 Workshop
*April 22–24, 2026 @ Seattle, WA*

This repository contains code for the 2026 Pathways to Open-Source Hardware for Laboratory Automation Workshop. Read on to learn about the workshop and the contents of this repository.

## About the Workshop
We are gathering scientists and engineers interested in using open-source technologies for automating scientific experiments in Seattle, WA! You can find more information about the workshop, participants, and schedule at [depts.washington.edu/machines/scienceautomation](https://depts.washington.edu/machines/scienceautomation/).

## Workshop Hackathon
During the workshop, we will break into teams and have hands-on activity sessions to set up automation workflows using [Science Jubilee](https://science-jubilee.readthedocs.io/en/latest/), a custom toolchanging machine.

### Materials
We have 6 Jubilees, each of which is equipped with some of the following:

- a camera, for imaging the bed plate
- a 10cc syringe, for liquid handling or gel extrusion 3D printing
- a spectral sensor, for data collection
- an OT-2 pipette, for precision liquid handling
- a sonicator, for sonication
- an electromagnet, for capping and uncapping

We'll also have:
- An OT-2
- a Genmitsu CNC router

We'll provide laptops with all relevant software already installed for the Jubilees. If you'd prefer to install on your own laptop, see the [installation instructions](#installation) below.

### What Should I Make?
Whatever you want! You don't necessarily have to use Jubilee, if you and your team have other interests. Here are some ideas:

- Implementing a color matching algorithm
- Tuning (plastic or gel) 3D printing parameters using a video feed
- Integrating a new tool for Jubilee, if you're CAD-inclined
- Working on optimization algorithms that might be suited to Jubilee, if you're software-inclined

Whatever you work on, be sure to take some notes along the way so you can share with others what you've worked on!

### Connecting to Jubilee
If you want to use a Jubilee, the first step is to connect to the machine and open a computational notebook to talk to it. Here's how:

1. Turn on the machine.
2. Connect with the Ethernet cable.
3. Open this folder in VS Code.
4. A dashboard is also available at http://192.168.1.2/ if you want to send individual commands.

### Repository Overview
This repository has introductory notebooks for each tool, along with some other relevant documentation. They probably won't do exactly what you'd like, but will give you a starting point to work from! There's no need to run through every notebook; feel free to only use the ones relevant to your interests/your project idea.

#### [Start Here](./examples/start-here/)
Start here! A general introduction to using the machine (connecting, moving around, etc).

#### [Labware](./examples/labware/)
Notebooks about using a laboratory automation deck to house labware. Check these out if you plan on doing liquid handling or navigating labware.

#### [Pipette](./examples/pipette/)
Notebooks for liquid handling with the OT-2 Pipette. Run through the [labware](#labware) notebooks for more details on setting up labware.

#### [Syringe](./examples/syringe/)
Notebooks for both liquid handling with the syringe tool, as well as gel extrusion 3D printing. If you want to do liquid handling with the syringe, run through the [labware](#labware) notebooks first.

#### [Spectral Sensor](./examples/spectral-sensor/)
Notebooks for data collection with the spectral sensor.

#### [Camera](./examples/camera/)
Notebooks for taking pictures and video using the camera tool.

#### [Calibration](./examples/calibration/)
Some helper notebooks for various calibration tasks, in case your project calls for them.

#### [Extending Science Jubilee](./examples/extending-science-jubilee/)
Resources on how to add a new tool to science jubilee.

#### [Demo of All Tools](./DemoOfAllTools.ipynb)
A single notebook that walks through every tool on the machine: syringe serial dilution, pipette transfer, camera capture, and spectral measurement. This assumes some familiarity with Jubilee, so check out the example notebooks above for more details.

---

## Installation
We will have laptops with the relevant software installed so you don't have to deal with any installation issues. But if you'd like to use your personal computer, use the following steps. Note that we are using the `pose26-demo` branch of science-jubilee!

### Mac

```bash
mkdir POSE26-workshop
cd POSE26-workshop

python3 -m venv .venv
source .venv/bin/activate

git clone https://github.com/machineagency/science-jubilee.git
git clone https://github.com/machineagency/POSE26.git

cd science-jubilee
git checkout pose26-demo
python3 -m pip install --upgrade pip
python3 -m pip install -e .
cd ..

python3 -m pip install jupyterlab ipykernel
python3 -m pip install opencv-contrib-python matplotlib numpy
python3 -m ipykernel install --user --name=science_jubilee
```

If your Python version raises an OpenSSL warning:
```bash
python3 -m pip uninstall urllib3
python3 -m pip install urllib3==1.26.7
```

### VS Code (optional)
- Install from [code.visualstudio.com](https://code.visualstudio.com)
- Install the Python and Jupyter extensions
- Open the workshop folder and select the `science_jubilee` kernel when running notebooks

---

## Optional: AI-Assisted Coding with jubiLLM

This repo includes optional support for using an AI coding assistant that understands Science Jubilee. The assistant can help you understand existing notebooks, modify code for your experiment, and write new workflows.

For setup instructions, see **[jubiLLM setup](./jubiLLM/readme.md)**.
