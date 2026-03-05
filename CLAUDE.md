# Camera Studio Planner

An interactive HTML tool for planning 8-camera Taekwondo poomsae filming setups in studio spaces.

## Project Overview

Single-page HTML application (`camera-studio-planner.html`) that calculates and visualizes:
- Camera positions and angles for 8 synchronized cameras
- Studio space requirements and feasibility
- Lighting calculations (lux levels, evenness, stop adjustments)
- Wall coverage and GoPro/DJI camera configurations

`index.html` is a copy deployed for GitHub Pages. `camera-ui-mockup.html` is an iPhone 17 Pro mockup of the camera switching UI.

## Tech Stack

Pure HTML/CSS/JavaScript — no build tools, no dependencies. Open any `.html` file directly in a browser.

## Key Conventions

- All calculations use metric units
- Studio parameters are configurable via the UI
- Lux values are color-coded by feasibility
