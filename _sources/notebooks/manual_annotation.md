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

# notebook: manual annotation

One of the common bioimage analysis tasks in manual annotation. This annotation could be to provide ground truth data for a machine learning algorithm or quality control for automated process.

There are 3 main types of manual annotation that napari provides, each corresponding to a different layer type
- adding points to mark particular locations in an image with the **Points** layer
- drawing 2D polygons to identify particular regions of interest with the **Shapes** layer
- painting labels to provide a pixel-wise annotation of an image with the **Labels** layer

This tutorial will explore these three manual annotations in **[napari](https://napari.org/)**, using that same data from the image visualization tutorial.


## Setup
We start by importing `napari`, our `nbscreenshot` utility and instantiating an empty viewer

```{code-cell} python
:tags: ["remove-output"]

import napari
from napari.utils import nbscreenshot

viewer = napari.Viewer()
```

In this notebook we will load our data directly into napari using out builtin plugin reader

```{code-cell} python
:tags: ["remove-output"]

viewer.open('data/nuclei.tif')
```
This loads the 3D data into the napari viewer. 
Scrolling to the 30th z-slice should look as follows:

```{code-cell} python
:tags: ["remove-cell"]

viewer.dims.current_step = (30, 0, 0)
```

```{code-cell} python
nbscreenshot(viewer)
```

## Annotating dividing and non-dividing cells using the points layer

One simple task that a biologist or bioimage analyst might be interseted in annotating each cell as diving or non-dividng.

In order to do this we are going to add two points layers to the viewer, one called `dividing` and one called `non-dividing` and set some basic properties on these layers.

You can add the layers using the new points button in the middle of the left panel of the viewer (left-most button featuring with many small dots), or you can add them programatically from the viewer. We'll add them programatically from the viewer for this example.

```{code-cell} python
# add the first points layer for dividing cells
viewer.add_points(name='dividing', face_color='red', n_dimensional=True, ndim=3);

# add the second points layer for non-dividing cells
viewer.add_points(name='non-dividing', face_color='blue', n_dimensional=True, ndim=3);
```

Notice now how two new layers have been created, and that these layers have different controls (top-left corner) compared to the image layer. These layers now have properties like `face color`, `point size`, and `symbol` that can be adjusted. Note we have also enabled something called `n_dimensional` mode for these `Points` layers. This setting will be the points an `n-dimensional` extent when scrolling through z-planes and is useful when looking at 3D data.

```{code-cell}
nbscreenshot(viewer)
```

To add points you must enter add mode. This can be done by clicking on the add mode button in the top row of the control panel (2nd from the left, circle with a plus in it), or programatically from the notebook.

```{code-cell}
# programatically enter add mode for both Points layers to enable editing
viewer.layers['dividing'].mode = 'add'
viewer.layers['non-dividing'].mode = 'add'
```

```{code-cell}
nbscreenshot(viewer)
```

Now start adding points, clicking once per cell, approximately in the center of the cell, with the appropriate `Points` layer selected. You can tell which `Points` layer is selected because it will be highlighted left in the layers list in the bottom left hand corner of the screen. You can rapidly switch between selected layers using the up and down keys. Also don't forget this is a z-slice so you should move up and down the slice, which can also be done with the left/ right key.

After annotation, my data looks as follows:

```{code-cell} python
:tags: ["remove-cell"]

viewer.layers['dividing'].add([30, 88.1365794 , 54.39650486])
viewer.layers['non-dividing'].add(
      [[30,  13.90840911,  27.02955319],
       [30,  32.27800681,  82.51323603],
       [30,  11.28418087, 148.86872159],
       [30,  46.52381727, 182.60879899],
       [30,  73.89076894, 108.3806287 ],
       [30,  96.00926413, 160.11541406],
       [30,  47.64848652, 226.09600987],
       [30, 142.12070325, 186.73258623],
       [30, 145.86960074, 242.59115882],
       [30, 200.97839383, 173.61144502],
       [30, 154.49206496, 112.87930569],
       [30, 135.74757752,  39.77580465],
       [30, 182.60879613,  52.14716636],
       [30, 221.97221977,  82.13834628],
       [30, 241.09159696, 136.12247012],
       [30, 250.46384069, 209.60086091]]
   )
```

```{code-cell} python
nbscreenshot(viewer)
```

Or in 3D, which can be enabled by clicking 3D rendering button (which looks like a wireframe of a cube, second from the left) like this:

```{code-cell} python
:tags: ["remove-cell"]

viewer.dims.ndisplay = 3
viewer.camera.angles = (-10, 30, 135)
```

```{code-cell} python
nbscreenshot(viewer)
```

You can also get the number of cells of each class and an array of their centers as follows:

```{code-cell} python
print('Number of diving cells:', len(viewer.layers['dividing'].data))
print('Number of non-diving cells:', len(viewer.layers['non-dividing'].data))
```

```{code-cell} python
# Locations of non-dividing cells
viewer.layers['non-dividing'].data
```

To save a `csv` file with these values for each layer you can use our builtin writer functionality. Note these csv files can easily be opened up into standard tools like pandas or excel for further analysis.

```{code-cell} python
# Save out Points layer data to a csv file
viewer.layers['dividing'].save('dividing.csv', plugin='builtins')
viewer.layers['non-dividing'].save('non-dividing.csv', plugin='builtins')
```

Points layers also have a properties dictionary that would enable you to add other attributes like `volume` or `maximum-intensity` should you calculate those for each cell. You can learn more about the these advanced points annotations from the [tutorial](https://napari.org/tutorials/applications/annotate_points).


## Drawing polygons around cells
Another common task for research biologists and bioimage analysts is drawing polygons around regions of interest, in this case nuclei. These polygons might be used for segmentation and to quantify properties of interest.

For this example we'll work with a 2D maximum intensity projection of our cells in order to keep things simple. We can take the data we've already loaded into napari and use it for the projection.

```{code-cell} python
# Take the maximum intensity projection of the cells
nuclei_mip = viewer.layers['nuclei'].data.max(axis=0)
```

```{code-cell} python
# Remove select and remove all the current layers from the viewer
viewer.layers.select_all()
viewer.layers.remove_selected()

# Add in the maximum intensity projection
viewer.add_image(nuclei_mip);
```

```{code-cell} python
nbscreenshot(viewer)
```

We can now add an empty new shapes layer from the GUI using the new shapes button (middle of the left panel, 2nd from the left with a polygon on it) or programatically from the notebook.

```{code-cell} python
:tags: ["remove-cell"]

viewer.dims.ndisplay = 2
```

```{code-cell} python
viewer.add_shapes(name='nuclei outlines', face_color='red', edge_color='white', opacity=0.7)
```

Notice now in top left corner of the viewer we have a new controls panel corresponding to the shapes layer with buttons for creating and editing shapes. They include a select mode for dragging and resizing shapes, a vertex selection mode for dragging vertices, tools for adding and subtracting vertices from existing shapes, buttons for reordering shapes, and tools for drawing lines, ellipses, rectangles, paths, and polygons.

```{code-cell} python
nbscreenshot(viewer)
```

We will draw some shapes with the polygon tool around a couple of different nuclei.

```{code-cell} python
:tags: ["remove-cell"]

p1 = [[142.12070325,  94.13481824],
      [150.36827772,  89.26125151],
      [163.86430869,  87.76169251],
      [179.98456789, 100.88283373],
      [182.98368588, 113.25419544],
      [173.98633191, 124.87577766],
      [163.86430869, 133.87313163],
      [148.49382898, 135.37269063],
      [137.99691601, 128.2497854 ],
      [132.74845952, 116.25331343],
      [134.24801852, 103.50706197]]
p2 = [[ 87.01191015, 140.24625736],
      [ 97.13393338, 136.49735987],
      [109.50529509, 140.24625736],
      [118.12775932, 149.99339083],
      [120.37709781, 163.4894218 ],
      [114.75375158, 174.73611426],
      [104.25683861, 181.48412974],
      [ 93.75992564, 184.10835799],
      [ 80.26389467, 179.609681  ],
      [ 73.89076894, 166.11365004],
      [ 74.26565869, 152.24272933],
      [ 78.38944593, 145.1198241 ]]
p3 = [[ 48.02337627, 108.3806287 ],
      [ 48.02337627,  97.13393624],
      [ 58.52028924,  88.88636176],
      [ 69.39209195,  86.26213352],
      [ 83.26301267,  92.63525925],
      [ 94.50970513, 105.38151071],
      [ 98.25860262, 121.50176992],
      [ 93.01014614, 133.12335213],
      [ 80.26389467, 136.12247012],
      [ 67.51764321, 131.24890339],
      [ 53.6467225 , 123.00132891]]

viewer.layers['nuclei outlines'].add_polygons([p1, p2, p3])

# direct mode for vertex selection
viewer.layers.mode = 'direct'

# select and force redraw
viewer.layers['nuclei outlines'].selected_data = {2}
viewer.layers['nuclei outlines'].refresh()
```


```{code-cell} python
nbscreenshot(viewer)
```

The vertices for these shapes can be obtained from the shapes layer as follows:

```{code-cell} python
# The list of vertices for each shape
viewer.layers['nuclei outlines'].data
```

These shapes, and the underlying image can be saved as an svg file using our dedicated svg writer. This functionality is useful if you want to put the image and the shapes into a tool like illustrator when preparing a figure or a presentation.

```{code-cell} python
viewer.layers.save('nuclei-outlines.svg', plugin='svg')
```

Similarly to the points layer, we're working on adding support for properties dictionary to the shapes layer which would allow you to assign attributes to each shape and do things like adjust shape color based on them.

One common thing to use a shapes for is creating a binary mask or labels image where each pixel is assigned an integer label of the shape it is contained within, if any. napari provides some tooling to make these conversions easy.

```{code-cell} python
# Convert the polygons into labels
shape = viewer.layers['nuclei_mip'].data.shape
nuclei_labels = viewer.layers['nuclei outlines'].to_labels(labels_shape=shape)

print('Number of labels:', nuclei_labels.max())
```

We can now add this labels image to the viewer as a labels layer.

```{code-cell} python
# Add the cell segmenation labels as a labels layer
viewer.add_labels(nuclei_labels);

# Turn off the visibility of the shapes layer so as not to get confused
viewer.layers['nuclei outlines'].visible = False
```

```{code-cell} python
nbscreenshot(viewer)
```

## Painting labels for pixel-wise annotations

With the labels layer we can now make pixel-wise annotaions using a paintbrush, fill bucket, and eraser tools (see the row of buttons in the control panel in the top left of the viewer).

Using these tools we can touch up any of the labels that we got from our polygon masks or draw entirely new ones.

```{code-cell} python
:tags: ["remove-cell"]

# dilate labels a bit to show a change
from skimage.morphology import dilation
viewer.layers['nuclei_labels'].data = dilation(viewer.layers['nuclei_labels'].data)
```

```{code-cell} python
nbscreenshot(viewer)
```

```{code-cell} python
# Save out the nuclei labels as a tiff file
viewer.layers['nuclei_labels'].save('nuclei-labels.tif', plugin='builtins');
```

```{code-cell} python
# Note that the cell labels could be reloaded into the viewer as follows
viewer.open('nuclei-labels.tif', name='saved nuclei', plugin='builtins');

viewer.layers['nuclei_labels'].visible = False
```

```{code-cell} python
nbscreenshot(viewer)
```

One simple thing someone might want to do is quantify the total amount of signal inside our original image relative to the total amount of area for each of our labels. Using some simple Python we can do this as follows: 

```{code-cell} python
n_labels = viewer.layers['nuclei_labels'].data.max()

ratios = []
for label_id in range(n_labels):
    inside_pixels = viewer.layers['nuclei_labels'].data == label_id
    area = inside_pixels.sum()
    signal = viewer.layers['nuclei_mip'].data[inside_pixels].sum()
    ratios.append(signal / area)
    
print('Signal per unit area for our labels:', ratios)
```

As with the points and shapes layers we also have a properties dictionary on the labels layer to make it easy to attach extra attributes to each labeled region.

## Conclusions

We've now seen how to use the **Points**, **Shapes**, and **Labels** layers to produce manual annotations in napari and save those annotations in meaningful formats.

The next lessons will teach us how to perform interactive analyses in napari and more!
