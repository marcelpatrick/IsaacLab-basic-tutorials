# IsaacLab-basic-tutorials

- This readme follows the Basic tutorials by LycheeAI: [https://www.youtube.com/@LycheeAI](https://lycheeai-hub.com/isaac-lab/basics-videos) and adds extra information to them. 
- It uses files from the IsaacLab **GitHub Repo**: https://github.com/isaac-sim/IsaacLab 
- It goes through the main Python files in this IsaacLab GitHub repo and explains what they do.
- It runs scripts from inside: ``C:\Users\[YOUR USER]\IsaacLab\scripts\tutorials\00_sim``, or wherever you have installed the github project.

# Setup

- Open the Anaconda Prompt terminal and activate the conda env you have created with isaacsim and isaaclab installed and all the dependencies and the isaacsim/isaaclab base github project. If you haven't done it yet you can follow this tutorial: https://github.com/marcelpatrick/IsaacSim-IsaacLab-installation-for-Windows-Easy-Tutorial
- type ``code .`` to open vs code from inside your anaconda env. This will open VS Code with the correct python interpreter from this env and the VS code terminal will also run inside this env. 
- On the folder structure on the left, navigate to the isaaclab project or tutorial you want to run
- click the "run python file" button on VS code.

# Standard Functions
- Standard functions that appear in most IsaacLab code scripts

## 0. Argparse
- Defines which command-line inputs the device can receive to support customized ways to run the simulation. eg ``--headless``, ``--device cpu, --device gpu``
- Has to be run before any imports
  
## 0. AppLauncher()
- Launches the simulation handling command line args

## 1. Design_scene()
- Spawns primitives (objects) in the scene using func() and ObjectCfg
#### 1.1. func(): 
- spawn the objects in the scene based on their config files
#### 1.2. ObjectCfg: RigidObjectCfg(), DeformableObjectCfg()...
- Assets' config files with their standard configurations.

## 2. run_simulator()
- A specific service in the code that runs and controls the simulation

## 3. main()
- Triggers the whole program defined by the Python script, owns the overall program flow. 
- calls design_scene() and run_simulator()

  
# tutorials\00_sim: create_empty.py

## 0. Argparse and AppLauncher
- Defines the Argparser and launches the app with AppLauncher function
- Standard setup sequence for every Isaac Lab script. Initializes the simulation environment.

```python
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

# tutorials\00_sim: spawn_prims.py
- Defines prims config files and spawns them in the scene based on the attributes set in these files

## 1. Design_scene(): 
- Spawns primitives (objects) in the scene

```python
def design_scene():
    """Designs the scene by spawning ground plane, light, objects and meshes from usd files."""
    # Ground-plane
    # Configuration object is created  
    # configuration-driven interface: each prim is spawned using its configuration object which defines its properties, and relationships.
    # Config objects are then used with spawn functions to create the prims in the scene.
    # These config files are like blueprints or receipes that the spawn functions func() use to create the actual prims in the simulation.
    cfg_ground = sim_utils.GroundPlaneCfg()

    # Spawn functions
    # func() spawns objects into the scene by intepreting the configuration settings provided
    cfg_ground.func("/World/defaultGroundPlane", cfg_ground)

    # spawn distant light
    cfg_light_distant = sim_utils.DistantLightCfg(
        intensity=3000.0,
        color=(0.75, 0.75, 0.75),
    )
    cfg_light_distant.func("/World/lightDistant", cfg_light_distant, translation=(1, 0, 10))

    # Transform Prim (Xform): The arrow that moves objects in the scene
    # contains only transformation properties (position, rotation, scale) 
    # create a new xform prim for all objects to be spawned under
    prim_utils.create_prim("/World/Objects", "Xform")
    
    # spawn a red cone
    cfg_cone = sim_utils.ConeCfg(
        radius=0.15,
        height=0.5,
        visual_material=sim_utils.PreviewSurfaceCfg(diffuse_color=(1.0, 0.0, 0.0)),
    )
    cfg_cone.func("/World/Objects/Cone1", cfg_cone, translation=(-1.0, 1.0, 1.0))
    cfg_cone.func("/World/Objects/Cone2", cfg_cone, translation=(-1.0, -1.0, 1.0))

    # Prims with Physics properties such as Rigid Body, Colliders, Deformable Body.
    # spawn a green cone with colliders and rigid body
    cfg_cone_rigid = sim_utils.ConeCfg(
        radius=0.15,
        height=0.5,
        rigid_props=sim_utils.RigidBodyPropertiesCfg(),
        mass_props=sim_utils.MassPropertiesCfg(mass=1.0),
        collision_props=sim_utils.CollisionPropertiesCfg(),
        visual_material=sim_utils.PreviewSurfaceCfg(diffuse_color=(0.0, 1.0, 0.0)),
    )
    cfg_cone_rigid.func(
        "/World/Objects/ConeRigid", cfg_cone_rigid, translation=(-0.2, 0.0, 2.0), orientation=(0.5, 0.0, 0.5, 0.0)
    )

    # Deformable Body Prims
    # spawn a blue cuboid with deformable body. 
    # DeformableBodyPropertiesCfg defines how it deforms such as stiffness, damping, etc.
    cfg_cuboid_deformable = sim_utils.MeshCuboidCfg(
        size=(0.2, 0.5, 0.2),
        deformable_props=sim_utils.DeformableBodyPropertiesCfg(),
        visual_material=sim_utils.PreviewSurfaceCfg(diffuse_color=(0.0, 0.0, 1.0)),
        physics_material=sim_utils.DeformableBodyMaterialCfg(),
    )
    cfg_cuboid_deformable.func("/World/Objects/CuboidDeformable", cfg_cuboid_deformable, translation=(0.15, 0.0, 2.0))

    # Spawning prims from another file
    # spawn a usd file of a table into the scene
    # Imports a table from the Isaac Nucleus asset library and places it in the scene
    cfg = sim_utils.UsdFileCfg(usd_path=f"{ISAAC_NUCLEUS_DIR}/Props/Mounts/SeattleLabTable/table_instanceable.usd")
    cfg.func("/World/Objects/Table", cfg, translation=(0.0, 0.0, 1.05))
```

# tutorials\01_assets: run_rigid_object.py

## 0. AppLauncher and Argparser

```
"""Launch Isaac Sim Simulator first."""

import argparse

from isaaclab.app import AppLauncher

# add argparse arguments
parser = argparse.ArgumentParser(description="Tutorial on spawning and interacting with a rigid object.")
# append AppLauncher cli args
AppLauncher.add_app_launcher_args(parser)
# parse the arguments
args_cli = parser.parse_args()

# launch omniverse app
app_launcher = AppLauncher(args_cli)
simulation_app = app_launcher.app
```

## 0. Imports
```python
import torch

import isaacsim.core.utils.prims as prim_utils

import isaaclab.sim as sim_utils
import isaaclab.utils.math as math_utils
from isaaclab.assets import RigidObject, RigidObjectCfg
from isaaclab.sim import SimulationContext
```
- Have to run **after** AppLauncher and Argparser

## 1. design_scene(): Spawns primitives (objects) in the scene
```python
def design_scene():
    """Designs the scene."""
    # Ground-plane
```
### 1.2. Configuring and Spawning Primitives: Config objects and the func() function 

- **cfg = sim_utils.GroundPlaneCfg()**
    - IsaacLab uses a **configuration-driven interface**: each prim is spawned using its configuration object which defines its properties, and relationships.
    - These config files are like blueprints or recipes
    - Easy Modification: Since the behavior is defined by data (the settings in the Cfg objects) and not hardcoded logic, you can easily change the entire experiment (e.g., swapping a wheeled robot for a legged robot) just by changing which configuration object you use. This makes the framework modular and efficient for creating and modifying environments.
    - This system is essential for scaling up reinforcement learning, as it allows Isaac Lab to efficiently create thousands of parallel copies of your simulation environment for training.
    - cfg contains the **standard** configurations of each assets
    - Since these are basic static background elements, they are immediately spawned and don't need to be passed to main() during the simulation loop; they don't need to be returned from the function.

- **func()**
- Func() are like spawn functions that actually spawn the objects in the scene based on their config files. They take the location of the object files to be created and its configuration (saved inside the cfg object itself)

```python
    cfg = sim_utils.GroundPlaneCfg()
    cfg.func("/World/defaultGroundPlane", cfg)
    # Lights
    cfg = sim_utils.DomeLightCfg(intensity=2000.0, color=(0.8, 0.8, 0.8))
    cfg.func("/World/Light", cfg)
```
- Create XForms (arrows), one for each object -> scene_origins
- Define where objects get spawned
- And each of these locations will be a separate simulation environment, simulating one different entity during RL
  
```python
    # Create separate groups called "Origin1", "Origin2", "Origin3"
    # Each group will have a robot in it
    origins = [[0.25, 0.25, 0.0], [-0.25, 0.25, 0.0], [0.25, -0.25, 0.0], [-0.25, -0.25, 0.0]]
    for i, origin in enumerate(origins):
        prim_utils.create_prim(f"/World/Origin{i}", "Xform", translation=origin)
```
### 1.3. Defining assets with custom config: RigidObjectCfg()
- This approach instantiates a config object while passing specific config params inside it using the ``RigidObjectCfg`` class
- that's why it doesn't use the ``cfg = sim_utils.RigidObjectCfg()`` ``cfg.func("/World/RigidObject", cfg)`` approach
  
```python
    # Rigid Object
    cone_cfg = RigidObjectCfg(
        prim_path="/World/Origin.*/Cone",
        spawn=sim_utils.ConeCfg(
            radius=0.1,
            height=0.2,
            rigid_props=sim_utils.RigidBodyPropertiesCfg(),
            mass_props=sim_utils.MassPropertiesCfg(mass=1.0),
            collision_props=sim_utils.CollisionPropertiesCfg(),
            visual_material=sim_utils.PreviewSurfaceCfg(diffuse_color=(0.0, 1.0, 0.0), metallic=0.2),
        ),
        # Set the initial position of this object
        init_state=RigidObjectCfg.InitialStateCfg(),
    )
    cone_object = RigidObject(cfg=cone_cfg)

    # return the scene information
    scene_entities = {"cone": cone_object}
    return scene_entities, origins
```

## 2. run_simulator(): 
- keeps the simulation in a loop (steps), interacts with prims in the scene, resets object states, updates internal buffers to update new object states etc.
- Specifies the location of the objects according to the global coordinates of the environment.

```python
def run_simulator(sim: sim_utils.SimulationContext, entities: dict[str, RigidObject], origins: torch.Tensor):
    """Runs the simulation loop."""
    # Extract scene entities
    # note: we only do this here for readability. In general, it is better to access the entities directly from
    #   the dictionary. This dictionary is replaced by the InteractiveScene class in the next tutorial.
    cone_object = entities["cone"]
    # Define simulation stepping
    sim_dt = sim.get_physics_dt()
    sim_time = 0.0
    count = 0
    # Simulate physics
    while simulation_app.is_running():
        # reset
        if count % 250 == 0:
            # reset counters
            sim_time = 0.0
            count = 0
            # reset root state
            root_state = cone_object.data.default_root_state.clone()
            # sample a random position on a cylinder around the origins
            root_state[:, :3] += origins
            root_state[:, :3] += math_utils.sample_cylinder(
                radius=0.1, h_range=(0.25, 0.5), size=cone_object.num_instances, device=cone_object.device
            )
            # write root state to simulation
            cone_object.write_root_pose_to_sim(root_state[:, :7])
            cone_object.write_root_velocity_to_sim(root_state[:, 7:])
            # reset buffers
            cone_object.reset()
            print("----------------------------------------")
            print("[INFO]: Resetting object state...")
        # apply sim data
        cone_object.write_data_to_sim()
        # perform step
        sim.step()
        # update sim-time
        sim_time += sim_dt
        count += 1
        # update buffers
        cone_object.update(sim_dt)
        # print the root position
        if count % 50 == 0:
            print(f"Root position (in world): {cone_object.data.root_pos_w}")
```

## 3. Main
- calls design_scene() and fetches scene_entities, scene_origins
- sim.reset(): resets the simulation for it to be ready to play before running the simulator. Re-initializes the entire environment to a clean starting state, allowing the next training episode to begin.
- calls run_simulator() taking scene_entities, scene_origins retrieved from design_scene()

```python
def main():
    """Main function."""
    # Load kit helper
    sim_cfg = sim_utils.SimulationCfg(device=args_cli.device)
    sim = SimulationContext(sim_cfg)
    # Set main camera
    sim.set_camera_view(eye=[1.5, 0.0, 1.0], target=[0.0, 0.0, 0.0])
    # Design scene
    scene_entities, scene_origins = design_scene()
    scene_origins = torch.tensor(scene_origins, device=sim.device)
    # Play the simulator
    sim.reset()
    # Now we are ready!
    print("[INFO]: Setup complete...")
    # Run the simulator
    run_simulator(sim, scene_entities, scene_origins)


if __name__ == "__main__":
    # run the main function
    main()
    # close sim app
    simulation_app.close()
```











