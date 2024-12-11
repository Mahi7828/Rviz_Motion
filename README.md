# Rviz_Motion

change the sdf and urdf file plugins 

sdf
```
<plugin filename="libignition-gazebo-diff-drive-system.so" name="ignition::gazebo::systems::DiffDrive">
    <left_joint>left_wheel_joint</left_joint>
    <right_joint>right_wheel_joint</right_joint>
    <wheel_separation>0.1182</wheel_separation>
    <wheel_radius>0.04</wheel_radius>
    <odom_publish_frequency>50</odom_publish_frequency>
    <odom_frame>odom</odom_frame> <!-- Specify the odometry frame -->
    <robot_base_frame>base_link</robot_base_frame> <!-- Link base_link to odom -->
    <topic>cmd_vel</topic>
    <publish_tf>true</publish_tf> <!-- Ensure TF is published -->
</plugin>
```
Make the odom_tf_publisher package. 
python code :


Run it 
```bash
ros2 run odom_tf_publisher odom_tf_publisher
```

Now launch the gazebo rviz launch file
```bash
ros2 launch mrs_hudson mrs_hudson_gazebo_rviz.launch.py
```

for publishing the data: 
```bash
ros2 topic echo /model/mrs_hudson/odometry
```
To check the TF tree
```bash
ros2 run tf2_tools view_frames
```

not req: 
bridge odometry data to /odom topic
```bash
ros2 run ros_gz_bridge parameter_bridge /model/mrs_hudson/odometry@nav_msgs/msg/Odometry[ignition.msgs.Odometry]
```



