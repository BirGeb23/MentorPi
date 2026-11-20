# MentorPi: Dashboards

```
cd ~/MentorPi/Dashboards
python3 dashboard_server.py          # live monitor  -> http://192.168.149.1:5000/
python3 intent_demo_server.py        # button demo   -> http://192.168.149.1:5050/  (add --real-motion to move)
```

- **Live monitor:** current intent, VA values, motion, distance and safety state. It reads `/tmp/spot_dashboard_status.json`, written by `../VA_Pipeline/continuous_runner_http.py` and `../Gesture/run_gesture_mentorpi.py`, so start one of them in another terminal.
- **Demo panel:** trigger any intent with a button, no pipeline needed.
