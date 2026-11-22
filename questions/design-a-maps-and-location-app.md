# Design a Maps and Location App

A maps client draws a map the user can pan and zoom, shows the user's position on it, and searches for places. The map of the world is far too large to hold on a device, so the design is about loading the right small pieces at the right time, caching them, and reading location without draining the battery.

## Requirements to clarify

- Is the scope viewing and search, or turn-by-turn navigation too? (Propose viewing, the user's position, and place search.)
- Must maps work offline for a chosen region?
- Does the design draw the map itself, or use a platform map SDK? (Most interviewers allow the SDK and then ask what happens inside it.)
- How accurate must the user's position be, and must it update in the background?

## API and data model

A map is cut into **tiles**, which are square pieces of the map at a fixed zoom level. Each tile is addressed by three numbers: zoom, x, and y. At zoom 0 one tile covers the world, and each further zoom level has four times as many tiles.

| Call | Purpose |
|---|---|
| `GET /tiles/{z}/{x}/{y}` | One map tile, served by a CDN |
| `GET /v1/places/search?q=&near=` | Places matching the text near a point |
| `GET /v1/places/{id}` | Details of one place |

Tiles come in two kinds. Raster tiles are images. Vector tiles carry shapes and labels that the device draws itself, which makes them smaller and lets the map rotate and zoom smoothly.

On the device, a tile cache maps the tile address to a file, with a size limit. A separate store holds offline regions, which are never evicted automatically.

## Client architecture

The map view asks a tile manager for the tiles that cover the visible area. The tile manager checks memory, then disk, then the network, in the same order as an [image pipeline](../concepts/image-loading-and-caching.md). A location provider wraps the platform location service and publishes positions, and a search repository handles place queries.

## The decisions that matter

**1. Which tiles to load.** The client computes the tiles covering the screen at the current zoom, usually between 12 and 20 of them, and requests the center first. While new tiles load, the tiles of the previous zoom level are scaled and shown, so the user never sees an empty grid. Requests for tiles that left the screen are cancelled.

**2. Two tile stores with different rules.** The browsing cache is bounded, for example 200 MB with least recently used eviction. An offline region is an explicit download: the app computes every tile inside the region for a range of zoom levels, shows the size before starting, and downloads over Wi-Fi as background work. Each added zoom level multiplies the tile count by about four, so the range of zoom levels must be limited.

**3. Location accuracy against battery.**

| Need | Setting |
|---|---|
| "Show roughly where I am" | Low accuracy from Wi-Fi and cell signals; little battery |
| A blue dot while the map is open | High accuracy, only while the screen is visible |
| Navigation with the screen off | A visible background location mode, the most expensive choice |

Each reading carries an accuracy radius, which the map draws as a circle, and a poor reading should not move the dot far.

**4. Search requests.** Typing sends a request only after the user pauses for about 300 milliseconds, which is called debouncing. Each new request cancels the previous one, so an old response cannot overwrite a newer one.

## Failure, offline, and shipping

- **No network:** cached and offline tiles are drawn; missing tiles show a neutral placeholder; search states that it needs a connection.
- **Location denied:** the map still works, centered on the last viewed area, and the position button explains how to enable location.
- **Shipping:** behind a flag, watching time to a fully drawn map, tile cache hit rate, and battery use per session.

## What the interviewer probes

- How many tiles does an offline download of one city need at five zoom levels?
- How do updated map tiles replace cached ones?
- The user pans quickly across a country. What limits the number of requests?
- What must change to support turn-by-turn navigation?

## Go deeper

- [Grokking Modern Mobile System Design Interview](https://www.designgurus.io/course/grokking-modern-mobile-system-design-interview?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=questions-design-a-maps-and-location-app) covers location, battery, and live tracking in its ride hailing and food delivery case studies.
- Lesson: [Ride Hailing](https://www.designgurus.io/course-play/grokking-modern-mobile-system-design-interview/doc/ride-hailing?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=questions-design-a-maps-and-location-app), in Grokking Modern Mobile System Design Interview
- Lesson: [Food Delivery](https://www.designgurus.io/course-play/grokking-modern-mobile-system-design-interview/doc/food-delivery?utm_source=github&utm_medium=repo&utm_campaign=mobile-system-design-interview&utm_content=questions-design-a-maps-and-location-app), in Grokking Modern Mobile System Design Interview
- [Background work and battery](../concepts/background-work-and-battery.md) covers background location limits.
- [Design a ride hailing app](design-a-ride-hailing-app.md) is the closest related question.
