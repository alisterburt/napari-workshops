# Creating a napari plugin
## Overview
In this tutorial, we will make a napari analysis plugin from the `detect_spots()` function we wrote in the first part of this practical session. The primary steps in making a napari plugin are as follows:

1. choose which manifest contribution(s) your plugin requires
2. create your repository using the napari cookiecutter template
3. implement your contributions
4. share your plugin with the community

In the following sections, we will work through steps (1) - (3). For step (4) you can refer to the [in depth plugin tutorial](https://www.youtube.com/watch?v=NL-VywidzXE),
or [the instructions on napari.org](https://napari.org/plugins/test_deploy.html#preparing-for-release).
You can also use [the lecture slides](https://docs.google.com/presentation/d/1-9ObrjrbjtrsO3kjEZELpd3QFDM0ct-oa3L-EIdO77I/edit?usp=sharing) as reference material if you'd like.

![plugin example](./resources/plugin-01.png)

## Choosing a contribution
A contribution is a construct in `napari.yaml` (the manifest file), that napari uses for each specific type of plugin. Each contribution conforms to a function signature, i.e. the function linked to the contribution defines what napari provides to the plugin (e.g., data and parameters) and what the plugin returns to napari. napari is then able to use the functions pointed to in `napari.yaml` to carry out the plugin tasks. The current categories of contributions are described below. Please see the [contribution reference](https://napari.org/plugins/contributions.html) and [contribution guide](https://napari.org/plugins/guides.html) for more details. Many plugins will declare multiple contributions to provide all of the desired functionality.

- **reader**: allows loading of specified data formats into napari layers
- **writer**: this allows layer data to be written to disk in specified formats
- **sample data**: allows developers to provide users with sample data with their plugin.
- **widget**: allows custom Qt widgets (GUIs) to be added to napari, either from a `magic_factory` widget, a plain function, or a subclass of QWidget
- **theme**: allows customization of the entire napari viewer appearance e.g. light theme or dark theme

In this tutorial, we will create a spot detection plugin by implementing a widget contribution with the spot detection function (`detect_spots()`) we created in the first part of this practical session.

## Using the cookiecutter template to create your plugin directory
To make creating plugins easier, we provide a template that automatically builds most of the infrastructure for your plugin, so you can focus on implementing the details unique to your plugin. The template is provided using a command line utility called [`cookiecutter`](https://github.com/cookiecutter/cookiecutter). In the following steps, you will build your plugin directory using the cookiecutter template.

First, open your terminal and navigate to your Documents folder.

```bash
cd ~/Documents
```

Next, activate the conda environment you created in the first part of the tutorial. This environment includes all of the packages required to make your plugin (including `cookiecutter`).

```bash
conda activate napari-tutorial
```

In this next step, we will use `cookiecutter` to create a directory for our plugin from the template. `cookiecutter` will ask a series of questions that that will customize the directory for your plugin. Once completed, a new directory will be created in your current directory. It will come pre-initialised with a git repository.

```bash
cookiecutter https://github.com/napari/cookiecutter-napari-plugin
```

You will be asked for some information to customize the setup of your plugin. Each prompt gives the default value in square brackets (`[]`). The questions are explained below. Enter your answer after the prompt and press enter to continue.

- `full_name [Napari Developer]`: enter your name here. Names entered here will be listed as the authors of the plugin in the package metadata.
- `email [yourname@example.com]`: this email will be listed as the contact information in the package metadata
- `github_username_or_organization [githubuser]`: if you have a github username, you can enter it here. It will be used to generate a GitHub URL for you after you enter a plugin name.
- `plugin_name [napari-foobar]`: enter the name you would like your plugin to be called. spaces are not allowed and are often replaced with `-` (e.g., `napari-spot-detector`). This will be your PyPI package name (if you choose to deploy your plugin).
- `Select github_repository_url:` this is used for plugin metadata and is not required now. If you don't plan to upload it to your github, select 2.
- `module_name [napari_foobar]`: this is the name of the module containing your plugin code. Typically, this is the plugin name with the `-` replaced with `_` (e.g., `napari_spot_detector`).
- `display_name [napari FooBar]`: this name will show up in the napari viewer and on the napari hub if you release your plugin. You should pick something human readable and ideally indicative of what your plugin does. There are no restrictions on the symbols in this name.
- `short_description [A simple plugin to use with napari]`: give a one sentence description of your plugin. This will go into the readme.\
- `include_reader_plugin [y]`: answer `y` (for yes) if you would like a reader contribution. We do not need a reader contribution for this tutorial, so answer `n` for no.
- `include_writer_plugin [y]`: answer `y` (for yes) if you would like a writer contribution. We do not need a writer contribution for this tutorial, so answer `n` for no.
- `include_sample_data_plugin [y]`: answer `y` (for yes) if you would like a sample data contribution. We do not need a sample data contribution for this tutorial, so answer `n` for no.
- `include_dock_widget_plugin [y]`: answer `y` (for yes) if you would like a widget contribution. We are implementing a widget contribution for this tutorial, **so answer `y` for yes**.
- `use_git_tags_for_versioning [n]`: we will not be covering setting plugin versions in this tutorial, so enter `n` for no.
- `install_precommit [n]`: we will not be covering precommit in this tutorial, so enter `n` for no.
- `Select license`: select the license you would like to use for your plugin. The license sets the rules for how others can build upon and re-use your plugin code. For more information on typical open source licenses, [choosealicense.com](https://choosealicense.com/) is a good primer. The default choice is [BSD-3](https://opensource.org/licenses/BSD-3-Clause).

After completing all of the questions, a directory will be created containing your new napari plugin. You will be given instructions on how to upload the initialized git repository to GitHub. By default, we will not be covering this aspect in the tutorial, but please feel free to ask the teaching team if you would like to give it a try. Your new plugin directory (assuming you called the plugin `napari-spot-detector` and the module `napari_spot_detector`) will be organized as follows (with some irrelevant files/folders omitted)

```
napari-spot-detector
├── .github
│   └── workflows
│       └── test_and_deploy.yml
├── LICENSE
├── MANIFEST.in
├── .napari
│   └── DESCRIPTION.md
├── pyproject.toml
├── README.md
├── setup.cfg
├── src
│   └── napari_spot_detector
│       ├── __init__.py
│       ├── napari.yaml
│       ├── _tests
│       │   ├── __init__.py
│       │   └── test_widget.py
│       └── _widget.py
└── tox.ini
```

See below for explanations about some of the most notable files, but do not hesitate to reach out to the teaching team if you have questions about any of the other files.

- `.github/workflows/test_and_deploy.yml`: this is a [github actions](https://github.com/features/actions) workflow that will automatically run the tests and upload your plugin to pypi (thus making it available through the built-in napari plugin browser. Please ask the teaching team if you would like to learn how to set up your github repository to support the workflow.
- `pyproject.toml` and `setup.cfg`: these files allow your plugin to be built as a package and installed by pip. the cookiecutter template has set everything up in these files, so you are good to go!
- the `src/` folder contains all the python code for your plugin.
- `src/napari_spot_detector/_widget.py`: This file contains example implementations for different widget contributions. This is where you will add your `detect_spot()` function. 
- the `src/napari_spot_detector/napari.yaml` file declares commands and contributions for each example widget in the `_widget.py` file. Look at these carefully and match up which command & contribution belong to what python code in the `_widget.py` file.

You have now set up the directory for your new plugin! You can explore the directory and files with the file browser. In the next step, you will complete your plugin by adding your `detect_spots()` function to the `_widget.py` file.

## Implementing a function GUI
In this step, we will implement our `detect_spots()` function as a plugin contribution. First, we will add our spot detection function to the plugin package. Then, we will add the type annotations to the function to so that napari can infer the correct GUI elements to add to our plugin.

1. To edit your plugin source code, open an integrated development environment (VSCode is a good, free option) or text editor.
2. In VSCode, open the directory you created with `cookiecutter` in the section above. 
	- From the "File" menu, select "Open..."
	- Navigate to and select the directory you created with `cookiecutter` (`~/Documents/napari-spot-detector` if you called your plugin `napari-spot-detector)`. 
3. You should now see your plugin directory in the "Explorer" pane in the left hand side of the window. You can double click on folders to expand them and files to open them in the editor.
4. Open the `<module_name>/_widget.py` file using VSCode by double clicking on it in the "Explorer" pane.
5. You will see that it has already been populated with a few code blocks by cookiecutter.
	- At the top, you see the imports. You can leave unchanged for now.

	```python
    from typing import TYPE_CHECKING

    from magicgui import magic_factory
    from qtpy.QtWidgets import QHBoxLayout, QPushButton, QWidget

    if TYPE_CHECKING:
        import napari
	```

    - Next, you see three different ways to add widgets to napari. 
        1. The first subclasses `QWidget` directly. This option provides
maximum flexibility, but means you have to take care of adding all the different GUI elements, laying them out, and hooking them
up to the viewer.

    ```python
    class ExampleQWidget(QWidget):
        # your QWidget.__init__ can optionally request the napari viewer instance
        # in one of two ways:
        # 1. use a parameter called `napari_viewer`, as done here
        # 2. use a type annotation of 'napari.viewer.Viewer' for any parameter
        def __init__(self, napari_viewer):
            super().__init__()
            self.viewer = napari_viewer

            btn = QPushButton("Click me!")
            btn.clicked.connect(self._on_click)

            self.setLayout(QHBoxLayout())
            self.layout().addWidget(btn)

        def _on_click(self):
            print("napari has", len(self.viewer.layers), "layers")    
    ```

        2. The second option is to write a `magic_factory` decorated function. You might recognize this from our `intensify` widget in the lecture. With minimal extra work you can configure options for your GUI elements, such as min and max values for integers, or choices for dropdown boxes. See the [magicgui configuration docs](https://napari.org/magicgui/usage/configuration.html) for details on what you can configure in the decorator.

    ```python
    @magic_factory
    def example_magic_widget(img_layer: "napari.layers.Image"):
        print(f"you have selected {img_layer}")
    ```

        3. Finally, you see the what looks like just a plain function. We don't need complex GUI interactions for our plugin, and we don't want to have to lay out the GUI ourselves, so we will modify this to incorporate our `detect_spots` function.

	```python
    # Uses the `autogenerate: true` flag in the plugin manifest
    # to indicate it should be wrapped as a magicgui to autogenerate
    # a widget.
    def example_function_widget(img_layer: "napari.layers.Image"):
        print(f"you have selected {img_layer}")
	```

    - Find the `Command` ID in `napari.yaml` that points to `example_function_widget`, and then find that `Command` ID in the
`Widgets` contribution section. Note that unlike the other `widget` contributions, this one includes `autogenerate: true`.

    ```yaml
    - command: napari-spot-detector.make_func_widget
      autogenerate: true
      display_name: Example Function Widget
    ```

    - This means our function doesn't need to know anything about magicgui. If we provide type annotations to the parameters,
the GUI widgets will be generated for us without even a decorator!
	
	- Let's edit `example_function_widget` to do our spot detection.
  
1. You can delete everything above `example_function_widget` in `_widget.py` if you want. If you do, make sure to delete the associated `Commands` and `Widget` contributions, and the imports in `napari_spot_detector/__init__.py`!
2. Copy the `gaussian_high_pass()` and `detect_spots()` functions from your notebook from the first part of the tutorial and paste it where the example functions were (the ones you deleted in the previous step).
3. Next, we need to modify `detect_spots()` to return the necessary layer data so that napari can create a new Points layer with our detected spots. If `detect_spots()` returns a `LayerDataTuple`, napari will add a new layer to the viewer using the data in the `LayerDataTuple`. For more information on the `LayerDataTuple` type, please see [the docs](https://napari.org/plugins/guides.html#the-layerdata-tuple).
	- The layer data tuple should be: `(layer_data, layer_metadata, layer_type)`
	- `layer_data`: the data to be displayed in the new layer (i.e., the points coordinates)
	- `layer_metadata`: the display options for the layer stored as a dictionary. Some options to consider: `symbol`, `size`
	- `layer_type`: the name of the layer type as a string (i.e., `'Points'`)
9. Add type annotations to the function parameters (inputs). Napari (via [magicgui](https://napari.org/magicgui/)) will infer the required GUI elements from the type annotations. We have to add annotations to both the parameters (i.e., inputs to the function) and the return type.
10. Annotate the Return type as `"napari.types.LayerDataTuple"`. 
11. Add the required imports for the `scipy.ndimage` module and `scikit-image` `blob_log()` function to the top of the file.
    - `from scipy import ndimage as ndi`
    - `from skimage.feature import blob_log`


### _function.py solution

See below for an example implementation of the `_widget.py` file, and the associated changes to `napari.yaml`

 
```python
# _widget.py
from typing import TYPE_CHECKING


import numpy as np
from scipy import ndimage as ndi
from skimage.feature import blob_log

if TYPE_CHECKING:
    import napari


def gaussian_high_pass(image: np.ndarray, sigma: float = 2):
    """Apply a gaussian high pass filter to an image.

    Parameters
    ----------
    image : np.ndarray
        The image to be filtered.
    sigma : float
        The sigma (width) of the gaussian filter to be applied.
        The default value is 2.

    Returns
    -------
    high_passed_im : np.ndarray
        The image with the high pass filter applied
    """
    low_pass = ndi.gaussian_filter(image, sigma)
    high_passed_im = image - low_pass

    return high_passed_im


def detect_spots(
    image: "napari.types.ImageData",
    high_pass_sigma: float = 2,
    spot_threshold: float = 0.01,
    blob_sigma: float = 2
) -> "napari.types.LayerDataTuple":
    """Apply a gaussian high pass filter to an image.

    Parameters
    ----------
    image : napari.types.ImageData
        The image in which to detect the spots.
    high_pass_sigma : float
        The sigma (width) of the gaussian filter to be applied.
        The default value is 2.
    spot_threshold : float
        The threshold to be passed to the blob detector.
        The default value is 0.01.
    blob_sigma: float
        The expected sigma (width) of the spots. This parameter
        is passed to the "max_sigma" parameter of the blob
        detector.

    Returns
    -------
    layer_data : napari.types.LayerDataTuple
        The layer data tuple to create a points layer
        with the spot coordinates.

    """

    # filter the image
    filtered_spots = gaussian_high_pass(image, high_pass_sigma)

    # detect the spots
    blobs_log = blob_log(
        filtered_spots,
        max_sigma=blob_sigma,
        num_sigma=1,
        threshold=spot_threshold
    )
    points_coords = blobs_log[:, 0:2]
    sizes = 3 * blobs_log[:, 2]

    layer_data = (
        points_coords,
        {
            "face_color": "magenta",
            "size": sizes
        },
        "Points"
    )
    return layer_data

```

```python
# napari_spot_detector/__init__.py
__version__ = "0.0.1"
```

```yaml
#napari.yaml
name: napari-spot-detector
display_name: Spot Detection
contributions:
  commands:
    - id: napari-spot-detector.make_func_widget
      python_name: napari_spot_detector._widget:detect_spots
      title: Make spot detection widget
  widgets:
    - command: napari-spot-detector.make_func_widget
      autogenerate: true
      display_name: Spot Detection
```

## Explore the other files generated by cookiecutter
In order for napari to automatically find and make your plugin available to the user once it has been installed (i.e., "discoverable"), we must add a `napari.manifest` entry point to the `setup.cfg` file. An entry point is a way that a python package can advertise that it has a component available (our plugin in this case). napari searches the python environment for packages that have a `napari.manifest` and then uses the path in the `entry_point` to find `napari.yaml`, where all your
plugin functionality is declared.

If we open the `setup.cfg` file created by `cookiecutter`, we see that the entry point was already added by the cookiecutter template! If you called your plugin `napari-spot-detector` and your module `napari_spot_detector`, you will see the following:

```
[options.entry_points]
napari.manifest =
    napari-spot-detector = napari_spot_detector:napari.yaml
```

Note that `src` doesn't occur in the path to `napari.yaml`, but `napari.yaml` is definitely within the `src` folder! Python knows
to look inside the `src` folder for your code because `setup.cfg` declares an `options.packages.find` key.

```
[options.packages.find]
where = src
```

## Testing/Installing your plugin
To test and use our plugin, we need to install it in our python environment. First, return to your terminal and verify you have the `napari-tutorial` environment activated. Then, navigate to the directory that you created with the cookiecutter. For example, if you named your plugin `napari-spot-detector`, you would enter the following into your terminal.

```bash
cd ~/Documents/napari-spot-detector
```

Then, we install the plugin with pip. pip is the package installer for python (see [the documentation](https://pip.pypa.io/en/stable/) for more information). We will use the `-e` option to install in "editable" mode. This means that when we make a change to our source code, it will update the installed package the next time it is imported, without us having to reinstall it.

```bash
pip install -e .
```

To confirm if your installation completed successfully, you can launch napari from the command line.

```bash
napari
```

Once napari is open, you can open your plugin from the "Plugin" menu. You can test your plugin by locating the spots image from the tutorial notebooks folder (`napari_plugin_intro_workshop`) we downloaded at the beginning of this tutorial in the File browser (`<path to notebook folder>/data/spots_cropped.tif`), dragging the image into the napari viewer, and try running the plugin.

Congratulations! You have made your first napari plugin!


## Bonus exercises

In case you have finished all of the exercises with some time to spare, we have provided some ideas for ways that you can extend the plugin. Please feel free to give them a go and ask the teaching team if you have any questions.

- add sample data to your plugin. To do so, you would need to implement the [sample data contribution](https://napari.org/plugins/guides.html#sample-data)
- add an option to your `detect_spots()` function plugin to return the filtered image in addition to the points layer.
- add some tests to the `_tests/test_widget.py` file.
- upload your plugin to github
- start your own plugin
- consult with the teaching team about integrating napari into your workflow
