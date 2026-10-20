# Photo Sunset Check

**English** · [Bahasa Indonesia](README.id.md)

Checks whether the time shown on a photo is plausible for its date and place, and shows how a time-zone assumption can flip the conclusion.

- Repository: https://github.com/hanifalghiffari979-max/photo-sunset-check
- Live demo: https://hanifalghiffari979-max.github.io/photo-sunset-check/gui/ (available once GitHub Pages is enabled)

## Why this exists

Imagine a photo stamped 18:38 in a city on 12 October. Was it still light, twilight, or dark?

It depends on which "18:38" you mean. Some countries change their daylight-saving rules over the years. Mexico moved its clocks forward in summer until 2022, then abolished the practice. So 18:38 in 2013 and 18:38 in 2026 at the same place are two different moments for the sun, one hour apart.

The tool computes the sun's position and compares it with the photo time under several clock assumptions. The answer is not "true" or "false" but: **under which assumption is this photo plausible?**

| Year | Sunset (wall clock) | Photo time 18:38 |
|---|---|---|
| 2013 | 19:26 | Still light (sun 10.4° above the horizon) |
| 2026 | 18:26 | Twilight (sun 3.4° below the horizon) |

Same place, same date, same camera time. Only the year, and therefore the time-zone rules, differs.

## Features

- Three statuses instead of a yes/no verdict: before sunset, twilight, dark.
- Four camera-clock scenarios: local time under that year's rules, fixed UTC−6, fixed UTC−5, camera set to UTC.
- Web interface that reads EXIF from a photo (time, offset, GPS, brightness). Photos stay on your device.
- Sun-elevation chart with the camera time marked.
- Automated tests and a recorded sample output.

## Quick start

```bash
git clone https://github.com/hanifalghiffari979-max/photo-sunset-check.git
cd photo-sunset-check
pip install -r requirements.txt
python -m src.sunset_check
python -m pytest
```

Custom input:

```bash
python -m src.sunset_check --lat 19.87 --lon -101.92 --date 2013-10-12 --time 18:38
```

Web interface: open `gui/index.html` in a browser.

## Repository layout

| Path | Purpose |
|---|---|
| `src/sunset_check.py` | Python command-line tool |
| `gui/index.html` | Web interface with EXIF reading |
| `tests/` | Automated tests (2013 vs 2026 case) |
| `examples/original_v0_1.py` | Original v0.1 script, kept as a baseline |
| `REPORT.md`, `docs/REPORT.pdf` | Report (English) |
| `LAPORAN.md`, `docs/LAPORAN.pdf` | Report (Indonesian) |
| `docs/sample_output.txt` | Real output from one run |

## What it does NOT do

- It does not prove a photo is real or fake.
- It cannot know whether the camera clock was ever set correctly.
- The default coordinates are an assumption and must be verified before drawing conclusions.
- EXIF brightness (EV100) is a rough clue, misleading for indoor or flash photos.
- EXIF is easy to edit and often stripped by messaging apps.
- It does not yet analyse shadow direction and length, which is stronger evidence than the clock.
- The web interface has not yet been tested with a broad set of real photos.

## Technology

Python 3.12, `astral` 3.2, `zoneinfo`/`tzdata`, `pytest`. Web interface: plain JavaScript (NOAA solar-position algorithm) and `exifr`.

## Roadmap

- [ ] Verify the default coordinates from a checkable source
- [ ] Shadow direction and length prediction
- [ ] Uncertainty ranges instead of single numbers
- [ ] Match EV100 against expected sun elevation
- [ ] CSV batch input

## Author

Mohammad Hanif Alghifari · [@hanifalghiffari979-max](https://github.com/hanifalghiffari979-max)

## License

MIT, © 2026 Mohammad Hanif Alghifari. See `LICENSE`.
