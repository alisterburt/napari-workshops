# SwissBIAS napari intro and plugins 11/21

This workshop will take you through the basics of bioimage analysis in Python and [napari](https://www.napari.org) and introduce you to the napari plugin engine. We will be performing some analysis on segmented nuclei using a combination of `napari`, `scikit-image`, `scipy`, and `cellpose`. Additionally, we will make a spot detection plugin.

## Instructors
The instructors for this session are Guillaume Witz ([@guiwitz](https://twitter.com/guiwitz)), Kevin Yamauchi ([@ky396](https://twitter.com/ky396)), Lorenzo Gaifas ([@brisvag](https://twitter.com/brisvag)).

## Goals
The aim of this workshop is to provide an introduction to bioimage analysis in Python and `napari`. By the end of the workshop you should be able to
- use napari as an image viewer
- perform interactive analysis with napari in a [jupyter notebook](https://jupyter.org/)
- make use of the [napari plugin ecosystem](https://www.napari-hub.org/)
- make simple plugins using the napari hookspecs

## Tutorial instructions

1. Install napari and dependencies. Instructions [here](./installation.md).
2. Download the notebooks and launch jupyter notebook. Instructions [here](./notebook_setup.md).
3. Do the introduction to viewing images in napari (`part_0_viewer_intro.ipynb`).
4. Segment and measure nuclei using the cellpose plugin (`part_1_segmenting_and_measuring_nuclei.ipynb`).
5. Perform spot detection using your own spot detection function. There are 3 versions of this tutorial (see description below). They all cover the same content, they just require more or less coding depending on experience level.
    - `part_2_spot_detection_tutorial_beginner.ipynb`: this is the activity notebook for people new to image processing with python
    - `part_2_spot_detection_tutorial_advanced.ipynb`: this is the activity notebook for people with experience performing image processing with python
    - `part_2_spot_detection_tutorial_solution.ipynb`: this is the solution to the activity.
6. Turn your spot detection function into a napari plugin. Instructions [here](./make_a_simple_plugin.md).
