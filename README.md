[README.md](https://github.com/user-attachments/files/27307366/README.md)
# Custom Air Quality Card

A polished Home Assistant Lovelace card that combines air quality, climate, and trend data into one beautiful at-a-glance view.

![Air Quality Card Preview](preview.png)

## Features

- **Overall air quality score (0-100)** with a circular gauge that color-shifts from green to red
- **Live status pill** — Good / Moderate / Poor / Bad
- **Smart advice** that updates based on what's elevated ("CO2 high - open a window", "Particles elevated", etc.)
- **24-hour trend graph** for temperature and humidity
- **Pollutant tiles** for PM1.0, PM2.5, PM10, VOC, and CO2 with threshold-based colors
- Works with **any compatible sensors** — ESPHome devices, commercial air monitors, BLE sensors via Bluetooth proxy

## Requirements

Install these from [HACS](https://hacs.xyz) (Frontend section):

- [mod-card](https://github.com/thomasloven/lovelace-mod-card)
- [button-card](https://github.com/custom-cards/button-card)
- [mini-graph-card](https://github.com/kalkih/mini-graph-card)

You'll also need sensors providing:

- PM1.0, PM2.5, PM10 (e.g. PMS5003, SDS011, ESPHome PM sensors)
- VOC index (e.g. SGP40, SGP41)
- CO2 (e.g. SCD41, SCD30, MH-Z19, SwitchBot Meter Pro CO2)
- Temperature
- Humidity

Don't have all of them? Remove the relevant tiles or set their entities to a dummy sensor — the card handles missing values gracefully.

## Installation

1. Open your Home Assistant dashboard
2. Click the three-dot menu → **Edit dashboard**
3. Click **+ Add Card** → scroll to the bottom → **Manual**
4. Paste the contents of [`air-quality-card.yaml`](air-quality-card.yaml)
5. Replace the 5 entity IDs marked with `REPLACE:` comments
6. Save

## What to replace

Open the YAML and find/replace these placeholders with your own entity IDs:

| Placeholder | What to replace it with |
|-------------|-------------------------|
| `sensor.YOUR_PM1_ENTITY` | Your PM1.0 sensor |
| `sensor.YOUR_PM25_ENTITY` | Your PM2.5 sensor |
| `sensor.YOUR_PM10_ENTITY` | Your PM10 sensor |
| `sensor.YOUR_VOC_ENTITY` | Your VOC index sensor |
| `sensor.YOUR_CO2_ENTITY` | Your CO2 sensor |
| `sensor.YOUR_TEMPERATURE_ENTITY` | Your temperature sensor |
| `sensor.YOUR_HUMIDITY_ENTITY` | Your humidity sensor |

Tip: in Home Assistant, go to **Developer Tools → States** to find your exact entity IDs.

## How the score works

The score starts at 100 and subtracts penalties:

| Pollutant | Max penalty | Scaling |
|-----------|-------------|---------|
| PM2.5 | 40 points | Linear from 0 to 35 µg/m³ |
| CO2 | 35 points | Linear from 400 to 2000 ppm |
| VOC | 25 points | Linear from 0 to 300 (index) |

Status pill colors:

- **80-100** Good (green)
- **60-79** Moderate (yellow)
- **40-59** Poor (orange)
- **0-39** Bad (red)

You can tweak the weightings or thresholds by editing the JS in the `body:` and `tiles:` custom fields.

## Tile thresholds

Based on WHO, EPA, and ASHRAE guidelines:

| Tile | GOOD | MOD | HIGH | V.HIGH |
|------|------|-----|------|--------|
| PM1.0 | ≤10 | ≤25 | ≤50 | >50 |
| PM2.5 | ≤12 | ≤35 | ≤75 | >75 |
| PM10 | ≤50 | ≤150 | ≤250 | >250 |
| VOC | ≤100 | ≤200 | ≤300 | >300 |
| CO2 (ppm) | ≤800 | ≤1200 | ≤2000 | >2000 |

Adjust to your environment — older buildings, kitchens, or smokers may want looser thresholds.

## My setup

For reference, here's what I'm running:

- **ESPHome air monitor** (ESP32 + PMS5003 + SGP41 VOC sensor) for PM and VOC
- **SwitchBot Meter Pro CO2** for CO2, connected via Bluetooth proxy on a separate ESP32
- **Separate temperature and humidity sensor** on the trusted LAN

The card doesn't care where the data comes from — any HA sensor with the right `device_class` works.

## Preview

A standalone HTML preview is included as [`preview.html`](preview.html). Open it in any browser to see what the card looks like before installing.

## Contributing

Found a bug, have an improvement, or built a variant (different pollutants, different layout)? Open an issue or PR.

## License

MIT — see [LICENSE](LICENSE).

---

If this card was useful, a star on the repo is appreciated.
