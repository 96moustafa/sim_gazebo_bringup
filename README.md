# ROS2 package: sim_gazebo_bringup

## Overview
Generic simulation bringup from JSON

This package is a modified version of [this package](https://github.com/rudislabs/sim_gazebo_bringup) that was originally created by Rudis Labs and it launches multiple PX4 instances in simulation.

A scenario is described in a file called **gen_params.json**.

This file is as follows:
```json
{	"setup":{
		"autopilot": { //autopilot repo that will be cloned
			"autopilot_build_params_0": {
				"repo": "git@github.com:96moustafa/PX4-Autopilot-Avoidance.git",
				"name": "PX4-Autopilot-Avoidance",
				"version": "master",
				"clone": 1,
				"gazebo_plugins": 1,
				"build_type": "px4_sitl_rtps",
				"build_prefix": "HEADLESS=1",
				"build_postfix": "gazebo"
			}
		},
		"gazebo": {
			"gazebo_models": {
				"gazebo_repo_0":{
					"repo": "git@github.com:rudislabs/tii_gazebo.git",
					"version": "main",
					"name": "tii_gazebo"
				}
			}
		}
	},
	"world_params": {//configuration of Gazebo world parameters
		"gazebo_name": "tii_gazebo",
		"generate_world": 1,
		"generate_params":{
			"sdf_version": "NotSet",
			"sun_model": "sunSolarNoon",
			"sun_utc_date": "1904_09_20_17_30",
			"cloud_speed": "NoClouds",
			"shadows": 1,
			"video_widget": "NotSet",
			"update_rate": 250,
			"wind_speed": "NotSet",
			"realtime_factor": 1.0,
			"world_name": "empty",
			"ambient_light": 0.5,
			"background_light": 0.15,
			"spherical_coords": 1,
			"latitude": 39.8039,
			"longitude": -84.0606,
			"altitude": 244,
			"irlock_beacon_pose": "NotSet",
			"ode_threads": 2
		}
	},
	"models": {
		"model_params_0": {// first vehicle
			"gazebo_name": "tii_gazebo", // gazebo to be used
			"autopilot_name": "PX4-Autopilot-Avoidance",// autopilot to be used
			"autopilot_build_type": "px4_sitl_rtps",
			"spawn_pose": [0, -50, 0, 0, 0, 1.57], // (x,y,z,-,-,yaw) wrt lat,lon,alt of the gazebo world.
			"generate_params":{
				"base_model": "iris",
				"sdf_version": "NotSet",
				"mavlink_tcp_port": 4560,
				"mavlink_udp_port": 14560,
				"qgc_udp_port": 14550,
				"sdk_udp_port": 14540,
				"serial_enabled": 0,
				"serial_device": "/dev/ttyACM0",
				"serial_baudrate": 921600,
				"enable_lockstep": 1,
				"hil_mode": 0,
				"model_name": "NotSet"
			}
		},
		"model_params_1": {// second vehicle
			"gazebo_name": "tii_gazebo",
			"autopilot_name": "PX4-Autopilot-Avoidance",
			"autopilot_build_type": "px4_sitl_rtps",
			"spawn_pose": [0, 50, 0, 0, 0, -1.57],
			"generate_params":{
				"base_model": "iris",
				"sdf_version": "NotSet",
				"mavlink_tcp_port": 4561,
				"mavlink_udp_port": 14561,
				"qgc_udp_port": 14551,
				"sdk_udp_port": 14541,
				"serial_enabled": 0,
				"serial_device": "/dev/ttyACM0",
				"serial_baudrate": 921600,
				"enable_lockstep": 1,
				"hil_mode": 0,
				"model_name": "NotSet"
			}
		}
	}
}

```

Each scenario is described using a **gen_params.json** file in a separate directory with the unique name of the scenario.

## How to use it
1. Create your scenario by creating a directory with your scenario's name and adding a json file with the required configuration inside it.
2. Build this package in ROS2 workspace directory using the following command:
   ```
   ~/src/ros2_ws$ colcon build --symlink-install --packages-select sim_gazebo_bringup 
   ```
3. Now you can run the sim_gazebo_bringup to launch PX4 instances in simulation.  
    ```
    $ ros2 launch sim_gazebo_bringup sim_gazebo.launch.py 
    ```

    > Note: The first time you run the sim_gazebo.launch.py, it will clone the specified PX4-Autopilot repository and the specified gazebo repository from Github in the parent directory of your ros2_ws directory. Therefore, it is important to have an SSH connection to your Github account. 

 4. After running the script, it will parse the scenarios directories and you will be prompted to choose a scenario by entering its corresponding number.

    ```bash
    The available scenarios are: 
    0) 2_uav_acute_angle 
    1) 2_uav_head_on 
    2) 2_uav_obtuse_angle 
    3) 2_uav_right_angle 
    4) 4_uav_square 
    Enter the number of the required scenario: 4
    ```

    Choose the required scenario's number and press enter. 

    You should see an x-terminal for each PX4 instance. 

    The MAVLink ID of the generated PX4 instances starts from 1 and increments. However, the instance numbering starts from 0. For example, instance #0 has the mavlink id #1 and instance #1 has the mavlink id #2 and so on. 

    Also, micrortps_client starts automatically with the following UDP ports configutation: 

    micrortps_client start -t UDP -r $((2019+2*px4_instance)) -s $((2020+2*px4_instance))  

    For example, instance #0 receives on port 2019 and sends on UDP port 2020  

    If you open QGC, you should see the spawned vehicles of the chosen scenario in the corresponding location. 




