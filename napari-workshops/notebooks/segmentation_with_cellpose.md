---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.10.3
kernelspec:
  display_name: Python 3
  language: python
  name: python3
---

# notebook: segmenting nuclei with the cellpose napari plugin

## Overview

Plugins extend the functionality of napari and can be combined together to build workflows. Many plugins exist for common analysis tasks such as segmentation and filtering. In this activity, we will segment nuclei using the [cellpose napari plugin](https://github.com/MouseLand/cellpose-napari). Please visit the [napari hub](https://www.napari-hub.org/) for a listing of the available plugins.

### Data source

The data were downloaded from the [OpticalPooledScreens github repository](https://github.com/feldman4/OpticalPooledScreens).

## Loading the data

We will start by loading an image of DAPI stained nuclei. We can use `scikit-image`'s `imread()` function to download the data from the link below and load it into a numpy array called `nuclei`.

```{code-cell} python
:tags: ["remove-output"]

from skimage import io

url = 'https://raw.githubusercontent.com/kevinyamauchi/napari-spot-detection-tutorial/main/data/nuclei_cropped.tif'
nuclei = io.imread(url)
```

## Viewing the image

As we did in the previous notebooks, we can view the image in napari using the `napari.view_image()` function. Here we set the colormap to `magma`.

```{code-cell} ipython3
:tags: ["remove-output"]

import napari

viewer = napari.view_image(nuclei, colormap='magma')
```

```{code-cell} ipython3
from napari.utils import nbscreenshot

nbscreenshot(viewer)
```

## Segment nuclei

To segment the nuclei, we will use the [cellpose napari plugin](https://github.com/MouseLand/cellpose-napari). Please perform the segmentation using the instructions below. For more information on cellpose, please see the [paper](https://www.nature.com/articles/s41592-020-01018-x) and [repository](https://github.com/MouseLand/cellpose).

1. Start the cellpose plugin. From the menu bar, click Plugins->cellpose-napari: cellpose. You should see the plugin added to the right side of the viewer.

```{image} resources/cellpose_plugin.png
:alt:  cellpose plugin
:width: 80%
:align: center
```

2. Select the "nuclei" image layer.

```{image} resources/cellpose_screenshots_image_selection.png
:alt:  select the image layer
:width: 80%
:align: center
```

3. Set the model type to "nuclei"

```{image} resources/cellpose_screenshots_model_selection.png
:alt:  select the nuclei model
:width: 80%
:align: center
```

4. We need to give cellpose an estimate of the size of the nuclei so it can properly scale the data. We can do so using a napari Shapes layer. With the Shapes layer, we will outline some nuclei and then cellpose will use those annotations to estimate the size of the nuclei.
    1. Click the "add Shapes" layer button in the viewer. This will create and select a new layer called "Shapes".

    ```{image} resources/cellpose_screenshots_add_shape.png
	:alt:  add a shapes layer to measure the diameter
	:width: 80%
	:align: center
	```

    2. Set the mode to "Ellipse" by clicking the button in the layer controls.
    3. In the canvas, click and drag to add an ellipse that around a "representative" nucleus. For the purpose of this demo, this is enough, but for other data you may need to give more examples to make a better estimate of the cell diameter. If you need to pan/zoom while adding an ellipse, holding the spacebar will allow you to pan/zoom using your mouse (pan via click/drag, zoom by scrolling).
    4. If you would like to edit or move an ellipse, you can switch to "Select shapes" mode in the viewer. Shapes can now be moved by clicking on them and then dragging. They can be resized by selecting them and then dragging the control points.
    
    ```{image} resources/cellpose_screenshots_select_shape.png
	:alt:  use selection mode to edit shapes
	:width: 80%
	:align: center
	```

    5. Once you are happy with your annotations, you can click the "compute diameter from shape layer" button and you will see the "diameter" value populated. For this demo, the value is typically around 10 pixels.

    ```{image} resources/cellpose_screenshots_diameter.png
	:alt:  estimate the cell diameters
	:width: 80%
	:align: center
	```

5. For this demo, we recommend de-selecting "average 4 nets"(potentially less accurate, but faster segmentation) and otherwise using the default settings. If you would like to learn more about the cellpose settings, please see the [cellpose plugin documentation](https://cellpose-napari.readthedocs.io/en/latest/settings.html).

 	```{image} resources/cellpose_screenshots_settings.png
	:alt:  select the segmentation settings
	:width: 80%
	:align: center
	```
	
6. Now you are ready to run the segmentation! Click the "run segmentation" button. Segmentation for this demo typically takes ~1.5 minutes. Note that there is not currently a progress bar, so please just be patient.

 	```{image} resources/cellpose_screenshots_run.png
	:alt:  start the segmentation
	:width: 80%
	:align: center
	```

7. When the segmentation is completed, you will see some new layers added to the layer list. Of particular interest is "nuclei_p_masks_000", which contains our segmentation mask added as a Labels layer.

 	```{image} resources/cellpose_screenshots_results.png
	:alt:  select the segmentation settings
	:width: 80%
	:align: center
	```




 