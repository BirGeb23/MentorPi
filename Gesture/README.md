# MentorPi: Gesture Pipeline

Drives the MentorPi with **hand gestures**. MediaPipe's Gesture Recognizer reads a held hand sign, turns it into one of the five intents, and drives the wheels through the shared robot code in `../Robot/` (adapter + safety layer).

| Gesture | Intent | Wheel motion |
|---|---|---|
| Thumb up | `ENGAGE` | Forward 0.15 m/s, turns to face you, stops at 1.0 m |
| Pointing up | `CHECK_IN` | "I see you" nod, then forward 0.08 m/s, stops at 1.5 m |
| Open palm | `CAUTION` | Stops; one SCAN (backs up turning left/right) if held |
| Thumb down | `DE_ESCALATE` | Backward 0.10 m/s until 2.0 m away |
| No hand / other | `NEUTRAL` | Stops; small PULSE every 20 s while idle |

## Files

| File | What it does |
|---|---|
| `run_gesture_mentorpi.py` | Gesture → intent → wheel motion. |
| `gesture_check.py` | Accuracy test tool (no motion): how often a held gesture is recognized. |

## How it decides

- **Face first:** the face tracker finds the person, and the gesture model looks at a crop that follows them (sized from the face). Without the crop, hands are too small to recognize at 1.5 m.
- **Hold check:** a gesture only counts after at least 4 frames of it within 1 s, so one misread frame can't move the robot.
- **Repeat and release:** while the gesture is held, the intent is re-sent every 2.5 s. 1.5 s after the hand drops, the robot returns to NEUTRAL.
- **Person required:** gestures only count while a face was seen in the last 2 s.
- **Working range:** 1.5–2 m.

## Setup (on the Pi, once)

1. This repo at `~/MentorPi`, and the Python environment:
   ```
   python3 -m venv ~/va_pipeline_venv
   source ~/va_pipeline_venv/bin/activate
   pip install opencv-python mediapipe==0.10.9 websocket-client numpy
   ```
   Keep MediaPipe at 0.10.9 (the face tracker uses its older `mp.solutions` API).
2. The gesture model, downloaded on a laptop with internet (the Pi has none on the MentorPi hotspot) and copied over:
   ```
   curl -L -o gesture_recognizer.task https://storage.googleapis.com/mediapipe-models/gesture_recognizer/gesture_recognizer/float16/latest/gesture_recognizer.task
   scp gesture_recognizer.task pi@192.168.149.1:~/MentorPi/Gesture/
   ```

## Running

Starts in **mock mode** (prints the wheel commands; the robot doesn't move).

```
source ~/va_pipeline_venv/bin/activate
cd ~/MentorPi/Gesture
python3 run_gesture_mentorpi.py                  # mock, until Ctrl+C
python3 run_gesture_mentorpi.py --real-motion    # wheels move
```
Options: `--duration 60`, `--debug` (every frame's gesture), `--snapshot PATH` (saves the view after 10 s with the face box green and the crop blue), `--face-conf 0.3` (dim or backlit rooms), `--no-lidar` (face-size distance instead of LiDAR).

**Accuracy test** (hold one gesture for the whole run):
```
python3 gesture_check.py --duration 20 --delay 5 --crop 0.5 --crop-y 1.0 --expect Thumb_Up
```
The camera points upward, so people appear low in the frame: try `--crop-y` between 0.8 and 1.0. `--snapshot PATH` saves what the model saw.

**Live monitor:** while the runner is going, `python3 ../Dashboards/dashboard_server.py` in another terminal → `http://192.168.149.1:5000/`.

## Safety

With `--real-motion`: keep about 2.5 m clear in front and behind (DE_ESCALATE and SCAN back up), keep a hand near the power switch, Ctrl+C stops the wheels. All commands pass through `../Robot/safety.py` (speed limits, never closer than 0.8 m, stops if the person is lost or an obstacle is within 0.4 m).

## Troubleshooting

- **`person not visible`:** the face isn't found. Check framing with `--snapshot`, avoid bright lights behind you, try `--face-conf 0.3`.
- **Gestures not recognized:** stand 1.5–2 m away, hold the hand out to the side of your body (not in front of your face or dark clothing), at chest or shoulder height.
