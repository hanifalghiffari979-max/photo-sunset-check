# Project Report: Photo Time vs Sunset Check

Report date: 19 October 2026 · Version 0.2

## 1. Summary

This project tests one idea: the conclusion to "is the time on this photo plausible?" can flip purely because of an assumption about time-zone rules. In the test case (12 October, same place, camera time 18:38) the conclusion changes between 2013 and 2026 because Mexico abolished daylight-saving time in 2022. The project is a demonstration and analysis aid, **not a detector of photo authenticity**.

## 2. Background and goals

The time on a photo is often used as a clue. But the wall-clock time in a place is not a constant: time-zone rules change. If the wrong year's rules are applied, a careful-looking calculation still yields a wrong conclusion.

Goals:
1. Compute the sun's position (sunset, end of twilight, elevation) for a given date, place and time.
2. Compare results across several assumptions about the camera clock.
3. Show the assumptions openly instead of hiding them behind a single verdict.

## 3. Method

- **Solar position:** the `astral` 3.2 library (Python) and a JavaScript re-implementation of the NOAA algorithm (web interface).
- **Time zone:** an IANA zone name (`America/Mexico_City`) via `zoneinfo` with `tzdata`, so historical rules come from the database rather than a hand-written offset.
- **Three statuses** based on the sun's elevation at the photo time:
  - Before sunset: elevation ≥ −0.833°
  - Twilight: between −0.833° and −6° (end of civil twilight)
  - Dark: below −6°
- **Four camera-clock scenarios:** local time under that year's rules; assumed fixed UTC−6; assumed fixed UTC−5; camera set to UTC. The web interface adds a fifth if EXIF records an offset.
- **EXIF (web interface):** time, offset, GPS and EV100 are read with `exifr`, processed in the browser and not uploaded.
- **Test parameters:** latitude 19.87; longitude −101.92; altitude 0 m; date 12 October; time 18:38. The coordinates come from the original code and have not been verified.

## 4. Results

Run with Python 3.12.3 and astral 3.2.

| Year | Zone offset | Sunset | Twilight ends | Elevation at 18:38 local | Status |
|---|---|---|---|---|---|
| 2013 | UTC−5 | 19:26:05 | 19:48:38 | 10.4° | Before sunset |
| 2026 | UTC−6 | 18:26:12 | 18:48:45 | −3.4° | Twilight |

Other scenarios in 2013: assumed fixed UTC−6 gives −3.5° (twilight); assumed fixed UTC−5 gives 10.4° (before sunset). "Camera set to UTC" gives 62.5° because 18:38 UTC is around midday in Mexico. That scenario is implausible for a photo that looks like evening and is shown only so the assumption is visible.

**Cross-check.** The web interface (JavaScript) gives sunset 19:26:16 (2013) and 18:26:23 (2026), about 11 seconds from astral. Elevations differ by about 0.1°. The one-hour gap between years is identical in both implementations.

**Automated tests.** 4 `pytest` tests pass: offset 2013 (UTC−5) vs 2026 (UTC−6), wall-clock sunset about 60 minutes apart, verdict flip for local time, and the three-status boundaries.

## 5. Analysis

Under the local-time rules of each year, a photo stamped 18:38 is before sunset in 2013 and after it in 2026. The original two-state verdict ("plausible / impossible") was too harsh: in 2026 the photo is only 12 minutes after sunset, still in twilight bright enough to record. Version 0.2 therefore uses three statuses.

Main lesson: before drawing a conclusion from one number, show the assumptions behind it and re-run with alternatives.

## 6. Limitations

- The coordinates and site altitude are unverified.
- It does not prove authenticity. A camera clock may be set wrongly, and EXIF can be edited or stripped.
- EV100 is a rough clue and is not automatically compared with the expected sun elevation.
- No shadow analysis, moon phase or map matching yet.
- The web interface has not been tested with real photos.
- Results depend on the `tzdata` version and, in the web interface, on the browser's time-zone data.

## 7. Notes on improvements during development

Version v0.1 had several flaws that were fixed: a wrong code comment ("minutes since noon" when it meant midnight), a two-state verdict, minute-level comparison that dropped seconds, and site altitude ignored. The early figures in discussion (19:26 and 18:26) were at first only predictions; they were confirmed only after the code was actually run (section 4).

## 8. Next steps

1. Verify the site coordinates from a checkable source.
2. Predict shadow direction and length with `astral` (azimuth and elevation) and compare with the photo.
3. Add an uncertainty range (varying location and camera time) instead of a single number.
4. Match EV100 against the expected sun elevation.
5. CSV input for many cases and an elevation chart in the Python version.

## 9. Sources to check independently

- Python `zoneinfo` and `tzdata` documentation (docs.python.org)
- `astral` documentation (`sun` module)
- IANA time-zone database, entry `America/Mexico_City` (DST rules until 2022)
- NOAA Solar Calculator (basis of the solar-position algorithm)
