# 🕷️ thwip

Become Spider-Man in your browser. Point a webcam at yourself, make the web-shooter
gesture — index and pinky out, middle and ring curled — and fire webs into your own room.

Everything is one self-contained `index.html`: no build step, nothing to install, no
start button. Hand tracking runs on-device via MediaPipe; the webs are a real 3D
physics simulation in metres, rendered to canvas.

## Run it

```sh
python3 -m http.server 8000
```

Then open <http://localhost:8000> and allow camera access. That's the whole setup.

## What's actually being simulated

The webs are not drawn on the glass — they live in a metric 3D camera frame and are
projected through a pinhole camera, which is what makes them behave.

- **Your hand is lifted into 3D.** MediaPipe's world landmarks give the hand's true
  size in metres, so how big it looks fixes how far away it is. The wrist depth is
  solved under full perspective across all 21 landmarks rather than assuming the hand
  sits at a single depth — worth ~11% when the palm is aimed down the lens, which is
  the pose the whole thing is built around.
- **Point at the camera and the web fires into the room.** The aim is rotated 180°
  about the vertical axis, so the world you can see behind you is the world you shoot
  into. It recedes toward the vanishing point, thins with distance, and falls.
- **Real ballistics.** 30 m/s muzzle speed, 9.81 m/s² gravity, quadratic air drag with
  a payout term for the line being dragged behind. Fired flat, a web drops about 2 cm
  over 2 m — matching `g·d²/2v₀²` to within a millimetre in test.
- **Real rope.** 36-particle Verlet with distance constraints at a 1/960 s substep. A
  line with 8% slack settles to a catenary sag of 0.183 × span (theory: 0.178), and a
  3 m line swings with a 3.03 s period (theory: ~3.1 s), keeping 72% of its amplitude
  after two swings. Pull past 25% strain and the line parts, because dragline silk
  does — it never stretches like rubber.
- **Anchors stay on the wall.** Global camera motion is estimated by block matching
  with your own hands rejected as outliers, then every world-fixed point is
  re-projected so it keeps landing on the same real-world pixel. Nudge the laptop and
  the webs stay put.
- **Webs catch on real structure.** A Sobel edge map picks out doorframes, shelf edges
  and monitor bezels; everything else collides with a modelled room box calibrated off
  how far away your hand is.

The line itself is rendered as a tube, not a stroke: a perspective-scaled silhouette,
a lit side and a shaded side, and three filaments braided around it on a
rotation-minimising frame, so the twist passes *behind* the cable on the far side of
every turn.

## Keys

| key | what |
| --- | --- |
| `H` | toggle the HUD |
| `D` | toggle depth mode — lazy-loads a monocular depth model and replaces the room box with your actual room |

Nothing else. Discovering the gesture is the point.
