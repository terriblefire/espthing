# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**espthing** is a hardware project for an Amiga Serial Port to ESP01 breakout board. The board enables serial communication between a Commodore Amiga computer and an ESP-01 WiFi module, with the potential to power the ESP-01 directly from the Amiga's serial port.

This is a **hardware design project**, not a software project. It contains PCB design files, schematics, and manufacturing outputs - no code to build or test.

## Project Structure

The repository contains Autodesk EAGLE PCB design files:

- `eagle/espthing.sch` - Schematic design (XML format)
- `eagle/espthing.brd` - PCB board layout (XML format)
- `eagle/espthing.s#1`, `espthing.b#1` - Backup files from EAGLE editor
- `esp_gerbers.zip` - Manufacturing gerber files for PCB fabrication
- `espthing_bom.csv` - Bill of Materials for component ordering
- `images/` - PNG exports of schematic and board layout for documentation

## Hardware Architecture

The design implements a level-shifting RS-232 interface with the following key components:

**Power Supply Chain:**
- Input: 12V @ 20mA from Amiga DB25 serial port
- LM1117-3.3 (SOT223): Steps down 12V to 3.3V for ESP-01 and MAX3232
- Optional diode (D1): Protects the Amiga from back-feed
- External 12V jumper (12VPWR): Alternative power input if Amiga port insufficient

**Serial Interface:**
- MAX3232 (SOIC16): RS-232 level converter (12V ↔ 3.3V TTL)
- Capacitors C1-C8: Charge pumps and bypass for MAX3232 operation
- DB25 Female (F25HP): Connects to Amiga serial port
- 2x4 pin header (ESPHEADER): Standard ESP-01 module socket

**Key Design Constraint:** The Amiga serial port specification allows 20mA @ 12V, which should be sufficient to power both the ESP-01 (~70mA) and MAX3232 (~5mA) after voltage regulation, though this is untested.

## Working with this Repository

Since this is a hardware project using EAGLE CAD:

**Viewing/Editing Designs:**
- Use Autodesk EAGLE (free version available) to open `.sch` and `.brd` files
- Schematic and board PNGs are available in `images/` for quick reference

**Manufacturing:**
- `esp_gerbers.zip` contains production-ready gerber files
- `espthing_bom.csv` lists all components needed for assembly
- README.md includes complete BOM table with package sizes

**Modifying the Design:**
- Open files in EAGLE PCB editor (not a text/code editor)
- Both `.sch` (schematic) and `.brd` (board layout) must be kept synchronized
- Export new gerbers after board changes for manufacturing
- Update BOM CSV if components change

## File Formats

- `.sch`, `.brd` - EAGLE design files (XML-based but require EAGLE to edit properly)
- `.s#1`, `.b#1` - EAGLE automatic backup files
- Gerber files (in zip) - Industry-standard PCB manufacturing format
- BOM available in both CSV and Numbers spreadsheet formats
