# Android-Based Renewable Energy Powered Smart Curtain with Automatic Light and Temperature Sensor

A personal capstone project that automates curtain control using an ESP32 embedded system and an Android mobile application. The curtain opens or closes automatically based on sunlight intensity and temperature, and can also be controlled manually through the app or physical push buttons.

---

## 📁 Repositories

| Repository | Description | Language |
|---|---|---|
| [SmartCurtainControllerApp](https://github.com/jerichoyullanes/SmartCurtainControllerApp) | Android mobile controller app | Java |
| [SmartCurtainSystem](https://github.com/jerichoyullanes/SmartCurtainSystem) | ESP32 embedded system firmware | C++ (Arduino) |

---

## 📱 SmartCurtainControllerApp — Android App

### Overview

An Android mobile application that allows the user to remotely monitor and control the smart curtain system over a local Wi-Fi connection. It displays real-time sensor data (temperature and humidity) and sends commands to open, close, or switch the curtain's operating mode.

### Tech Stack

- **Language:** Java
- **Platform:** Android (API 21 – Android 5.0 to API 34 – Android 14)
- **UI:** Material Design 3, ConstraintLayout
- **HTTP Client:** OkHttp 4.10.0
- **Build Tool:** Gradle with Android Gradle Plugin 7.4.2

### Required Permissions

```xml
INTERNET
ACCESS_WIFI_STATE
ACCESS_NETWORK_STATE
CHANGE_WIFI_STATE
CHANGE_NETWORK_STATE
```

### Screens

| Screen | Description |
|---|---|
| **Launch Screen** | 1.5-second splash screen on app startup |
| **Home Screen** | Shows connection status; button to connect curtain via Wi-Fi or Bluetooth |
| **Controller Screen** | Main interface — displays sensor readings, connection status, mode, and curtain control buttons |
| **Wi-Fi Connection Screen** | Step-by-step guide to connect the phone to the Smart Curtain Wi-Fi network |
| **Bluetooth Connection Screen** | Bluetooth/BLE pairing instructions |

### Features

- Real-time temperature (°F) and humidity (%) display, updated every second
- Curtain open and close controls (hold to operate)
- Mode switching between Manual and Automatic
- Automatic Wi-Fi connection detection
- Clean, minimal Material Design UI

### Hardware Communication

The app connects to the ESP32 over a local Wi-Fi network using HTTP GET requests.

| Endpoint | Action |
|---|---|
| `GET /open` | Opens the curtain |
| `GET /close` | Closes the curtain |
| `GET /switch` | Toggles between Manual and Automatic mode |
| `GET /temperture` | Returns current temperature (°F) _(note: endpoint name has a typo in the firmware)_ |
| `GET /humidity` | Returns current humidity (%) |

- **Base URL:** `http://192.168.4.1/`
- **Protocol:** HTTP (local network only, no internet required)
- **Polling interval:** 1 second (temperature and humidity)

### Setup & Installation

**Prerequisites:**
- Android Studio (latest version)
- Android SDK API 34
- JDK 1.8 or higher

**Steps:**
1. Clone the repository:
   ```bash
   git clone https://github.com/jerichoyullanes/SmartCurtainControllerApp.git
   ```
2. Open the project in Android Studio.
3. Wait for Gradle sync to complete.
4. Connect an Android device via USB (with USB Debugging enabled) or launch an emulator.
5. Click **Run** or use the command line:
   ```bash
   ./gradlew installDebug
   ```

**Useful Gradle commands:**
```bash
./gradlew assembleDebug       # Build debug APK
./gradlew assembleRelease     # Build release APK
./gradlew installDebug        # Build and install on connected device
./gradlew test                # Run unit tests
./gradlew lint                # Run static code analysis
./gradlew clean               # Clean build artifacts
```

### Connecting to the Smart Curtain

1. Launch the app — it opens directly to the Controller screen.
2. Tap **Connect Curtain** → choose **Wi-Fi**.
3. The app opens the system Wi-Fi settings — connect to the **SmartCurtain** network (password: `12345678`).
4. Return to the app. It will detect the connection automatically.
5. Sensor readings will begin updating and the curtain controls will become active.

---

## ⚙️ SmartCurtainSystem — ESP32 Firmware

### Overview

An Arduino/C++ sketch for an ESP32 microcontroller that serves as the brain of the smart curtain system. It hosts a local Wi-Fi access point, runs an HTTP web server to receive commands from the Android app, reads environmental sensors, and drives a 12V DC motor to open or close the curtain.

### Tech Stack

- **Language:** C++ (Arduino framework)
- **Microcontroller:** ESP32
- **IDE:** Arduino IDE
- **Libraries:**
  - `WiFi.h` — Wi-Fi access point and HTTP server
  - `dht11.h` — DHT11 temperature and humidity sensor
  - `OneButton.h` — Multi-function push button handling

### Hardware Components

| Component | Purpose | GPIO Pin(s) |
|---|---|---|
| ESP32 | Main microcontroller | — |
| LDR (Photoresistor) | Measures sunlight intensity | 32 |
| DHT11 Sensor | Measures temperature and humidity | 33 |
| 12V DC Motor | Opens and closes the curtain | 0, 2, 4 (ENA) |
| HC-SR04 Ultrasonic Sensor | Detects curtain close position (distance < 20 cm) | Trig: 13, Echo: 14 |
| Limit Switches (×2) | Detect fully open curtain position | 34, 35 |
| Push Button — Open | Manually opens the curtain | 25 |
| Push Button — Close | Manually closes the curtain | 26 |
| Push Button — Mode Switch | Toggles Manual / Automatic mode | 27 |
| LED — Manual Indicator | Lights up when in Manual mode | 16 |
| LED — Automatic Indicator | Lights up when in Automatic mode | 17 |

### Circuit Design

A Fritzing circuit diagram is included in the repository:
```
ESP32 Version Final (12v DC Motor Version) .fzz
```

### Operating Modes

**Manual Mode (default):**
- The curtain is controlled by physical push buttons or commands from the Android app.
- Open button (GPIO 25): opens the curtain while held.
- Close button (GPIO 26): closes the curtain while held.

**Automatic Mode:**
- The curtain opens or closes automatically based on the LDR sensor reading.
- LDR value > 3000 (bright) → Open curtain
- LDR value ≤ 3000 (dark) → Close curtain

**Safety stops:**
- Curtain stops opening when both limit switches are triggered (fully open).
- Curtain stops closing when the ultrasonic sensor reads distance < 20 cm (fully closed).

### Wi-Fi Configuration

| Setting | Value |
|---|---|
| SSID | `SmartCurtain` |
| Password | `12345678` |
| IP Address | `192.168.4.1` |
| Port | `80` |

### Setup & Installation

**Prerequisites:**
- Arduino IDE (version 1.8.x or 2.x)
- ESP32 board package installed in Arduino IDE
- Required libraries:
  - `dht11` — install via Arduino Library Manager
  - `OneButton` — install via Arduino Library Manager

**Steps:**
1. Clone the repository:
   ```bash
   git clone https://github.com/jerichoyullanes/SmartCurtainSystem.git
   ```
2. Open `smart_curtain_system/smart_curtain_system.ino` in the Arduino IDE.
3. In the Arduino IDE:
   - Go to **Tools → Board** and select **ESP32 Dev Module**.
   - Go to **Tools → Port** and select the correct COM port for your ESP32.
4. Click **Upload** to flash the firmware.
5. Open the Serial Monitor (baud rate: `921600`) to verify the system starts and the IP address is printed.

### Serial Monitor Output Example

```
Access Point started
IP address: 192.168.4.1
Light Resistance: 2450
Humidity (%): 65.00
Temperature (C): 28.00
MODE: MANUAL
```

---

## 🏗️ System Architecture

```
┌─────────────────────────────────┐
│  Android App (SmartCurtainControllerApp) │
│  Java • OkHttp • Material Design        │
└────────────────┬────────────────┘
                 │ HTTP GET (local Wi-Fi)
                 ▼
┌─────────────────────────────────┐
│  ESP32 @ 192.168.4.1            │
│  Wi-Fi AP: SmartCurtain         │
│  HTTP Web Server (Port 80)      │
│                                 │
│  Inputs:                        │
│  • LDR (sunlight)               │
│  • DHT11 (temp + humidity)      │
│  • HC-SR04 (distance)           │
│  • Limit Switches               │
│  • Push Buttons                 │
│                                 │
│  Outputs:                       │
│  • 12V DC Motor (open/close)    │
│  • Status LEDs                  │
└─────────────────────────────────┘
```

---

## 🔄 How It Works

1. The ESP32 starts up and creates a Wi-Fi access point named **SmartCurtain**.
2. The user connects their Android phone to this Wi-Fi network.
3. The Android app sends HTTP commands to `192.168.4.1` to open, close, or switch modes.
4. The app polls the `/temperture` (typo in firmware) and `/humidity` endpoints every second to display live sensor data.
5. In **Automatic Mode**, the ESP32 independently reads the LDR sensor and opens or closes the curtain based on the detected light level — no app interaction needed.
6. Physical push buttons on the ESP32 system allow operation even without the phone.

---

## 👤 Author

**Jericho Yu Llanes**
Capstone Project — 2023

---

## 📄 License

This project is proprietary. All rights reserved © 2023 Jericho Yu Llanes.
Use, copying, modification, or distribution without explicit written permission is prohibited.
See the [LICENSE](LICENSE) file for full details.
