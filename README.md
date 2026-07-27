# 🕷️ thwip

Become Spider-Man in your browser. Point a webcam at yourself, make the web-shooter
gesture — index and pinky out, middle and ring curled — and fire webs into your own room.

Everything is one self-contained `index.html`: no build step, no dependencies to install,
no start button. Hand tracking runs on-device via MediaPipe; the webs are a real 3D
physics simulation rendered to canvas.

## Run it

```sh
python3 -m http.server 8000
```

Then open <http://localhost:8000> and allow camera access.

## Keys

| key | what |
| --- | --- |
| `H` | toggle the HUD |
| `D` | toggle depth mode (lazy-loads a monocular depth model) |
