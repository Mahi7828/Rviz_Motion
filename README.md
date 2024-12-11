# Rviz_Motion


Make the odom_tf_publisher package. 

go to erc_ws/src

```bash
ros2 pkg create --build-type ament_python odom_tf_publisher
cd odom_tf_publisher

touch odom_tf_publisher.py
chmod +x odom_tf_publisher.py

```

in this odom_tf_publisher.py file , put the following code:
```bash
import rclpy
from rclpy.node import Node
from nav_msgs.msg import Odometry
from geometry_msgs.msg import TransformStamped
from tf2_ros import TransformBroadcaster

class OdomTFPublisher(Node):
    def __init__(self):
        super().__init__('odom_tf_publisher')
        self.subscription = self.create_subscription(
            Odometry,
            '/model/mrs_hudson/odometry',  # Replace with your odometry topic
            self.odom_callback,
            10)
        self.tf_broadcaster = TransformBroadcaster(self)

    def odom_callback(self, msg):
        t = TransformStamped()
        t.header.stamp = msg.header.stamp
        t.header.frame_id = "odom"  # Set to your odom frame
        t.child_frame_id = "base_link"  # Set to your robot's base link
        t.transform.translation.x = msg.pose.pose.position.x
        t.transform.translation.y = msg.pose.pose.position.y
        t.transform.translation.z = msg.pose.pose.position.z
        t.transform.rotation = msg.pose.pose.orientation

        self.tf_broadcaster.sendTransform(t)

def main():
    rclpy.init()
    node = OdomTFPublisher()
    rclpy.spin(node)
    rclpy.shutdown()

if __name__ == '__main__':
    main()
```

Edit the setup.py file of the package 
```bash
entry_points={
    'console_scripts': [
        'odom_tf_publisher = odom_tf_publisher.odom_tf_publisher:main',
    ],
},
```
now,
```bash
cd ~/ros2_ws  # Replace with your workspace path
colcon build
source ~/ros2_ws/install/setup.bash  # Replace with your workspace path

```




Run it 
```bash
ros2 run odom_tf_publisher odom_tf_publisher
```

Now launch the gazebo rviz launch file
```bash
ros2 launch mrs_hudson mrs_hudson_gazebo_rviz.launch.py
```

In the rviz, select odom as the fixed frame. You will be able to see it moving the same way as in gazebo, as you give commands using teleop.

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



