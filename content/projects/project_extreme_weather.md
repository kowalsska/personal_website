---
title: "Extreme Weather Tracker"
date: 2025-03-01T00:00:00+00:00
draft: false
---

<img src="../../img/extreme_weather_tracker.png">

<a href="https://extreme-weather-tracker-3zod.vercel.app/" target="_blank">🌐 extreme-weather-tracker-3zod.vercel.app</a>

The 2023-24 El Niño was the strongest in decades. I kept reading about individual events: floods in Libya, wildfires in Canada, cyclones in the Pacific. But I never had a single view of what was happening globally at any given moment. News coverage is reactive and fragmented. Climate databases are either paywalled or built for researchers. I just wanted a map I could open and look at.

The Extreme Weather Tracker is a live world map of extreme weather events: storms, floods, wildfires, drought, and extreme heat, updated every 12 hours. The goal was to build something actually useful as a visual reference while keeping the entire stack on free tiers, no credit card required.

## Data sources

Weather data comes from GDACS (the Global Disaster Alert and Coordination System), which publishes near-real-time alerts for tropical cyclones, floods, wildfires, and droughts. Wildfire data is augmented with NASA FIRMS, which provides active fire detections from MODIS and VIIRS satellites, aggregated into one event per country per day weighted by the fire's geographic centre.

Extreme heat is the hardest hazard to get as an event feed because no data provider ships one ready-made. I derived it myself: Open-Meteo provides free global temperature forecasts over a city grid, and I flag days where temperatures cross an absolute threshold. The better approach (on the roadmap) is percentile-based anomaly detection, comparing against climatological baselines rather than a fixed number. That makes the heatwave signal meaningful across climates: 38°C hits differently in Lisbon than in Phoenix.

All five hazard types are normalised into a single `intensity_norm` score from 0 to 1, so the map can size and colour every dot on a consistent scale regardless of whether it's a Category 4 cyclone or a wildfire.

## Architecture

The project runs four independent layers, each on its own free tier:

```
Next.js + MapLibre + deck.gl   →  Vercel (Hobby)         frontend / map
FastAPI (Python)               →  Vercel (Python funcs)   read-only API
Postgres + PostGIS             →  Neon (free tier)        normalised event store
Python ETL                     →  GitHub Actions (cron)   ingestion every 12h
```

The ETL pipeline runs on GitHub Actions twice a day with no always-on infrastructure. It fetches from GDACS and NASA FIRMS, normalises the data, and upserts into a PostGIS table. The API is a read-only FastAPI app that returns events as GeoJSON, filterable by hazard type, date range, bounding box, and minimum intensity. The frontend renders them on a WebGL-accelerated deck.gl layer on top of MapLibre.

PostGIS is already installed on Neon, which made geographic queries straightforward without any additional setup.

## What I learned

The hardest part wasn't the mapping or the ingestion. It was the data normalisation. Every source uses different severity schemes, different geographic representations, and different update cadences. GDACS uses an alert level (green/orange/red) plus a numeric alertscore. NASA FIRMS gives raw fire pixel counts. Open-Meteo gives temperature readings with no inherent severity. Converting all of these into a single comparable intensity score that makes visual sense on a map required more domain thinking than engineering.

The El Niño connection also turned out to be harder to show than I expected. El Niño is a slow background signal that shifts probabilities and patterns over months, while the map shows discrete events. Making that link visible rather than implied is the next design challenge.

**Stack:** Next.js · TypeScript · MapLibre GL · deck.gl · FastAPI · Python · PostgreSQL · PostGIS · Neon · GitHub Actions · GDACS · NASA FIRMS · Open-Meteo · Vercel
