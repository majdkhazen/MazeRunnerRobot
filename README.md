# Maze Runner Robot

An **ESP32 maze-navigation robot** combining C++ firmware, distance sensing and motor control with a Python maze-planning application.

## How it works

1. A Tkinter interface builds and validates a grid maze.
2. A weighted A* search finds a path from start to goal.
3. A translation layer converts grid movements into turn instructions and junction counts.
4. Python writes instructions to a Google Sheet. A Google Apps Script endpoint makes them available to the ESP32.
5. The firmware reads three VL53L0X distance sensors, counts junctions, controls the motors, and adjusts its course using wall measurements.

## Repository guide

| Path | Purpose |
| --- | --- |
| `MazeGUI/` | Maze editor, environment, search algorithms, and robot-action translation |
| `MazeRunnerRobot/` | Main Arduino/ESP32 firmware |
| `MazeRunnerRobotDEBUG/` | Debug firmware with WebSerial support |
| `sensor/`, `motors/`, `MotorSensor/` | Component tests and integration experiments |
| `SheetScript/Code.gs` | Google Apps Script integration |
| [UserManual.pdf](UserManual.pdf) | Project user manual |

## Hardware and firmware

The source targets an ESP32, three VL53L0X sensors, two motors, and a motor driver. GPIO assignments, speeds, distance thresholds, and turn durations are defined in `defs.h`.

1. Install Arduino IDE with ESP32 board support.
2. Install Adafruit VL53L0X and ArduinoJson. The debug variant additionally includes AsyncTCP, ESPAsyncWebServer, and WebSerial.
3. Open `MazeRunnerRobot/MazeRunnerRobot.ino`.
4. Configure your own Wi-Fi settings in `defs.h` and your Apps Script deployment URL in `comm.h`.
5. Check wiring against the GPIO definitions and user manual, choose the matching ESP32 board/port, then compile and upload.

Library/core versions are not pinned. Hardware-specific settings require calibration; this documentation update does not certify a successful build or hardware run.

## Python application

Use a Python 3 environment with Tkinter, NumPy, Gym, IPython, gspread, and oauth2client. Inspect imports for additional dependencies before running.

`MazeGUI/MazeSolver.py` opens the maze editor, solves the maze, and transmits the result. Transmission expects a locally supplied `JSONkey.json` service-account file and a Google Sheet named `Transmit` accessible to that account. Configure your own Sheet and Apps Script deployment; do not commit credentials.

```sh
cd MazeGUI
python MazeSolver.py
```

The current script transmits automatically after solving. To explore planning locally, disable the final `transmitSolution(solution)` call in a local copy.

## Team project and provenance

This is Majd Khazen's fork of the shared project at [abdkeb/MazeRunnerRobot](https://github.com/abdkeb/MazeRunnerRobot). The repository includes team work; individual file authorship should not be inferred from the fork owner. Original project history and credits are preserved.
