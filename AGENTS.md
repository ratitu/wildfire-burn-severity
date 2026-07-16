# AGENTS.md

## Overview

Single-file Streamlit app (`app.py`, ~1800 lines) for wildfire burn severity analysis using Google Earth Engine, Sentinel-2 imagery, and climate data (CHIRPS precipitation, ERA5 temperature). All logic lives in `app.py` — no separate modules.

## Run

```bash
streamlit run app.py
```

Python 3.13. Install with conda/mamba (see README for env setup). `requirements.txt` is UTF-16 encoded — use `mamba install --file requirements.txt` rather than pip.

## Secrets Required

The app reads `st.secrets` at runtime:
- `json_key` — Google Earth Engine service account JSON (for cloud deploy). Falls back to local `ee.Initialize()` if absent.
- `mapbox_token` — Mapbox Dark basemap token.

Without these, the app will fail at startup.

## Branching

Git-flow model. PRs go against `streamlit-dev`. Main branch is `streamlit-main`.

## Gotchas

- **No tests, no linter, no typecheck, no CI.** There are no automated checks. Verify changes manually by running the app.
- **Global state in `app.py`:** `last_uploaded_centroid` is a module-level global mutated during file uploads. Be aware when modifying `upload_files_proc()`.
- **Earth Engine calls are synchronous and slow.** `.getInfo()` blocks the Streamlit server. Large AOIs or long date ranges will be sluggish.
- **dNBR_classified is overwritten:** The variable `dNBR_classified` is first used for the classified raster (7 classes), then later reassigned to a boolean threshold (`dNBR_classified.gte(4)`) for burn scar vectorization. Don't confuse the two uses.
- **Streamlit session state** is used to pass `masked_dNBR_classified` and `geometry_aoi` from the map section to the report section.
- **src/ contains only images** (README assets, GIFs). No code there.
