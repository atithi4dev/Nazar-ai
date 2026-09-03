<div align="center">

# Nazar AI

### City-Wide AI Engine for Multi-Camera ANPR Trajectory Tracking & Urban Traffic Analytics

**Turning disconnected city cameras into real-time traffic intelligence.**
</div>

---

## Table of Contents

1. [What This Project Is About](#-what-this-project-is-about)
2. [The Problem We Are Solving](#-the-problem-we-are-solving)
3. [Our Solution](#-our-solution)
4. [Why This Is Different](#-why-this-is-different)
5. [How It Works - System Architecture](#-how-it-works---system-architecture)
6. [AI Decision Pipeline](#-ai-decision-pipeline)
---

## What This Project Is About

Every city already has cameras on its roads - **hundreds of them**. But each camera works in isolation. Someone has to watch many screens to find a vehicle, and no one can see a single vehicle's full journey across the city.

**Nazar** connects all those cameras into one *searchable, map-aware, real-time traffic intelligence platform*. It:

- **Reads number plates** automatically from existing CCTV / ANPR camera feeds.
- **Records when and where** each plate was seen (timestamp, camera, lane, direction).
- **Joins sightings across space and time** so an authorized official can see a vehicle's journey across town on a map.
- **Reveals city traffic patterns** - congestion, density, speed, and busy routes.
- **Alerts officials** when a watchlisted or suspicious vehicle appears.

> **Everyday example:** A family reports a stolen car. Instead of an officer scanning 10 separate camera screens, they search the plate once and instantly see the car drive from **Market Road -> Ring Road -> Old Fort Street**. Meanwhile, a traffic officer watches a red-hot area grow on the map and clears a developing bottleneck before it becomes a city-wide jam.

---

## The Problem We Are Solving

| # | Problem (the day-to-day challenge) |
|---|---|
| 1 | Cities already own thousands of cameras, but feeds are fragmented **silos** - each watched separately by an operator. |
| 2 | Answering **"where has this vehicle been?"** is slow, manual, and depends on luck, memory, and dozens of screens. |
| 3 | Planners know a road is *somewhere* congested, but cannot see **where it starts or how far it spreads**. |
| 4 | Watchlisted, stolen, or suspicious vehicles slip through because no one can monitor every screen in real time. |
| 5 | Dirty, bent, blurred, or low-light plates make machine reading **unreliable**. |
| 6 | Cloned / fake plates and lapsed-registration vehicles go undetected without registry cross-checking. |
| 7 | A single camera's data is near-useless for journey tracking - value only appears when **hundreds are joined together**. |

The core pain: **cities already have the cameras; nobody actually uses them together.**

---

## Our Solution

A centralized, GIS-enabled platform that connects geographically distributed CCTV and ANPR cameras into a **single intelligence layer**, delivering two value streams from one event feed:

| Value stream | What it delivers |
|---|---|
| **Plate-level investigation** | Search one plate and see a verified, time-ordered journey across camera locations, with image evidence and audit trail. |
| **Aggregate traffic analytics** | Anonymized, privacy-safe views of density, speed, route demand, origin-destination patterns, and congestion. |

### Capabilities

- **Confidence-led plate reading** - OCR is validated with regional plate-format rules, multi-frame voting, image-quality scoring, and camera context before it becomes a *trusted* event.
- **Cross-camera trajectory reconstruction** - routes are inferred along permitted roads (road-graph-aware), not naive straight lines between cameras.
- **Real-time alerts** - watchlisted vehicles and "impossible" route patterns trigger audited operator alerts with proof images.
- **Road-network-aware routing** - journeys follow real road topology so results are believable, not guessed.
- **Phased deployment** - starts on existing cameras; inference can move to edge devices as camera count grows.

---

## Why This Is Different

- **Links sightings across cameras and time**, rather than treating each camera as an isolated feed.
- **Combines operational vehicle investigation and city-scale mobility analytics** in a single system.
- **Trusts before it records** - confidence-aware OCR, plate-format validation, and duplicate suppression reduce unreliable reads.
- **Separates operational access from aggregate planning analytics** through role-based controls.
- **Two value layers, one event stream** - the same careful data serves both a single-vehicle hunt and whole-city planning.
- **Reuses existing camera infrastructure** - no city-wide hardware replacement required.

---

## How It Works - System Architecture

The platform is a set of **loosely coupled services** connected by an event stream. Cameras produce raw frames; AI services turn frames into **trusted sighting events**; backend services join those events across space and time; a GIS dashboard and alerts expose the results to operators.

```
CCTV / RTSP cameras  +  uploaded videos  +  existing ANPR feeds
        |
        v
  INGESTION LAYER   ->  camera adaptors, FFmpeg, health monitor, frame sampling (2-5 fps)
        |
        v
   AI PIPELINE      ->  vehicle detect (YOLO) -> plate crop -> enhance -> OCR (PaddleOCR)
        |
        v           (format validation + confidence score + duplicate suppression)
   TRUSTED SIGHTINGS -> event store, quality-gated
        |
        +----------------+----------------+----------------+
        v                v                v                v
   TRAJECTORY        ANALYTICS         ALERTS          (evidence files)
   route joining     density/speed/OD  watchlist/anomalies
        \________________|________________|________________/
                             |
                             v
              API + DASHBOARD  ->  FastAPI REST/WebSocket, React + MapLibre
```

### End-to-end flow

1. **Ingest** raw frames or existing ANPR events from every camera.
2. **Detect & validate** plates; save only confidence-qualified sightings.
3. **Join** sightings spatially and temporally to reconstruct vehicle movement.
4. **Aggregate** events to calculate flow, density, speed, and congestion.
5. **Trigger alerts** only for authorized rules and preserve an operator audit trail.

*(A detailed component diagram lives in [dont_touch.md](./dont_touch.md).)*

---

## AI Decision Pipeline

```
Camera frame
   |
   v
Is a vehicle detected? -- No --> keep processing next frame
   | Yes
   v
Locate & crop the plate
   |
   v
Enhance image (blur, glare, angle, low light)
   |
   v
OCR reads the plate text  +  re-ID embedding
   |
   v
Valid format + sufficient confidence?
   | No   --> review queue / reject (never silently trusted)
   | Yes
   v
Create a trusted sighting event
   |
   v
Map, alerts, and analytics
```

> **Key technical truth:** OCR confidence is **not** certainty. The system preserves low-confidence candidates for manual review; a low-confidence read is **never** used as the sole grounds for any consequential action.
