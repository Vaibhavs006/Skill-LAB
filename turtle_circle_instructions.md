# Turtle Circle — ROS2 Package

## 1. Create workspace

```bash
mkdir -p ~/ros2_ws/src
cd ~/ros2_ws
colcon build
source install/setup.bash
```

## 2. Create package

```bash
cd ~/ros2_ws/src
ros2 pkg create --build-type ament_python turtle_circle --dependencies rclpy geometry_msgs
```

## 3. Create the node file

```bash
cd ~/ros2_ws
code .
```
create circle_node.py in src/turtle_circle/turtle_circle
Paste into `circle_node.py`:

```python
import rclpy
from rclpy.node import Node
from geometry_msgs.msg import Twist

class CircleNode(Node):

    def __init__(self):
        super().__init__('circle_node')

        # Create publisher
        self.publisher_ = self.create_publisher(
            Twist,
            '/turtle1/cmd_vel',
            10
        )

        # Timer calls move_turtle every 0.1 sec
        self.timer = self.create_timer(
            0.1,
            self.move_turtle
        )

    def move_turtle(self):

        msg = Twist()

        # Forward speed
        msg.linear.x = 2.0

        # Turning speed
        msg.angular.z = 1.0

        self.publisher_.publish(msg)

        self.get_logger().info('Moving in circle')

def main(args=None):

    rclpy.init(args=args)

    node = CircleNode()

    rclpy.spin(node)

    node.destroy_node()

    rclpy.shutdown()

if __name__ == '__main__':
    main()
```

## 4. Update `setup.py`



Add the entry point inside `console_scripts`:

```python


    entry_points={
        'console_scripts': [
            'circle_node = turtle_circle.circle_node:main',
        ],


## 5. Update `package.xml`

File: `~/ros2_ws/src/turtle_bringup/package.xml`

```xml


  <depend>rclpy</depend>
  <depend>geometry_msgs</depend>

  
```

## 6. Build

```bash
cd ~/ros2_ws
colcon build --packages-select turtle_circle
source install/setup.bash
```

## 7. Run

**Terminal 1:**
```bash
ros2 run turtlesim turtlesim_node
```

**Terminal 2:**
```bash
source ~/ros2_ws/install/setup.bash
ros2 run turtle_circle circle_node
```

## 8. Verify

```bash
ros2 topic echo /turtle1/cmd_vel
ros2 topic hz /turtle1/cmd_vel
ros2 node list
```

