# HardwareOne Map Converter

A web-based tool to create offline map files (`.hwmap`) for ESP32 devices running HardwareOne firmware.

## Features

- **Interactive Map Selection** - Draw a rectangle or polygon to select your area
- **Customizable Features** - Choose which map elements to include (roads, water, parks)
- **Feature Names** - Street names, highway refs, park names embedded for context & search
- **Size Estimation** - See estimated file size before downloading
- **Compact Binary Format** - Optimized for embedded devices with limited storage
- **No Dependencies** - Runs entirely in your browser

## Usage

### Online (GitHub Pages)
Visit: [Github Pages Link](https://cadengithubb.github.io/hardwareone-map-tool/)

### How to run this Locally
1. Download or clone this repository
2. Open `index.html` in any modern browser
3. Draw a rectangle or polygon on the map
4. Click "Generate Map File"
5. Upload the `.hwmap` file to your device at `/maps/`

## How to add map to device

On your HardwareOne device:
1. Upload `.hwmap` files to `/maps/` directory
2. The GPS OLED mode will display the map when a fix is acquired
3. Multiple map regions are supported - device auto-selects based on GPS location

## Data Source

Map data is fetched from [OpenStreetMap](https://www.openstreetmap.org/) via the [Overpass API](https://overpass-api.de/).

## License

MIT License - See LICENSE file
