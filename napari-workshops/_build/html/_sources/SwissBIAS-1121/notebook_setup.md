# Downloading and launching the notebooks

## Downloading the notebooks.
During this tutorial, we will be working through a set of notebooks. On this page, we will download the notebooks and launch jupyter notebook. There are two ways to download the notebooks: download as a .zip from github. Follow the instructions below for either "downloading zip" (recommended for beginners) or "cloning via git".

### Downloading .zip
You can download the notebooks as a .zip file. To do so, please do the following:

1. Navigate your web browser to https://github.com/kevinyamauchi/napari_plugin_intro_workshop
2. Click the green "Code" button to open the download menu and then "Download ZIP"
    ![download code](./resources/download_code.png)
3. Choose the location you would like to download the .zip.
4. Open your file browser and double click on the .zip file to uncompress it.
5. You have downloaded the notebooks! Proceed to the "Launching jupyter notebook" section.


### Cloning via git
To perform this tutorial, we first need to download the notebook. To do so, please clone the repository containing the tutorial materials to your computer. We recommend cloning the materials into your Documents folder, but you can choose another suitable location. First, open your Terminal navigate to you the folder you will download the course materials into

```bash
cd ~/Documents
```

and then clone the repository. This will download all of the files necessary for this tutorial.

```bash
git clone https://github.com/kevinyamauchi/napari_plugin_intro_workshop.git
```

## Launch jupyter notebook

Open your terminal and navigate to the `notebooks` subdirectory of the `napari_plugin_intro_workshop` directory you just downloaded.

```
cd <path to napari_plugin_intro_workshop>/notebooks
```

Now activate your `napari-tutorial` conda environment you created in the installation step.

```
conda activate napari-tutorial
```

We will perform the analysis using Jupyter Notebook. To start Jupyter Notebook, enter

```bash
jupyter notebook
```

Jupyter Notebook will open in a browser window and you will see the following notebooks:

- `part_0_viewer_intro.ipynb`: in this activity, you will gain familiarity with loading and viewing images in napari.
- `part_1_segmenting_and_measuring_nuclei.ipynb`: in this notebook, you will use cellpose to segment nuclei and scikit-image to measure them.
- `part_2_spot_detection_tutorial_beginner.ipynb`: this is the spot detection notebook for people new to image processing with python
- `part_2_spot_detection_tutorial_advanced.ipynb`: this is the spot detection notebook for people with experience performing image processing with python
- `part_2_spot_detection_tutorial_solution.ipynb`: this is the solution to the spot detection activity.

All of the "part 2" notebooks cover the same activities, they just require you to fill in more or less of the solution. For your convenience, we have rendered the solutions to these notebooks in the following pages.

![jupyter notebook](./resources/jupyter_notebook.png)
