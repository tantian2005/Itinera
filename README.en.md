# Itinera 路书

[中文](README.md) | **English**

Turn a travel guide into an itinerary map you can edit: day-by-day timeline, transport, drag to reorder, share as an image.

**Use it online: <https://tantian2005.github.io/Itinera/>**

There are no accounts and no server. Your trips are kept in your own browser, and your guide is never uploaded.

## How it works

1. On the start page, press **Copy prompt** and send the prompt to ChatGPT, Claude or another AI together with your travel guide.
2. Paste the AI's reply, upload the .json file it made, or drop the file on the page.
3. Fix what the checks at the top point out, edit and reorder as you like, then share an image or export the JSON.

To see what it looks like first, open one of the samples on the start page: Kyoto and Osaka, or Chengdu.

## Features

**Import**
- Text outside the code block is ignored.
- Small format errors are fixed, such as comments, single quotes, missing commas and output that was cut off. The page tells you what it fixed.
- Field names are matched loosely: case is ignored, and common aliases are accepted, such as `latitude` or `coords`.
- Coordinates can be written in degrees, for example `51.5089° N`.
- Places without coordinates are looked up by name and marked approximate; only those that cannot be found are reported.
- AIs often write the departure time as the arrival time. Arrival and departure times are worked out from the times in the transport notes around each stop.

**Checks**
After import, the side panel lists the mistakes AIs often make:
- a stop that is far from the stops before and after it
- a day that does not start or end at the place you stay
- the same journey written twice
- transport written into the notes, where the map cannot show it

Some of these can be fixed with one click. For the rest, **Copy notes for the AI** writes a message you can send back to the AI.

**Map**
- Curved routes, with dashed lines for long trips and flights and arrows showing the direction.
- Street and satellite base maps, your location, a scale bar, and place names next to markers when you zoom in.
- A search box on the map finds places, and also accepts coordinates or a link from Google Maps, Amap or Baidu Maps.
- Mainland China uses WGS-84 coordinates throughout. Links from Amap, Baidu or Google's China map are converted from GCJ-02 or BD-09, and places in China can be opened in Amap.
- Right-click the map (long-press on a phone) to add a place right there.

**Timeline**
- Each stop shows when you arrive and leave.
- Between stops, the transport is shown with an icon for each leg and its times.
- The transport mode can be chosen by hand. Otherwise it is read from the text, or guessed from the speed.

**Editing**
- Everything is edited in place, with no pop-ups.
- Drag the handles to reorder stops, also from one day to another.
- The toolbar has undo, redo, add place and move places.

**Sharing**
- **Share image** makes one long image with the map on top and the timeline below.
- On a phone you can send it straight from the share sheet; on a computer it saves as a PNG.

**Other**
- Chinese and English, light and dark.
- On a computer you can drag the edge of the side panel to change its width.
- On a phone the map fills the screen and the itinerary sits in a bottom sheet you can drag.

## Run it locally

It is a static page with everything in `index.html`, and no build step:

```bash
python -m http.server 8000
```

Then open <http://localhost:8000/>. The map and Leaflet need an internet connection.

You can also open `index.html` directly. OpenStreetMap does not serve tiles to pages opened from a file, so the page switches to Esri's street map.

## Deploy

Publish the repository root on any static host. This project uses GitHub Pages from the root of the `main` branch.

## JSON format

```json
{
  "version": 1,
  "title": "Paris in 2 days",
  "dateRange": "5.1–5.2",
  "places": {
    "hotel": {"name": "Hôtel du Nord", "lat": 48.8721, "lng": 2.3426, "approx": true, "kind": "hotel", "desc": "", "facts": []},
    "louvre": {"name": "Louvre", "nameLocal": "Musée du Louvre", "lat": 48.8606, "lng": 2.3376, "approx": false,
               "kind": "sight", "desc": "Booking required", "facts": [["Hours", "09:00–18:00"]], "link": "https://www.louvre.fr"}
  },
  "days": [
    {"date": "5.2", "weekday": "Sat", "title": "Louvre", "notes": "Breakfast 07:30",
     "stops": [
       {"place": "hotel", "time": "", "end": "08:30", "mode": "subway", "leg": "08:30–08:50 Metro line 7"},
       {"place": "louvre", "time": "09:00", "end": "12:00", "leg": ""}
     ]}
  ],
  "backups": []
}
```

| Field | Meaning |
|---|---|
| `kind` | `sight`, `food`, `hotel`, `transport` or `backup` |
| `approx` | whether the coordinates are only approximate |
| `time` / `end` | when you arrive and when you leave |
| `leg` | how to get to the next stop; separate several legs with `;` and start each with its times |
| `mode` | main way to the next stop: `walk`, `subway`, `bus`, `train`, `car`, `boat` or `flight`; optional |
| `nameLocal`, `link` | local name or address, and website; optional |

## Data sources

- Maps: © [OpenStreetMap](https://www.openstreetmap.org/copyright) contributors; Esri World Street Map when the page is opened from a file; satellite images from Esri World Imagery
- Place search: [Photon](https://photon.komoot.io) (OpenStreetMap data), with Nominatim as a fallback
- Map library: [Leaflet](https://leafletjs.com)
