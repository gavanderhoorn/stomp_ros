# Industrial MoveIt


#### Build
- Build the workspace:
  - Cd into the catkin workspace directory and type the following command:
```
catkin build
```

#### Unit Test
- Run all of industrial_moveit unit tests:
  - Cd into the catkin workspace directory and type the following command:
```
catkin run_tests 
```
- Run the stomp_core unit tests:
```
catkin run_tests stomp_core
```


#### Stomp Moveit Demo
- Run the demo
  - Run the demo.launch file
  ```
  roslaunch stomp_test_kr210_moveit_config demo.launch
  ```

  - In the Rviz Motion Planning planel, select **rail_start_pose** from the dropdown menu under "Select Start State" and click **Update**
  - In Rviz, select **rail_end_pose** from the dropdown menu under "Select Goal State" and click **Update**  
  - Click **Plan** in order to generate a motion plan.

#### Configure Stomp
- Locate the the stomp planner configuration file
  - **roscd** into the **stomp_test_kr210_moveit_config** package and locate the "stomp_config.yaml" file under the config directory
- Rerun demo.launch file and plan once again to see how the changes affect the planner's behavior.

#### Seeding Stomp
The STOMP planner works through optimization: it starts with a given trajectory, called the ***seed***, and iteratively attempts to improve it. This seed is set:
 1. By default, it is set to the joint interpolated path between the start and end joint configurations.
 2. If you wish, you can set your own seed trajectory.

The `StompPlanner` class works off of the `moveit_msgs/MotionPlanRequest` message type which does not provide an interface for seeds. Until that is added, we bastardize the unused `MotionPlanRequest::trajectory_constraints` field to serve this purpose. Use the `StompPlanner::encodeSeedTrajectory(const trajectory_msgs::JointTrajectory& seed)` static function to do this:

```c++

StompPlanner planner = makeStompPlanner(); // However you initialize
planning_interface::MotionPlanRequest request;

// set your nominal goals, start conditions, etc...

trajectory_msgs::JointTrajectory seed_traj; // Look up your seed traj
request.trajectory_constraints = StompPlanner::encodeSeedTrajectory(seed_traj);

// Call the planning service or the planner itself
planner.setMotionPlanRequest(request)

MotionPlanResponse res;
planner.solve(res);
``` 
There is no current way to set this through the MoveGroupInterface class. 

========================================================================  
[ROS-Industrial]: http://www.ros.org/wiki/Industrial  
[ROS wiki]: http://ros.org/wiki/industrial_moveit  
[ConstrainedIK]: http://wiki.ros.org/constrained_ik
[Stomp MoveIt!]: http://wiki.ros.org/stomp_moveit

