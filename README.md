# Firewatch — South Africa Wildfire Early-Warning Console (Flet)

A cross-platform dashboard for a simulated wildfire sensor network, written
entirely in Python. One codebase runs on Windows, macOS, Linux, Android, iOS
and the web.

## What's simulated

- 83 sensor nodes across all nine provinces of South Africa (cities, towns, mountain and
  bushveld areas such as the Cape winelands, Drakensberg, Kruger and the Kgalagadi), with
  temperature, humidity and fuel-moisture readings that drift over time
- A map of South Africa with the Lesotho and eSwatini enclaves and province names
- A risk score per zone from wind speed, fuel moisture, temperature and slope
- Random ignitions weighted toward high-risk zones, with a spread ellipse oriented by
  wind direction and a dashed evacuation-advisory ring on extreme fires
- Containment over time, a live event feed, and wind / fuel-moisture trends
- A public alert view: the same data as a plain-language advisory

## Files

| File | Role |
|------|------|
| `geo.py` | Geography: the South Africa outline, province labels and the latitude/longitude to map projection. |
| `simulation.py` | The engine. Plain Python, no UI. `tick()` advances it, `snapshot()` returns the state. |
| `main.py` | The Flet interface. Builds every screen in Python from Flet controls and redraws from `snapshot()` via `page.update()`. |
| `requirements.txt` | `flet` |

The UI only ever reads `snapshot()`, so real sensor / weather / satellite data can replace
`Simulation._step_environment` without touching `main.py`.

## Run it
```bash
pip install -r requirements.txt
python main.py
```

The layout is responsive: three columns on wide windows, a single scrolling column on
tablets and phones.

## Package it

Install Flet, then build for your target platform:

- **Windows / macOS / Linux:** `flet build windows` (or `macos`, `linux`)
- **Android:** `flet build apk` (or `flet build aab` for the Play Store)
- **iOS:** `flet build ipa` (requires a Mac with Xcode)
- **Web:** `flet build web`

Run these from the project root. Build settings such as the app name, version,
and dependencies go in `pyproject.toml` (or via flags like `--project` and
`--product`).

## Adding or moving places

Places live in `ZONE_DEFS` at the top of `simulation.py`:

```python
# id, name, province, lat, lon, elev, slope
("z84", "Hluhluwe", "KwaZulu-Natal", -28.02, 32.05, 0.10, 0.20),
```

Use real latitude/longitude (South Africa is roughly lat -22 to -35, lon 16 to 33).
`elev` and `slope` are 0 to 1; slope feeds the risk score. To print a place's name on the map,
add it to `LABELLED` in `geo.py`. Ignition odds scale automatically with the number of nodes.

## Notes

- The outline in `geo.py` is simplified and stylised, not a survey-grade border.
- The simulation runs in-process on Flet's clock (every 2.2 s). There is no server, so the app
  works offline and on mobile.
- To go beyond a prototype, swap the toy risk formula for a validated fire-danger index (e.g., NFDRS)
  and the spread model for a physics-based one (e.g., Rothermel).
