# CLAUDE.md

This file provides guidance to Claude Code when working with the MMM-Ecobee module.

## Project Overview

MMM-Ecobee is a MagicMirror² module that displays ecobee thermostat data including current temperature, set temperature, humidity, HVAC status, and remote sensor readings.

## Architecture

### Files

- **MMM-Ecobee.js** - Frontend module that renders the UI. Extends MagicMirror's `Module` class.
- **node_helper.js** - Backend helper that communicates with the Ecobee API. Handles OAuth authentication and data fetching.
- **MMM-Ecobee.css** - Card-based UI styling with color-coded states.
- **tokens.json** - Stores OAuth access and refresh tokens (auto-generated, not in repo).

### Data Flow

1. Frontend sends `UPDATE_SENSORS` socket notification to backend
2. Backend fetches data from Ecobee API (`/1/thermostat` endpoint)
3. Backend sends `UPDATE_MAIN_INFO` notification with thermostat data to frontend
4. Frontend re-renders UI with `updateDom()`

### Key API Fields

- `thermostat.settings.hvacMode` - Current mode: "heat", "cool", "auto", or "off"
- `thermostat.equipmentStatus` - CSV string of actively running equipment (empty when idle)
- `thermostat.runtime.desiredHeat` / `desiredCool` - Target temperatures (in tenths of degrees)
- `thermostat.remoteSensors` - Array of sensors with temperature, humidity, and occupancy capabilities

### UI States

The card border reflects active equipment status (not just mode):
- **Neutral border** - System idle (equipmentStatus is empty)
- **Orange border + glow** - Actively heating (equipmentStatus contains "heat" or "auxHeat")
- **Blue border + glow** - Actively cooling (equipmentStatus contains "compCool")

Status indicator shows:
- **ON** (green) - Equipment actively running
- **IDLE** (dimmed) - System in a mode but not currently running
- **OFF** (grey) - System turned off (hvacMode is "off")

## Development

### Testing Changes

Restart MagicMirror to see changes. The module updates every 5 minutes by default (minimum 3 minutes per Ecobee API limits).

### Color Palette

Uses cohesive colors matching the MagicMirror theme:
- Heat: `#F99B1F` (orange)
- Cool: `#36C3EA` (cyan)
- Success/Active: `#22c55e` (green)
- Auto: `#9966cc` (purple)

### Ecobee API Reference

- [Thermostat Object](https://www.ecobee.com/home/developer/api/documentation/v1/objects/Thermostat.shtml)
- [Selection Object](https://www.ecobee.com/home/developer/api/documentation/v1/objects/Selection.shtml)
- [Equipment Status](https://www.ecobee.com/home/developer/api/documentation/v1/objects/Equipment.shtml)
