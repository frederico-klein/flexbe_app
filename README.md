# FlexBE App

User interface (editor + runtime control) for the FlexBE behavior engine.

![FlexBE CI](https://github.com/FlexBE/flexbe_app/workflows/FlexBE%20CI/badge.svg?branch=noetic)

## Installation

Clone the following repos into your ROS workspace:

    git clone https://github.com/FlexBE/flexbe_behavior_engine.git  # if not already present
    git clone https://github.com/FlexBE/flexbe_app.git

Build you workspace:

    catkin_make # or catkin build

During the build process, the required nwjs binaries are automatically downloaded and extracted.
To download the binaries manually instead, run the script `bin/nwjs_install`.

## Workspace

In order to create and prepare a new repository for behavior development, run the following script in your ROS workspace and pass it the name of your project or identifier:

    rosrun flexbe_widget create_repo [your_project_name]

This will initialize a new local git repository with the correct workspace structure which you can then push to a desired remote location. Make sure that you build the workspace afterwards.

## Usage

If desired, run the following command to create a shortcut in the application menu:

    rosrun flexbe_app shortcut create
    rosrun flexbe_app shortcut remove  # if you want to remove it

Use the shortcut or the following command to run the FlexBE App alone, i.e., for only developing behaviors, but not executing them:

    rosrun flexbe_app run_app --offline

Omitting the `--offline` arg will let the App try to connect to ROS. You can manually connect/disconnect the App in the *Configuration* view.

Use the following launch file to run FlexBE's operator control station, i.e., everything FlexBE requires on the operator machine for controlling behavior execution:

    roslaunch flexbe_app flexbe_ocs.launch
    roslaunch flexbe_onboard behavior_onboard.launch  # counterpart expected to run on the robot

Use the following launch file to run both of the above for local behavior execution:

    roslaunch flexbe_app flexbe_full.launch


## Backwards Compatibility

The FlexBE App in this repository replaces the previous *flexbe_chrome_app*. 

---

Please note that the way how state and behavior packages are detected has changed and breaks direct compatibility.
Follow the instructions below to make the required changes.
Behavior packages can also be converted automatically by the new FlexBE App.

### State packages

A package is a state package for FlexBE if its `package.xml` declares the export of `flexbe_states`:

    <package>
    ...
      <export>
        <flexbe_states />
      </export>
    ...
    </package>

It is then expected to provide Python class definitions as described in [Developing Basic States](http://wiki.ros.org/flexbe/Tutorials/Developing%20Basic%20States). Example: [flexbe_states](https://github.com/FlexBE/flexbe_behavior_engine/tree/feature/flexbe_app/flexbe_states). Adding the above export statement is the only change to previous versions.

### Behavior packages

A behavior package contains the code and manifest files generated by the FlexBE App. Usually, you do not need to modify it manually. Again, a behavior package is identified by an export statement in its `package.xml`:

    <package>
    ...
      <export>
        <flexbe_behaviors />
      </export>
    ...
    </package>

If no package in the `ROS_PACKAGE_PATH` contains such an export statement, the FlexBE App will suggest to initialize one of the existing packages as behavior package. Doing so will add the export statement and create the correct structure. If the chosen package already contains behavior manifests, the referenced behaviors can be automatically imported. The old behavior packages and manifests (all manifests outside the manifest folder, e.g., those inside the behaviors folder) can be removed afterwards.

In order to create a completely new behavior package, create an empty ROS package and then use the FlexBE App to initialize it.

A behavior package is expected to provide a `manifest` folder which contains the manifests for all provided behaviors. The behaviors are located in a Python module named like the package and contained in the `src` folder.
