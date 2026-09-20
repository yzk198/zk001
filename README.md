# Glassmorphism Toolbox

A lightweight, browser-based desktop-style tool dashboard with glassmorphism design. All tools run locally in your browser, no backend server required.

## Introduction

This project is a lightweight desktop-style tool panel running in the browser with glassmorphism design. All tools run locally in your browser without a backend. The main control panel stays visible, other tools are hidden by default and can be opened by clicking hyperlinks. All tool windows support free dragging.

## Features

### Main Control Center (Always Visible)

- Image drag & drop viewer: Drop an image to open it in a new browser tab
- Interactive calendar: Switch months, select and display the chosen date
- Stopwatch: Start, pause and reset timer

### Extra Tools (Opened by clicking hyperlinks)

- 🎲 Dice Roller: Animated dice roll with roll history
- 📋 Todo List: Add, complete and delete tasks; data saved locally
- 📏 Unit Converter: Length & weight unit conversion, one-click swap conversion direction
- 📝 Sticky Note: Auto-saved local text note
- ⏱ Countdown Timer: Custom minutes & seconds, alert popup when finished
- 🎵 Music Player: Load local audio files, playback control, progress bar and volume slider
- 🔑 Password Generator: Custom password length, toggle character types, one-click copy password

## Tech Stack

- HTML
- CSS (Glassmorphism style with backdrop-filter blur)
- Vanilla JavaScript
- LocalStorage: Store todo items and note data

## How to Use

1. Copy all source code and save it as an `.html` file (e.g. `toolbox.html`)
2. Open the file with any modern browser (Chrome, Edge, Firefox, Safari)
3. Use image drag, calendar and stopwatch directly on main control center
4. Click hyperlinks at the bottom of control center to open tool windows
5. Drag window header to move floating windows; click `✕` or re-click hyperlink to close

## Notes

1. All data is stored in browser LocalStorage. Clearing browser cache will erase todos and notes.
2. The music player only loads local audio files and will not upload data to any server.
3. If popups are blocked by browser, please allow popup permission for this page.
