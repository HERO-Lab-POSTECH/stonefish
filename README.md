![Stonefish logo](https://github.com/patrykcieslak/stonefish/blob/master/Library/shaders/logo_64.png)
# ***Stonefish***
### An advanced simulation tool developed for marine robotics.

Stonefish is a C++ library combining a physics engine and a lightweight rendering pipeline. The physics engine is based on the core functionality of the [Bullet Physics](https://pybullet.org) library, extended to deliver realistic simulation of marine robots. It is directed towards researchers in the field of marine robotics but can as well be used as a general purpose robot simulator. 

Stonefish includes advanced hydrodynamic computations based on actual geometry of bodies, to better approximate hydrodynamic forces and allow for effects not possible when using symbolic models. The rendering pipeline, developed from the ground up, delivers realistic rendering of atmosphere, ocean and underwater environment. Special focus was put on the latter, where effects of wavelength-dependent light absorption and scattering were considered (other simulators often use only blue fog). 

Stonefish can be used to create standalone applications or combined with a [Robot Operating System](https://www.ros.org) (ROS) package [_stonefish_ros_](https://github.com/patrykcieslak/stonefish_ros), which implements 
standard simulator node and facilitates easy integration with ROS architecture.

There are two sources of documentation for the library: [html documentation generated with Sphinx](https://stonefish.readthedocs.io) and code documentation generated with Doxygen, based on comments in the code (instructions below).

### Requirements

The simulation is CPU heavy and requires a recent GPU. The minimum requirement is the support for *OpenGL 4.3*. 

Install official manufacturer drivers for your graphics card before using _Stonefish_!

The software is developed and tested on *Linux Ubuntu*. It should work on any Unix based platform. A version for Windows is not available at this time. MacOS is not supported due to its lack of support for OpenGL 4.3.

### Installation

#### 1. Dependencies

The following dependencies must be installed before building the library:

* **OpenGL Mathematics library** (libglm-dev, version >= 0.9.9.0)
* **SDL2 library** (libsdl2-dev)
* **Freetype library** (libfreetype6-dev)

Install on Ubuntu:
```bash
sudo apt-get update
sudo apt-get install -y libglm-dev libsdl2-dev libfreetype6-dev
```

> **Note:** SDL2 library may need a small fix to the CMake configuration file to avoid build errors. Remove a space after `-lSDL2` in `/usr/lib/x86_64-linux-gnu/cmake/SDL2/sdl2-config.cmake`.

#### 2. Building

```bash
# Clone the repository
git clone https://github.com/HERO-Lab-POSTECH/stonefish.git
cd stonefish

# Create build directory
mkdir build
cd build

# Configure with CMake
cmake ..

# Build (replace X with number of CPU cores)
make -jX

# Install system-wide
sudo make install
```

#### Build Options

CMake provides special build options:

* **BUILD_TESTS**: Build test applications and examples
  ```bash
  cmake -DBUILD_TESTS=ON ..
  ```

* **EMBED_RESOURCES**: Embed internal resources into library binary (useful for binary releases)
  ```bash
  cmake -DEMBED_RESOURCES=ON ..
  ```

#### 3. Documentation

Generate Doxygen code documentation:

```bash
cd stonefish
doxygen doxygen
# Open docs/html/index.html in browser
```

Online documentation: [https://stonefish.readthedocs.io](https://stonefish.readthedocs.io)

### Modifications (Imaging Sonar Depth Occlusion Fix)

This version includes fixes for imaging sonar depth occlusion issues where multiple overlapping objects at similar ranges would have their intensities incorrectly averaged.

**Modified Files:**

**C++ Source (OpenGL Depth Test):**
- `Library/src/graphics/OpenGLFLS.cpp` - Added depth test enable/disable for FLS
- `Library/src/graphics/OpenGLSSS.cpp` - Added depth test enable/disable for SSS
- `Library/src/graphics/OpenGLMSIS.cpp` - Added depth test enable/disable for MSIS

**GLSL Shaders (Closest-Sample-Wins Logic):**
- `Library/shaders/sonarOutput.comp` (FLS) - Changed from averaging to closest-sample-wins with brightness preference
- `Library/shaders/sonarOutput2.comp` (SSS) - Partial fix applied, full fix pending (requires sonarLine.comp update)
- `Library/shaders/sonarOutput3.comp` (MSIS) - Changed to closest-sample-wins
- `Library/shaders/sonarUpdate.comp` (MSIS) - Updated to work with new data format

**Changes:**
1. OpenGL depth testing now enabled during sonar input rendering to properly occlude far objects
2. Histogram logic changed from averaging (sum/count) to selecting closest sample per range bin
3. When samples are at same distance (±1cm), prefer brighter intensity (prevents dark FOV edges from overwriting bright objects)

**Status:**
- FLS: Fully fixed
- MSIS: Fully fixed
- SSS: Partially fixed (sonarLine.comp still uses averaging, needs future update)

### Credits
This software was written and is continuously developed by Patryk Cieślak. Parts of the software based on code developed by other authors are clearly marked as such.

If you find this software useful in your research, please cite:

*Patryk Cieślak, "Stonefish: An Advanced Open-Source Simulation Tool Designed for Marine Robotics, With a ROS Interface", In Proceedings of MTS/IEEE OCEANS 2019, June 2019, Marseille, France*
```
@inproceedings{stonefish,
   author = {Cie{\'s}lak, Patryk},
   booktitle = {Proceedings of MTS/IEEE OCEANS 2019},
   title = {{Stonefish: An Advanced Open-Source Simulation Tool Designed for Marine Robotics, With a ROS Interface}},
   month = jun,
   year = {2019},
   doi={10.1109/OCEANSE.2019.8867434}}
```

*Michele Grimaldi, Patryk Cieślak, Eduardo Ochoa, Vibhav Bharti, Hayat Rajani, Ignacio Carlucho, Maria Koskinopoulou, Yvan R. Petillot, and Nuno Gracias, "Stonefish: Supporting Machine Learning Research in Marine Robotics", In Proceedings of IEEE ICRA 2025, May 2025, Atlanta, USA*

```
@inproceedings{stonefish_ml,
   author = {Michele Grimaldi and Patryk Cieslak and Eduardo Ochoa and Vibhav Bharti and Hayat Rajani and Ignacio Carlucho and Maria Koskinopoulou and Yvan R. Petillot and Nuno Gracias},
   title = {Stonefish: Supporting Machine Learning Research in Marine Robotics},
   booktitle = {Proceedings of the IEEE International Conference on Robotics and Automation},
   month = may,
   year = {2025},
   eprint = {2502.11887},
   archivePrefix = {arXiv},
   url = {https://arxiv.org/abs/2502.11887},
   organization = {IEEE}}
```

### Funding
Currently there is no funding of this work. It is developed by the author following his needs and requests from other users. The work was started during his PhD studies and was mainly developed in his free time. Parts of this work were developed in the context of the project titled ”Force/position control system to enable compliant manipulation from a floating I-AUV”, which received funding from the European Community H2020 Programme, under the Marie Sklodowska-Curie grant agreement no. 750063. The work was also extended under a project titled ”EU Marine Robots”, which received funding from the European Community H2020 Programme, grant agreement no. 731103. 

### License
This is free software, published under the General Public License v3.0.
