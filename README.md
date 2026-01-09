# HardwareOne Map Converter

A web-based tool to create offline map files (`.hwmap`) for ESP32 devices running HardwareOne firmware.

## Features

- **Interactive Map Selection** - Draw a rectangle to select your area
- **Customizable Features** - Choose which map elements to include (roads, water, parks)
- **Size Estimation** - See estimated file size before downloading
- **Compact Binary Format** - Optimized for embedded devices with limited storage
- **No Dependencies** - Runs entirely in your browser

## Usage

### Online (GitHub Pages)
Visit: `https://YOUR_USERNAME.github.io/hardwareone-map-tool/`

### Local
1. Download or clone this repository
2. Open `index.html` in any modern browser
3. Draw a rectangle on the map
4. Click "Generate Map File"
5. Upload the `.hwmap` file to your device at `/maps/`

## .hwmap File Format

The binary format is optimized for ESP32 with limited RAM:

```
Header (32 bytes):
  - Magic: "HWMP" (4 bytes)
  - Version: uint16 (2 bytes)
  - Flags: uint16 (2 bytes)
  - Bounds: minLat, minLon, maxLat, maxLon (4×4 bytes, microdegrees)
  - Feature Count: uint32 (4 bytes)
  - Region Name: 8 bytes (null-padded)

Features (variable):
  - Type: uint8 (0x01=major road, 0x02=minor road, 0x10=water, 0x11=park)
  - Point Count: uint8
  - First Point: lat + lon (2×4 bytes, microdegrees)
  - Delta Points: lat + lon deltas (2×2 bytes each)
```

## Device Integration

On your HardwareOne device:
1. Upload `.hwmap` files to `/maps/` directory
2. The GPS OLED mode will display the map when a fix is acquired
3. Multiple map regions are supported - device auto-selects based on GPS location

## Storage Guidelines

| Area Size | Detail Level | Approximate Size |
|-----------|--------------|------------------|
| 5×5 km | All features | 150-250 KB |
| 10×10 km | Major roads only | 100-200 KB |
| 10×10 km | All features | 300-500 KB |

Recommended: Keep total map storage under 800 KB to leave room for data logging.

## Data Source

Map data is fetched from [OpenStreetMap](https://www.openstreetmap.org/) via the [Overpass API](https://overpass-api.de/).

## License

MIT License - See LICENSE file
