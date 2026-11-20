# MentorPi: Robot (shared code)

Used by both `../VA_Pipeline/` and `../Gesture/` (and the dashboards and tools). The scripts there add this folder to their import path automatically.

| File | What it does |
|---|---|
| `mentorpi_adapter.py` | Intent → wheel motion (plus the GREET, ACK nod, PULSE and SCAN movements). Mock mode prints instead of moving. |
| `safety.py` | Checks every command: speed limits (0.25 m/s, 0.5 rad/s), never forward closer than 0.8 m, stop when the person is lost, stop for obstacles within 0.4 m, half speed below 20% battery, E-stop. |
| `person_tracker.py` | Face tracking: is a person visible, their direction and distance. |
| `lidar_reader.py` | Distance to the person from the LiDAR. |

The robot's ROS 2 bringup (Docker container `MentorPi`) provides the camera at `http://127.0.0.1:8080/stream?topic=/ascamera/camera_publisher/rgb0/image` and motion/LiDAR through `rosbridge` on `ws://127.0.0.1:9090`; wheel commands go to `/controller/cmd_vel`. `ros2` commands only work inside the container (`docker exec -it MentorPi bash`).
