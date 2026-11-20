# MentorPi: Tools

Test and diagnostic scripts. Run them from this folder (`cd ~/MentorPi/Tools`).

| Script | Purpose |
|---|---|
| `test_mentorpi_adapter_mock.py` | Every intent and safety case through the adapter in mock mode. No robot needed. |
| `test_lidar.py` | Checks the LiDAR connection and what it sees. |
| `test_face_detection.py` | Saves a camera frame with face detection drawn on it. |
| `test_rotation_direction.py` | Checks the robot turns toward the person, not away. |
| `test_all_intents.py` | Forces each intent in turn on the real robot. |
| `test_new_motions.py` | Tries the GREET, PULSE and SCAN movements. |
| `real_motion_showcase.py` | Real-motion run through all five intents. |
