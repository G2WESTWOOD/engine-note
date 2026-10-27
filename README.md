# Engine Note

Makes a Tesla (or any car) sound like a combustion car, through the car's speakers, driven by your real speed.
Every engine is a real recording from [Engine Simulator](https://github.com/ange-yaghi/engine-sim), captured while
driving the car through its gears: full-throttle pulls for the on-throttle sound, coasting down in gear for the
off-throttle sound and bouncing off the rev limiter in gear, plus its own start-up and idle. Nothing is synthesised
except the optional turbo, blow-off valve and pops.

Live: https://g2westwood.github.io/engine-note/

## Use it

**Tesla browser.** Open the site in the car, tap Start engine. Speed comes from the car's GPS (the browser can't read
the pedals), so revs follow the car about half a second behind. Turn Immersive Sound on (Standard or High) and try
Sound field Surround vs Stereo.

**Phone (recommended for response).** Open in Chrome or Safari, tap Start engine, allow location and motion, and mount
the phone firmly. Play over Bluetooth to the car. After 3–4 normal speed-ups the status shows "Motion live" and the
sound follows the pedal instantly.

**Computer.** Simulator mode: hold Gas (↑) to drive through the gears, Brake (↓), shift with ◀ ▶ (← →), R to rev,
Space to start/stop. Letting go of Gas works like Tesla one-pedal driving: regen slows the car at up to about 0.2 g
and brings it to a stop.

The app detects which of the three it is running on. You can override it under Speed data, or with
`?device=tesla|phone|desktop` in the URL.

## Settings

| Setting | What it does |
|---|---|
| Engine | 10 recorded engines. Each downloads only when picked. |
| Sound files | **WAV** (lossless, default, about 8–18 MB per engine) or **OGG** (data saver, about 1–3 MB per engine). Same recordings. |
| Your Tesla | Pick your model. It sets the pedal scale (full load = that car's full acceleration), tyre size and matched gearing. |
| Gearing | **Matched to car** (default): the final drive is set so top gear reaches your Tesla's top speed (250 km/h on a Model Y Performance) just under the redline, and 1st lasts at least about 2 s at your Tesla's full acceleration. Where a gearbox spreads too wide for both, its ratios are pulled closer together on the same spacing pattern. **Original car**: the real car's ratios (long gears; a Tesla runs through 1st much faster than the original car). |
| Shifting | Comfort, Sport, Track (automatic shift points, kickdown, rev-matched downshifts) or Manual (you shift with ◀ ▶). In the automatic modes ◀ ▶ work as paddles for a few seconds. |
| Gearbox | Car default, Manual (clutch in/out, heel-and-toe), DCT, Sequential or Automatic: changes shift time and feel. |
| Turbo | On/off per engine. Boost target is set with − / + next to the live boost readout (tap the readout for psi). Two-state model: the turbo shaft has inertia and spools with exhaust flow (rpm × load); boost follows the shaft, capped by the wastegate, and drops to vacuum on a lift while the shaft winds down. The whistle's pitch and volume follow the shaft, so it gets louder as the turbo spools up and fades as it winds down. |
| Blow-off valve, Pops | Optional extras. The valve fires when the throttle drops 60 % within 100 ms with boost (0.5 s lockout), with ±3 % pitch variation. It uses recorded samples if `samples/index.json` lists them under `"bov": {"pssh": [...], "flutter": [...]}`, otherwise synthetic variants. Off-throttle pops are off by default. |
| Pedal feel | How hard you have to accelerate for full load (car and phone modes). |
| Sound field, Volume, Sub bass | Surround sends the exhaust to the rear speakers on matrix upmixers. Sub bass is the recording's own low end, centred for the subwoofer. |

Your own recordings: "Load your own WAVs…" takes files named `<name>_<rpm>_<on|off>.wav` (optional `<name>_idle`,
`<name>_start`, `<name>_limiter`).

## How it works

- **Drivetrain.** Each engine has real-car gearing. The gearbox picks a gear from speed and load, the clutch slips on
  pull-away and disengages at a stop, upshifts cut torque and drop the revs, downshifts blip to match, and the limiter
  is a fuel-cut bounce. In Simulator mode the same model also produces the acceleration, and lifting off regen-brakes
  like a Tesla, so the gearbox works its way down through the gears as you slow. In the car, slowing at regen rates
  counts as a lift, not as braking.
- **Sound.** For the current rpm the two nearest recorded points are crossfaded and pitch-matched, and the on- and
  off-throttle clips are blended by load. Clips are seamless loops of a whole number of engine cycles. They come from
  sweeps (a top-gear pull, a coast-down in gear), so each is first resampled along the engine's rotation so the
  engine turns at exactly that point's rpm, and taken where the revs change slowest.
- **Car GPS.** An alpha-beta filter smooths speed and acceleration and predicts between the 1 Hz fixes.
- **Phone.** The accelerometer is projected onto a forward axis learned from GPS speed changes, so mounting angle
  doesn't matter.
- **Background.** Physics runs on a timer rather than the display refresh, so it keeps going when the screen dims.
- **Output.** 30 Hz high-pass (keeps sub-audible rumble off the subwoofer), gentle compressor, limiter, then a soft
  safety clip. The mix runs hot but clean, so the car or phone needs less volume (and adds less hiss).
- **Testing tip.** Set the car's EQ flat while trying engines.

## Limits

- Browser audio is stereo; individual car speakers can't be addressed.
- Throttle is inferred from acceleration: steady speed uphill reads as light load.
- The Tesla browser exposes no pedal or CAN data to web pages, only location.

## Credits

- Engine recordings: captured from [Engine Simulator](https://github.com/ange-yaghi/engine-sim) by AngeTheGreat.
- Blow-off valve recordings (`samples/bov/pssh_*.wav`): excerpts from
  ["Loud HKS BOV sound on peugeot 406 turbo"](https://commons.wikimedia.org/wiki/File:Peugeot_406_Turbo_(turbocharged,_HKS_blow-off_valve)_-_engine_and_exhaust_sound_(YouTube_kx0Tvu4_K04).opus)
  by nick7100 (YouTube), via Wikimedia Commons, licensed
  [CC BY 3.0](https://creativecommons.org/licenses/by/3.0/). Changes: four vents cut out, high-pass filtered to remove
  the engine underneath, faded and level-matched. The "Flutter" valve sound is synthetic.

## Files

- `index.html`: the whole app.
- `samples/index.json`: engine list and metadata.
- `samples/<engine>/{ogg,wav}/`: the clips.
