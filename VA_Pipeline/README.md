# MentorPi: Emotion (VA) Pipeline

Runs the team's **valence-arousal (VA) pipeline** on the MentorPi. It reads the person's face (camera) and voice (microphone), estimates how they feel (valence = pleasant/unpleasant, arousal = calm/excited), picks one of the five intents, and drives the wheels through the shared robot code in `../Robot/`.

The pipeline itself (JointCAM model, `run_offline.py`, `online/`, `test_emotions.py`) lives in the team repo **`chaicenter-spot-basics/va-driven-behaviors`**; this folder contains the MentorPi side.

```
camera + mic ──▶ VA pipeline (team repo) ──▶ intent ──▶ Robot/mentorpi_adapter ──▶ Robot/safety ──▶ wheels
face tracker + LiDAR (Robot/) ──▶ person distance / direction ──┘
```

## Files

| File | What it does |
|---|---|
| `continuous_runner_http.py` | **Cycle runner.** Records 6 s of video + audio, runs the offline pipeline on it, acts on each window's intent, repeats. |
| `run_online_mentorpi.py` | **Online (real-time) runner.** Uses the team's ring-buffer streaming session with the MentorPi camera, microphone and face/LiDAR tracking. |
| `mentorpi_streaming_session.py` | MentorPi version of the team's streaming session (camera over HTTP, WonderEcho Pro mic). Used by the online runner. |
| `mentorpi_live_capture.py` | Records a matching video + audio pair for offline tests. |
| `offline_full_loop.py` | Runs a saved video through the pipeline and then the adapter (mock, no motion). |
| `team_repo_patches/` | Fixes for files in the team repo (frame seeking, per-window audio, `max_workers`). See its README. |
| `docs/VA_Pipeline_Issues_Reference.md` | Known issues with the VA pipeline. |

## Setup (on the Pi, once)

1. This repo at `~/MentorPi` and the team repo at `~/chaicenter-spot-basics`. The scripts look for `va-driven-behaviors/` next to this repo first, then at `~/chaicenter-spot-basics/va-driven-behaviors`; set `MENTORPI_VA_DIR` to use another location.
2. Python environment (Python 3.11, MediaPipe 0.10.9):
   ```
   python3 -m venv ~/va_pipeline_venv
   source ~/va_pipeline_venv/bin/activate
   pip install -r ~/chaicenter-spot-basics/va-driven-behaviors/requirements.txt
   pip install opencv-python mediapipe==0.10.9 websocket-client sounddevice scipy numpy
   ```
3. JointCAM checkpoint: put `jointcam_finetuned_v4.pt` in `va-driven-behaviors/models/` (too large for Git; get it from the team).
4. Optional: apply the patches in `team_repo_patches/` to the team repo.

## Running

Everything starts in **mock mode** (prints the wheel commands; the robot doesn't move). Add `--real-motion` only after the mock output looks right.

```
source ~/va_pipeline_venv/bin/activate
cd ~/MentorPi/VA_Pipeline
```

**Cycle runner**
```
python3 continuous_runner_http.py                  # mock
python3 continuous_runner_http.py --real-motion    # wheels move
python3 continuous_runner_http.py --max-cycles 5   # stop after 5 cycles
```

**Online (real-time) runner**
```
python3 run_online_mentorpi.py --duration 60 --window 4.5 --sparse --max-workers 1
python3 run_online_mentorpi.py --duration 60 --window 4.5 --sparse --max-workers 1 --real-motion
```
Recommended settings (from testing on the Pi):
- `--sparse`: full analysis is far slower than real time on the Pi's CPU.
- `--max-workers 1`: two workers compete for the CPU and both slow down.
- `--window 4.5` with tracking on: analysis takes about 4 s per window, so 3 s windows build a backlog.
- `--min-confidence 0.1` (testing only): in `--sparse` mode confidence stays around 0.16, below the default 0.55.
- Other options: `--no-tracking`, `--no-audio`, `--debug`.

**Record a test clip / run it offline**
```
python3 mentorpi_live_capture.py --duration 20
python3 offline_full_loop.py <video.mp4> --pacing 2.0
```
The microphone records 48 kHz stereo; the capture script converts to the pipeline's 16 kHz mono.

## Extra movements (cycle runner)

| Movement | When |
|---|---|
| GREET (ENGAGE: two forward-back pushes) | After 2 ENGAGE results in a row, once per streak, 30 s cooldown |
| PULSE (NEUTRAL: small back-forward) | After 3 NEUTRAL results in a row |
| SCAN (CAUTION: backs up turning left/right) | After 2 CAUTION results in a row |

## Troubleshooting

- **`Worker overflow` warnings (online runner):** analysis is slower than the window. Use `--sparse --max-workers 1`, a longer `--window`, or `--no-tracking`.
- **Robot never acts:** confidence below `--min-confidence` (see above).
- **No internet on the Pi:** expected on the MentorPi hotspot. Download on a laptop and `scp` over.

## Known issue

The VA model loads only 77 of 85 parameters from its checkpoint (the model code in `test_emotions.py` no longer matches the trained architecture), so its VA output is unreliable. See `docs/VA_Pipeline_Issues_Reference.md`. This is the main reason the gesture pipeline (`../Gesture/`) exists.
