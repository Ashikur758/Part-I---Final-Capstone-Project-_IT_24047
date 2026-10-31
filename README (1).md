# 💧 AquaSense AI

**An AI-powered IoT Water Management System built with ESP32, Java Spring Boot, and Machine Learning.**

AquaSense AI monitors water levels and flow in real time, detects leakages automatically, predicts future water demand, and controls the pump intelligently, all from a web dashboard.

![Java](https://img.shields.io/badge/Java-17+-orange?logo=openjdk)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.x-brightgreen?logo=springboot)
![ESP32](https://img.shields.io/badge/Microcontroller-ESP32-blue?logo=espressif)
![MQTT](https://img.shields.io/badge/Protocol-MQTT-purple)
![License](https://img.shields.io/badge/License-MIT-yellow)

---

## 📌 Table of Contents

- [Overview](#-overview)
- [Key Features](#-key-features)
- [System Architecture](#-system-architecture)
- [Tech Stack](#-tech-stack)
- [Hardware Components](#-hardware-components)
- [Circuit / Pin Connections](#-circuit--pin-connections)
- [Project Structure](#-project-structure)
- [AI Modules](#-ai-modules)
- [API Endpoints](#-api-endpoints)
- [MQTT Topics](#-mqtt-topics)
- [Getting Started](#-getting-started)
- [Roadmap](#-roadmap)
- [Screenshots](#-screenshots)
- [Contributing](#-contributing)
- [License](#-license)
- [Author](#-author)

---

## 🌍 Overview

Water wastage through leakage, overflow, and poor usage planning is a major problem in homes, farms, and cities. **AquaSense AI** solves this by combining:

- **IoT sensors** to collect live water data
- **A Spring Boot backend** to store, process, and expose the data
- **AI algorithms** to make smart decisions
- **A dashboard** for real-time monitoring and remote control

---

## ✨ Key Features

| Feature | Description |
|---|---|
| 📊 Real-time Monitoring | Live tank level, flow rate, and total consumption |
| 🚰 Automatic Pump Control | Auto ON/OFF based on water level thresholds |
| 🔍 Leakage Detection | AI detects abnormal flow patterns and sends alerts |
| 📈 Demand Forecasting | Predicts upcoming water usage from historical data |
| 🧠 Smart Scheduling | Runs the pump at the most efficient time |
| 🔔 Alerts & Notifications | Low level, overflow, leak, and device offline alerts |
| 🔐 Secure Access | JWT-based authentication and role-based access |
| 🖥️ Web Dashboard | Charts, device status, and manual pump control |

---

## 🏗️ System Architecture

```
┌──────────────┐      ┌──────────┐   MQTT / HTTP   ┌─────────────────────────┐
│   Sensors    │ ───▶ │  ESP32   │ ──────────────▶ │  Spring Boot Backend    │
│ (Level/Flow) │      │ (C/C++)  │                 │                         │
└──────────────┘      └────▲─────┘                 │  ┌───────────────────┐  │
                           │                       │  │  REST API         │  │
                           │ Commands              │  │  MQTT Handler     │  │
                      ┌────┴─────┐                 │  │  AI Engine        │  │
                      │ Relay +  │ ◀────────────── │  │  Alert Service    │  │
                      │  Pump    │                 │  └───────────────────┘  │
                      └──────────┘                 └───────┬────────┬────────┘
                                                           │        │
                                                   ┌───────▼──┐  ┌──▼──────────┐
                                                   │ Database │  │  Dashboard  │
                                                   │(MySQL/PG)│  │ (WebSocket) │
                                                   └──────────┘  └─────────────┘
```

---

## 🛠️ Tech Stack

**Embedded / IoT**
- ESP32 (C/C++ via Arduino IDE or PlatformIO)
- MQTT (Mosquitto / HiveMQ) and HTTP REST

**Backend**
- Java 17+
- Spring Boot (Web, Data JPA, Security, WebSocket, Validation)
- Eclipse Paho / Spring Integration MQTT
- MySQL or PostgreSQL

**AI / Machine Learning**
- Statistical methods (Z-score, moving average)
- Linear / Polynomial Regression for forecasting
- Libraries: Smile / Weka / Tribuo / DJL (optional ONNX Runtime)

**Frontend**
- HTML, CSS, JavaScript with Chart.js *(or React / Thymeleaf)*

---

## 🔧 Hardware Components

| Component | Purpose |
|---|---|
| ESP32 DevKit | Main microcontroller with built-in WiFi |
| HC-SR04 Ultrasonic Sensor | Measures tank water level |
| YF-S201 Flow Sensor | Measures water flow rate |
| Relay Module (5V) | Switches the pump ON/OFF |
| DC / AC Water Pump | Fills the tank |
| Power Supply (5V / 12V) | Powers the board and the pump |
| *(Optional)* TDS / pH Sensor | Water quality monitoring |
| *(Optional)* Soil Moisture Sensor | Smart irrigation use case |

---

## 🔌 Circuit / Pin Connections

> ⚠️ Pin numbers below are suggestions. Adjust them to match your own wiring.

| Device | Device Pin | ESP32 Pin |
|---|---|---|
| HC-SR04 | TRIG | GPIO 5 |
| HC-SR04 | ECHO | GPIO 18 (use a voltage divider: ECHO outputs 5V) |
| Flow Sensor | Signal | GPIO 27 |
| Relay | IN | GPIO 26 |
| All sensors | VCC / GND | 5V / GND |

---

## 📁 Project Structure

```
AquaSense-AI/
│
├── firmware/                      # ESP32 code (C/C++)
│   ├── src/
│   │   └── main.ino
│   └── README.md
│
├── backend/                       # Spring Boot application
│   └── src/main/java/com/aquasense/
│       ├── controller/            # REST controllers
│       ├── service/               # Business logic
│       ├── repository/            # JPA repositories
│       ├── model/                 # Entities & DTOs
│       ├── mqtt/                  # MQTT config & listeners
│       ├── ai/                    # Leak detection & forecasting
│       ├── security/              # JWT & security config
│       └── AquaSenseApplication.java
│
├── frontend/                      # Dashboard UI
│
├── docs/                          # Diagrams, report, screenshots
│
├── .gitignore
├── LICENSE
└── README.md
```

---

## 🧠 AI Modules

### 1. Leakage Detection
- Computes a rolling mean and standard deviation of flow rate.
- Flags a reading as an anomaly when its **Z-score exceeds a threshold**.
- Raises a leak alert when flow continues during normal "no usage" hours (for example, at night).

### 2. Water Demand Forecasting
- Uses historical daily and hourly consumption data.
- Applies **regression models** to predict the next day's or week's usage.
- Helps plan tank refill and reduce waste.

### 3. Smart Pump Control
- Considers the current level, predicted demand, and time of day.
- Starts and stops the pump at the optimal time rather than using fixed thresholds alone.

---

## 🌐 API Endpoints

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/api/auth/register` | Register a new user |
| `POST` | `/api/auth/login` | Login and receive a JWT |
| `POST` | `/api/devices` | Register a new device |
| `GET` | `/api/devices` | List all devices |
| `POST` | `/api/sensor-data` | Receive sensor readings from the ESP32 |
| `GET` | `/api/devices/{id}/latest` | Latest reading of a device |
| `GET` | `/api/devices/{id}/history` | Historical readings |
| `POST` | `/api/devices/{id}/pump` | Turn the pump ON/OFF manually |
| `GET` | `/api/devices/{id}/forecast` | AI demand prediction |
| `GET` | `/api/alerts` | List alerts |

**Sample sensor payload**

```json
{
  "deviceId": "ESP32-001",
  "waterLevelPercent": 72.5,
  "flowRateLpm": 3.4,
  "totalLiters": 1250.8,
  "pumpStatus": false,
  "timestamp": "2026-01-15T10:30:00Z"
}
```

---

## 📡 MQTT Topics

| Topic | Direction | Purpose |
|---|---|---|
| `aquasense/{deviceId}/data` | ESP32 → Server | Publish sensor readings |
| `aquasense/{deviceId}/command` | Server → ESP32 | Pump ON/OFF commands |
| `aquasense/{deviceId}/status` | ESP32 → Server | Online / offline status |

---

## 🚀 Getting Started

### Prerequisites

- Java 17 or later
- Maven or Gradle
- MySQL or PostgreSQL
- MQTT Broker (Mosquitto or a cloud broker)
- Arduino IDE or PlatformIO
- ESP32 board and the hardware listed above

### 1. Clone the repository

```bash
git clone https://github.com/<your-username>/AquaSense-AI.git
cd AquaSense-AI
```

### 2. Set up the database

```sql
CREATE DATABASE aquasense;
```

### 3. Configure the backend

Edit `backend/src/main/resources/application.properties`:

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/aquasense
spring.datasource.username=your_username
spring.datasource.password=your_password
spring.jpa.hibernate.ddl-auto=update

mqtt.broker.url=tcp://localhost:1883
jwt.secret=change_this_to_a_long_random_secret
```

### 4. Run the backend

```bash
cd backend
./mvnw spring-boot:run
```

The server starts at `http://localhost:8080`.

### 5. Flash the ESP32

1. Open `firmware/src/main.ino` in Arduino IDE.
2. Set your WiFi SSID, password, and server/broker address.
3. Select the **ESP32 Dev Module** board and upload.

### 6. Open the dashboard

Visit `http://localhost:8080` and log in.

---

## 🗺️ Roadmap

- [x] Project setup and architecture
- [ ] ESP32 tank level reading
- [ ] ESP32 → Spring Boot data communication
- [ ] Database storage and REST API
- [ ] Automatic pump control
- [ ] Web dashboard with live charts
- [ ] Leakage detection (Z-score)
- [ ] Water demand forecasting (regression)
- [ ] Alert system (email / SMS / push)
- [ ] Water quality monitoring (TDS / pH)
- [ ] Mobile app
- [ ] Docker deployment

---

## 🖼️ Screenshots

> Add your screenshots to the `docs/` folder and link them here.

| Dashboard | Hardware Setup |
|---|---|
| ![Dashboard](docs/dashboard.png) | ![Hardware](docs/hardware.jpg) |

---

## 🤝 Contributing

Contributions are welcome!

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/your-feature`
3. Commit your changes: `git commit -m "Add your feature"`
4. Push to the branch: `git push origin feature/your-feature`
5. Open a Pull Request

---

## 📄 License

This project is licensed under the **MIT License**. See the [LICENSE](LICENSE) file for details.

---

## 👤 Author

**Your Name**
- GitHub: [@your-username](https://github.com/your-username)
- Email: your.email@example.com

---

⭐ If you find this project useful, please give it a star!
