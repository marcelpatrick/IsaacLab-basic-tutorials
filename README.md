# IsaacLab-basic-tutorials

This readme follows the Basic tutorials by LycheeAI: [https://www.youtube.com/@LycheeAI](https://lycheeai-hub.com/isaac-lab/basics-videos) and adds extra information to them. 
It runs basic scripts inside ``C:\Users\[YOUR USER]\IsaacLab\scripts\tutorials\00_sim``, or wherever you have installed the github project, and explains the code in them. 

## 0- Setup

- Open the Anaconda Prompt terminal and activate the conda env you have created with isaacsim and isaaclab installed and all the dependencies and the isaacsim/isaaclab base github project. If you haven't done it yet you can follow this tutorial: https://github.com/marcelpatrick/IsaacSim-IsaacLab-installation-for-Windows-Easy-Tutorial
- type ``code .`` to open vs code from inside your anaconda env. This will open VS Code with the correct python interpreter from this env and the VS code terminal will also run inside this env. 
- On the folder structure on the left, navigate to the isaaclab project or tutorial you want to run
- click the "run python file" button on VS code.
- All the code files here below are from inside the isaaclab project folder located in ```C:\Users\[YOUR USER]\IsaacLab\scripts\tutorials``` or wherever you have installed the isaaclab github project.
- More information is passed in the commented lines inside code snippets. 


## 1- ```create_empty.py```: Define the Argparser and launch the app with AppLauncher function
- Applauncher is a wrapper function that makes it easier to launch the simulation and handle configs with command line args
- Argparser Prepares the code to receive arguments from users typing commands in a CLI such as ``app.py --headless`` and which arguments it accepts.

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

## 2- ```spawn_prims.py``: Defines prims config files and spawns then in the scene based on the attributes set in these files

```
def design_scene():
    """Designs the scene by spawning ground plane, light, objects and meshes from usd files."""
    # Ground-plane
    # Configuration object is created  
    # configuration driven interface: each prim is spawned using its configuration object which defines its properties, and relationships.
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
