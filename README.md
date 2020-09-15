# amrl

This meta package hosts the packages `graph_navigation` for navigation and `enml` for localization. This code is authored by the University of Texas Autonomous Mobile Robotics Laboratory (AMRL) https://amrl.cs.utexas.edu/

## Installation

Clone this repository in the `src` directory of a catkin workspace, and required simulation environment

    $ git clone https://github.com/Ryangupta8/amrl.git
    $ git clone https://github.com/utexas-bwi/ahg_common.git

Update the submodules

    $ cd amrl
    $ git submodule sync --recursive
    $ git submodule update --init --recursive

Add all the ros-build packages to your path:

    $ cd scripts
    $ ./add_to_path.sh
    
Build dependent msgs package:

    $ cd ../amrl_msgs && export ROS_PACKAGE_PATH=`pwd`:$ROS_PACKAGE_PATH && make

Build the navigation and localization modules

    $ cd ../ && make

if there are no errors, you should now build the catkin packages.

    $ cd ~/catkin_ws && catkin build ahg_common 

