# python setup & napari installation (SciPy 2022)

**Note:** If you have any issues with installation, please feel free to write us a message on the [napari zulip](https://napari.zulipchat.com/#narrow/stream/212875-general) and we will try to help you get unstuck.


## Installing python via anaconda

In this tutorial, we will install python via miniforge, a distribution of anaconda python. However, if you already have anaconda, miniconda, or miniforge installed, those will work as well and you can skip to the next section.

1. In your web browser, navigate to the [miniforge page](https://github.com/conda-forge/miniforge). 
2. Scroll down to the "Miniforge3" header of the "Downloads" section. Click the link to download the appropriate version for your operating system. Note that even if you have a new Apple computer with an M1 propcessor, you should download the OS X x86_64 version.
	- Windows: `Miniforge3-Windows-x86_64`
	- Mac with Intel processor: `Miniforge3-MacOSX-x86_64`
	- Mac with M1 ("Apple silicon"): `Miniforge3-MacOSX-x86_64`
	- Linux with an Intel processor: `Miniforge3-Linux-x86_64`
3. Once you have downloaded miniforge installer, run it to install python.
	- **Windows**
		1. Find the file you downloaded (Miniforge3-Windows-x86_64.exe) and double click to execute it. Follow the instructions to complete the installation.
		2. Once the installation has completed, you can verify it was correctly installed by searching for the "miniforge prompt" in your Start menu.
	- **Mac OS**
		1. Open your Terminal (you can search for it in spotlight - `cmd` + `space`)
		2. Navigate to the folder you downloaded the installer to (usually this is in your Downloads folder). If the file was downloaded to your Downloads folder, you would enter:
		
			```bash
			cd ~/Downloads
			```
			
		3. Execute the installer with the command below. You can use your arrow keys to scroll up and down to read it/agree to it.
		
			```bash
			bash Miniforge3-MacOSX-x86_64.sh -b
			```
			
		4. To verify your installation worked, close your Terminal window and open a new one. You should see `(base)` to the left of your prompt.
		5. Finally, initialize miniforge with the command below. This makes sure that your terminal is set up correctly for your python installation.

		
			```bash
			conda init
			```

	- **Linux**
		1. Open your terminal application
		2. Navigate to the folder you downloaded the installer to (usually this is in your Downloads folder). If the file was downloaded to your Downloads folder, you would enter:
		
			```bash
			cd ~/Downloads
			```
			
		3. Execute the installer with the command below. You can use your arrow keys to scroll up and down to read it/agree to it.
		
			```bash
			bash Miniforge3-Linux-x86_64.sh -b
			```
			
		4. To verify your installation worked, close your Terminal window and open a new one. You should see `(base)` to the left of your prompt.
		5. Finally, initialize miniforge with the command below. This makes sure that your terminal is set up correctly for your python installation.
		
			```bash
			conda init
			```

## Setting up your environment
1. Open your terminal.
	- **Windows**: Open the "miniforge prompt" from your start menu
	- **Mac OS**: Open Terminal (you can search for it in spotlight - cmd + space)
	- **Linux**: Open your terminal application
2. We use an environment to encapsulate the python tools used for this workshop. This ensures that the requirements for this workshop do not interfere with your other python projects. To create the environment (named `napari-tutorial`), enter the following command.

	```bash
	conda create -n napari-tutorial python=3.9
	```

3. Once the environment setup has finished, activate the environment. If you successfully activated the environment, you should now see `(napari-tutorial)` to the left of your command prompt.

	```bash
	conda activate napari-tutorial
	```

4. Install the dependencies with the commands below

	```bash
	conda install -c conda-forge notebook
	pip install cookiecutter magicgui "napari[all]"
	pip install stardist-napari
	```

5. If you are on a Mac, please install this one additional dependency.

	```python
	conda install -c conda-forge python.app
	```

6. Test that your notebook installation is working. We will be using notebook for interactive analysis. Enter the command below and it should launch jupyter notebook book in a web browser. Once you've confirmed it launches, close the web browser and press ctrl+c in the terminal window to stop the notebook server.

	```bash
	jupyter notebook
	```

7. Test your napari installation. Enter the command below and an empty napari viewer should open. You can close the window after it opens. Please note that it takes a bit of extra time to launch napari the first time.
	
	```bash
	napari
	```