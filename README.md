# Travel widgets for Notion

Static vanilla HTML/CSS/JS widgets: `countdown.html`, `map.html`, same-origin
vendored `land.js`, and `index.html` demo links. No keys, analytics, remote fonts,
API calls or third-party requests. Both widgets set `referrer` to `no-referrer`.
All bundled flight examples are synthetic; map examples are generic cities.

## Privacy

Widgets read URL query parameters. Live embed URLs disclose those parameters to
the hosting provider and may appear in browser history, logs, and Notion previews.
A private Notion page does not make the widget URL private. Never include booking
references, seats, payment data, personal names or secrets. Do not commit live
embed URLs into this public repository.

## Hosting

Publish this directory as the GitHub Pages main-branch root. Embed
`https://<user>.github.io/notion-widgets/countdown.html?...` and
`https://<user>.github.io/notion-widgets/map.html?...` in Notion.

## Countdown parameters

| Parameter | Required | Example | Notes |
| --- | --- | --- | --- |
| `flight` | yes | `ZZ100` | Flight number, uppercase |
| `from` | yes | `AAA` | Origin IATA code |
| `to` | yes | `BBB` | Destination IATA code |
| `dep` | yes | `2030-01-01T13:10:00%2B04:00` | ISO 8601, explicit UTC offset |
| `label` | no | `Demo%20trip` | Default: Next flight |
| `alert` | no | `1` | Orange time-mismatch warning |

Live days/hours/minutes/seconds tick every second. Footer displays departure wall
time and offset, plus viewer-local time when different. After departure, a departed
state replaces the countdown. Missing fields or invalid/offset-free timestamps show
an empty state. A raw `+` becomes a space in query parsing: always encode as `%2B`.

## Map parameters

| Parameter | Example | Meaning |
| --- | --- | --- |
| `visited` | `Paris:48.86:2.35,London:51.51:-0.13` | Green pins |
| `planned` | `Tokyo:35.68:139.69,Oslo:59.91:10.75` | Blue pins |
| `home` | `Example:0:0` | Gray ring + dot; first entry only |

Use comma-separated `Name:lat:lon` entries. Names must not contain commas or colons.
Coordinates: latitude -90..90, longitude -180..180. Invalid entries are skipped.
Hover titles and legend chips show names; the header shows visited/planned counts.
No destinations shows a base map and a hint.

## Design and layout

`prefers-color-scheme` handles light/dark mode. Transparent backgrounds blend into
Notion. Blue = planned, green = visited, orange = attention, gray = home/neutral.
Widths 300–900px. Target embed heights: countdown 220px, map 420px. The map scales
with width (viewBox 360×145), and the chip legend wraps.

## Map geometry and attribution

Made with [Natural Earth](https://www.naturalearthdata.com/) (public domain).
[Source GeoJSON](https://raw.githubusercontent.com/nvkelso/natural-earth-vector/master/geojson/ne_110m_land.geojson).

`land.js` vendors Natural Earth 110m land exterior rings, simplified with
Douglas-Peucker tolerance 0.12 degrees and rounded to 3 decimals. Inland holes are
omitted at this display scale. 126 polygons, 3,798 points, about 64 KB.
Format: `window.WORLD_LAND = [[[lon,lat], ...], ...]`. The viewport crops latitudes
outside 85 N to 60 S. Decorative only, not for navigation.

## Synthetic examples

```
countdown.html?flight=ZZ100&from=AAA&to=BBB&dep=2030-01-01T13:10:00%2B04:00&label=Demo%20trip&alert=1
map.html?visited=Paris:48.86:2.35,London:51.51:-0.13,Sydney:-33.87:151.21&planned=Tokyo:35.68:139.69,Oslo:59.91:10.75&home=Example:0:0
```

## Testing

Serve this directory with a static server and open `index.html`. File URLs also
work: the map uses a plain script tag, not fetch. Test both widgets in light/dark
at 400px and 800px widths, with the heights above. Include empty, departed,
invalid-offset and home-label cases. Untrusted names are assigned using
`textContent` or escaped before HTML insertion; counts and fixed strings are safe.
