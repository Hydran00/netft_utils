C++ class and ROS node for ATI force/torque sensors connected to a Netbox. Includes gravity compensation and transformations.

# Usage
There are two main ways to use this package:
1. With a direct ROS node
1. Using `ros2_control` hardware interface and a `force_torque_broadcaster`

To launch the direct node, run the following with the correct IP address
```sh
ros2 run netft_utils netft_node --address WWW.XXX.YYY.ZZZ
```

The direct node publishes `netft_data` in `ft_sensor_frame` by default. These
are the sensor's native axes and measurement origin: the driver scales the
sensor readings but does not apply a TF transform. `--frame_id` overrides the
label only. Add the actual mounting transform to your URDF/TF tree to connect
this frame to the robot.

With the sensor stationary and the mounted tool free of external contact, tare
all six components using:

```sh
ros2 service call /reset_ft std_srvs/srv/Trigger '{}'
```

The service stores the latest raw sample as the bias and subtracts it from
subsequent `netft_data` messages. Each call replaces the previous bias. It
returns `success: false` if no sample has been received. The bias starts at zero
and is lost when the node restarts. This is a single-sample tare, not gravity
compensation: changing the tool orientation can change the measured load.

To run an example launch for `ros2_control` run the following with option namespace
```sh
ros2 launch netft_utils netft.launch.py node_namespace:="test_ns/"
```
