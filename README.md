# IsaacLab-basic-tutorials

Source: following tutorials by LycheeAI: https://www.youtube.com/@LycheeAI

Tutorial 1- https://www.youtube.com/watch?v=sL1wCfp9tRU&t=10s 

## 0- Setup

- Open the Anaconda Prompt terminal and activate the conda env you have created with isaacsim and isaaclab installed and all the dependencies. If you haven't done it yet you can follow this tutorial: https://github.com/marcelpatrick/IsaacSim-IsaacLab-installation-for-Windows-Easy-Tutorial
- type ``code .`` to open vs code from inside your anaconda env. This will open VS Code with the correct python interpreter from this env and the VS code terminal will also run inside this env. 
- On the folder structure on the left, navigate to the isaaclab project or tutorial you want to run
- click the "run python file" button on VS code.

## Code Explanation

### 1- Define the Argparser and launch the app with AppLauncher function
- Prepares the code to receive arguments from users typing commands in a CLI such as ``app.py --headless`` and which arguments it accepts.

```
# create argparser
parser = argparse.ArgumentParser(description="Tutorial on creating an empty stage.")
# append AppLauncher cli args
AppLauncher.add_app_launcher_args(parser)
# parse the arguments
args_cli = parser.parse_args()
# launch omniverse app
app_launcher = AppLauncher(args_cli)
simulation_app = app_launcher.app
```
