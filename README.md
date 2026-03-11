# Smart Curtain Controller App

An Android application that lets you control a smart curtain system wirelessly using your smartphone. The app communicates with an ESP32 microcontroller over Wi-Fi to open/close curtains, switch operation modes, and monitor real-time environmental data like temperature, humidity, and sunlight intensity.

---

## Features

- **Splash Screen** – Branded launch screen that transitions automatically to the main controller.
- **Connection Setup** – Choose between Wi-Fi or Bluetooth to connect your phone to the Smart Curtain device.
  - **Wi-Fi** – Opens Android Wi-Fi settings so you can connect to the ESP32's hotspot, then returns you to the app.
  - **Bluetooth** – Guides you through pairing with the Smart Curtain BLE device *(in progress)*.
- **Curtain Control** – Hold the **Open** or **Close** buttons to continuously drive the curtain motor in the desired direction.
- **Mode Switching** – Toggle between **Manual** and **Auto** operation modes with the **Switch** button.
- **Real-Time Sensor Readings** – The app polls the ESP32 every second to display:
  - Outdoor temperature (°F)
  - Outdoor humidity (%)
  - Sunlight intensity level
- **Connection Status** – Automatically detects whether the phone is connected to the Smart Curtain hotspot or another Wi-Fi network.

---

## App Screens

| Screen | Description |
|---|---|
| **Launch Screen** | Splash screen shown for 1.5 seconds on startup |
| **Home** | Landing page with a "Connect Curtain" button that opens the connection popup |
| **Connect Wi-Fi** | Step-by-step guide to join the ESP32's Wi-Fi access point |
| **Connect Bluetooth** | Step-by-step guide to pair with the ESP32 over Bluetooth |
| **Controller** | Main dashboard — sensor readings, connection status, mode, and curtain controls |

---

## How It Works

The ESP32 acts as a Wi-Fi access point (hotspot). Once your phone joins that network, the app sends HTTP GET requests to the ESP32's built-in web server at `http://192.168.4.1`.

| HTTP Endpoint | Action |
|---|---|
| `GET /open` | Opens the curtain (sent repeatedly while button is held) |
| `GET /close` | Closes the curtain (sent repeatedly while button is held) |
| `GET /switch` | Toggles between Manual and Auto mode |
| `GET /temperature` | Returns the current outdoor temperature |
| `GET /humidity` | Returns the current outdoor humidity |

The app detects the connection automatically — if your phone's IP address is `192.168.4.2`, it shows **"Connected to Smart Curtain"**; otherwise it shows **"Connected to other Wi-Fi"** or **"Not Connected"**.

---

## Tech Stack

- **Language**: Java
- **Platform**: Android (min SDK 21 / Android 5.0 Lollipop, target SDK 34 / Android 14)
- **Build**: Gradle
- **Networking**: [OkHttp 4.10.0](https://square.github.io/okhttp/)
- **UI**: Material Components, AppCompat, ConstraintLayout

---

## Project Structure

```
app/src/main/
├── java/com/example/smartcurtaincontrollerapp/
│   ├── LaunchScreenActivity.java    # Splash screen
│   ├── HomeActivity.java            # Home screen with connection popup
│   ├── ConnectWifiActivity.java     # Wi-Fi connection guide
│   ├── ConnectBluetoothActivity.java# Bluetooth connection guide
│   └── ControllerActivity.java      # Main curtain controller dashboard
└── res/
    ├── layout/                      # XML layout files for each screen
    ├── drawable/                    # Icons and background shapes
    └── values/                      # Colors, strings, themes
```

---

## Permissions

The app requires the following Android permissions:

| Permission | Purpose |
|---|---|
| `INTERNET` | Send HTTP commands to the ESP32 |
| `ACCESS_WIFI_STATE` | Read the current Wi-Fi connection info |
| `ACCESS_NETWORK_STATE` | Monitor network connectivity changes |
| `CHANGE_WIFI_STATE` | Bind the process to the ESP32's Wi-Fi network |
| `CHANGE_NETWORK_STATE` | Request a specific network for HTTP traffic |

---

## Getting Started

### Prerequisites

- Android Studio (latest stable)
- Android phone running Android 5.0 (Lollipop) or later
- ESP32 board flashed with the Smart Curtain firmware

### Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/jerichoyullanes/SmartCurtainControllerApp.git
   ```
2. Open the project in **Android Studio**.
3. Let Gradle sync and download dependencies automatically.
4. Build and run the app on your Android device.

### Connecting to the Smart Curtain

1. Power on your ESP32 Smart Curtain device.
2. On your Android phone, go to **Wi-Fi settings** and connect to the Smart Curtain hotspot.
3. Open the app — the controller screen will detect the connection automatically.
4. Use the **Open** and **Close** buttons to control your curtain.

---

## Dependencies

```gradle
implementation 'androidx.appcompat:appcompat:1.6.1'
implementation 'com.google.android.material:material:1.10.0'
implementation 'androidx.constraintlayout:constraintlayout:2.1.4'
implementation 'com.squareup.okhttp3:okhttp:4.10.0'
```

---

## License

This project is licensed under the terms found in the [LICENSE](LICENSE) file.
