# Gesture-Controlled Smart Car

A Raspberry Pi + Arduino robot car controlled by real-time hand gestures, with
YOLO-based road-sign detection feeding into the same control loop.

## How it works

- A laptop/PC captures webcam video and runs MediaPipe hand-gesture
  recognition (`manas/`) to translate hand poses into drive commands
  (forward / reverse / left / right).
- YOLO models (`models/`) trained on road-sign and lane images can also feed
  detected commands into the same control loop.
- Commands and video frames are exchanged over WebSockets
  (`ws_video_stream/`, `socket_server.py`, `socket_client.py`) between the
  controller machine and the Raspberry Pi on the car.
- The Pi (`pi/web_socket_motor_client.py`) receives commands over WebSocket
  and forwards them to an Arduino over serial, which drives the motors.

## Repo layout

| Path | Purpose |
|---|---|
| `manas/` | Hand-gesture recognition (MediaPipe) — see attribution below |
| `models/` | Trained YOLO weights for road-sign / lane detection |
| `ws_video_stream/` | WebSocket video streaming + YOLO inference clients/server |
| `pi/` | Runs on the Raspberry Pi; forwards commands to the Arduino over serial |
| `socket_server.py` / `socket_client.py` | Gesture-command + video WebSocket server/client |
| `dl_models.py`, `paper_lane_det.py` | YOLO training/inference scripts |

## Hardware

- Raspberry Pi, mounted on the car, running `pi/web_socket_motor_client.py`
- Arduino, driving the motors, receiving serial commands from the Pi
- Laptop/PC with a webcam, running gesture recognition + YOLO inference and
  sending commands over WebSocket

## Setup

```bash
pip install -r requirements.txt
```

Set `RASPBERRY_PI_IP` in `pi/web_socket_motor_client.py` and the WebSocket
client scripts to match your network, then:

1. Run `pi/web_socket_motor_client.py` on the Raspberry Pi (with the Arduino
   connected over serial).
2. Run the desired controller script from `ws_video_stream/` or
   `socket_client.py` on the machine with the webcam.

## Note on live demos

This project drives physical hardware over a local-network WebSocket
connection, so it can't be hosted as a public live demo the way a web app
can — see the project write-up for a demo video instead.

## Attribution

`manas/hand-gesture-recognition-mediapipe-main/` is an English-translated
fork (by [kinivi](https://github.com/kinivi/hand-gesture-recognition-mediapipe))
of [Kazuhito00/hand-gesture-recognition-using-mediapipe](https://github.com/Kazuhito00/hand-gesture-recognition-using-mediapipe),
licensed under Apache License 2.0 — see the `LICENSE` file in that directory.
