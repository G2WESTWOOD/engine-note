# Engine Note

Makes a Tesla (or any car) sound like a combustion car, through the car's speakers, driven by your real speed.
Every engine is a real recording from [Engine Simulator](https://github.com/ange-yaghi/engine-sim), captured on a dyno at
fixed rpm on and off throttle, plus its own start-up, idle and rev-limiter. Nothing is synthesised except the optional
turbo, blow-off valve and pops.

Live: https://g2westwood.github.io/engine-note/

## Use it

**Tesla browser.** Open the site in the car, tap Start engine. Speed comes from the car's GPS (the browser can't read
the pedals), so revs follow the car about half a second behind. Turn Immersive Sound on (Standard or High) and try
Sound field Surround vs Stereo.

**Phone (recommended for response).** Open in Chrome or Safari, tap Start engine, allow location and motion, and mount
the phone firmly. Play over Bluetooth to the car. After 3–4 normal speed-ups the status shows "Motion live" and the
sound follows the pedal instantly.

**Computer.** Simulator mode: hold Gas (↑) to drive through the gears, Brake (↓), shift with ◀ ▶ (← →), R to rev,
Space to start/stop.

The app detects which of the three it is running on. You can override it under Speed data, or with
`?device=tesla|phone|desktop` in the URL.

## Settings

| Setting | What it does |
|---|---|
| Engine | 13 recorded engines. Each downloads only when picked. |
| Sound files | **OGG** (fast, about 1–3 MB per engine) or **WAV** (lossless, about 8–18 MB per engine). Same recordings. |
| Shifting | Comfort, Sport, Track (automatic shift points, kickdown, rev-matched downshifts) or Manual (you shift with ◀ ▶). In the automatic modes ◀ ▶ work as paddles for a few seconds. |
| Gearbox | Car default, Manual (clutch in/out, heel-and-toe), DCT, Sequential or Automatic: changes shift time and feel. |
| Turbo | On/off per engine. Boost target is set with − / + next to the live boost readout (tap the readout for psi). |
| Blow-off valve, Pops | Optional extras; off-throttle pops are off by default. |
| Pedal feel | How hard you have to accelerate for full load (car and phone modes). |
| Sound field, Volume, Sub bass | Surround sends the exhaust to the rear speakers on matrix upmixers. Sub bass is the recording's own low end, centred for the subwoofer. |

Your own recordings: "Load your own WAVs…" takes files named `<name>_<rpm>_<on|off>.wav` (optional `<name>_idle`,
`<name>_start`, `<name>_limiter`).

## How it works

- **Drivetrain.** Each engine has real-car gearing. The gearbox picks a gear from speed and load, the clutch slips on
  pull-away and disengages at a stop, upshifts cut torque and drop the revs, downshifts blip to match, and the limiter
  is a fuel-cut bounce. In Simulator mode the same model also produces the acceleration.
- **Sound.** For the current rpm the two nearest recorded points are crossfaded and pitch-matched, and the on- and
  off-throttle clips are blended by load. Clips are seamless loops cut at a whole number of engine cycles.
- **Car GPS.** An alpha-beta filter smooths speed and acceleration and predicts between the 1 Hz fixes.
- **Phone.** The accelerometer is projected onto a forward axis learned from GPS speed changes, so mounting angle
  doesn't matter.
- **Background.** Physics runs on a timer rather than the display refresh, so it keeps going when the screen dims.

## Limits

- Browser audio is stereo; individual car speakers can't be addressed.
- Throttle is inferred from acceleration: steady speed uphill reads as light load.
- The Tesla browser exposes no pedal or CAN data to web pages, only location.

## Files

- `index.html`: the whole app.
- `samples/index.json`: engine list and metadata.
- `samples/<engine>/{ogg,wav}/`: the clips.
