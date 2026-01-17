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
Visit: `https://YOUR_USERNAME.github.io/hardwareone-map-tool/`

### Local
1. Download or clone this repository
2. Open `index.html` in any modern browser
3. Draw a rectangle or polygon on the map
4. Click "Generate Map File"
5. Upload the `.hwmap` file to your device at `/maps/`

## .hwmap File Format (v3)

The binary format is optimized for ESP32 with limited RAM. **v3** adds a spatial index for faster rendering when zoomed in.

```
Header (40 bytes):
  - Magic: "HWMP" (4 bytes)
  - Version: uint16 = 3 (2 bytes)
  - Flags: uint16 (bit 0 = has spatial index)
  - Bounds: minLat, minLon, maxLat, maxLon (4×4 bytes, microdegrees)
  - Feature Count: uint32 (4 bytes)
  - Name Count: uint16 (2 bytes)
  - Region Name: 8 bytes (null-padded)
  - Padding: 2 bytes

Name Table (variable):
  For each name:
  - Length: uint8 (max 63)
  - String: char[Length] (not null-terminated)

Features (variable):
  - Type: uint8 (see Feature Types below)
  - Name Index: uint16 (0xFFFF = unnamed, otherwise index into name table)
  - Point Count: uint8
  - First Point: lat + lon (2×4 bytes, microdegrees)
  - Delta Points: lat + lon deltas (2×2 bytes each)

Spatial Index (v3 only, when flag bit 0 is set):
  - Grid Size: uint8 (e.g., 8 = 8×8 = 64 cells)
  - Reserved: uint8
  - Total Entries: uint16
  - For each cell (gridSize × gridSize, row-major):
    - Count: uint16 (features in this cell)
    - Indices: uint16[Count] (feature indices)
```

### Feature Types

| Type | Description |
|------|-------------|
| 0x00 | Highway (motorway, trunk) |
| 0x01 | Major road (primary, secondary) |
| 0x02 | Minor road (tertiary, residential) |
| 0x03 | Path (footway, cycleway) |
| 0x10 | Water (rivers, lakes) |
| 0x11 | Park/Nature |
| 0x20 | Railway |
| 0x21 | Bus route |
| 0x22 | Ferry route |
| 0x30 | Building |
| 0x40 | Transit station |

### Spatial Index

The spatial index divides the map into an 8×8 grid (64 cells). Features are assigned to all cells their bounding box intersects. At render time, only features in visible cells are processed, with deduplication to prevent drawing the same feature twice.

**Performance**: When zoomed in 10×, typically 80-95% of features are skipped.

### Size Overhead

- Name table: ~2-5KB for typical maps
- Spatial index: ~1-5% of file size (depends on feature density)
- Total overhead: typically 5-15% of file size

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
