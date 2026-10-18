# smart-car-seat-belt-system
A Smart Car Seat Belt System designed to improve vehicle safety by monitoring seat belt usage and helping prevent unsafe driving through intelligent safety features.
# Smart Car Seat Belt: Accident Detection and Live Location Alerts

An Arduino-based prototype that detects a collision, sends an SMS with the vehicle's GPS location to an emergency contact, keeps sending location updates, and uploads live sensor data and position to the cloud.

B.Tech final-year project, Computer Science and Engineering (IoT), Malla Reddy University, Hyderabad (2025).

## Features

- **Crash detection** with a vibration sensor, using an interrupt so no impact is missed while the device is busy.
- **Live GPS location** from a NEO-6M module.
- **Crash SMS** through a GSM module with a Google Maps link to the current position. It sends at most one per minute, so a long vibration does not flood the contact.
- **Follow-up tracking SMS** every 2 minutes (5 times) after a crash, so responders can follow a moving vehicle.
- **ThingSpeak live map**: each upload carries latitude and longitude, shown on the channel's Map widget.
- **Pulse (BPM)** from a pulse sensor, with an abnormal-value warning.
- **Cabin temperature and humidity** with a DHT11 and a high-temperature warning.
- **Motion data** (gyro X) from an MPU6050.
- **Live status** on a 16x2 LCD, including GPS state ("searching", coordinates, or wiring problem).

## Firmware versions

| File | Description |
|---|---|
| `smart_seat_belt_gps.ino` | Main version with live GPS location sharing |
| `smart_seat_belt.ino` | Earlier version with a fixed location link (no GPS) |

## Hardware

| Part | Connection |
|---|---|
| Arduino Uno | Main controller |
| 16x2 LCD | D2 to D7 (RS, E, D4 to D7) |
| ESP8266 (Wi-Fi) | TX to D9, RX to D10 |
| NEO-6M GPS | TX to D8, RX to D12 (RX optional) |
| DHT11 | D11 |
| Pulse sensor | A0 |
| Vibration sensor (digital) | A1 |
| MPU6050 | SDA/SCL on A4/A5 |
| GSM module (SIM800/SIM900) | Hardware UART (D0/D1) |

Notes:
- Disconnect the GSM module from D0/D1 while uploading the sketch.
- The ESP8266 is a 3.3 V device. Use a voltage divider on the Arduino TX to ESP RX line.
- The Uno has one hardware serial port, so the ESP8266 and GPS share the software serial port. The code switches between them automatically.

## Software setup

1. Install the Arduino IDE.
2. Install these libraries: Adafruit MPU6050, Adafruit Unified Sensor, a `dht11` library, and TinyGPSPlus (Mikal Hart).
3. Open `smart_seat_belt_gps.ino`.
4. Edit the CONFIG section at the top of the file:
   - `WIFI_SSID` and `WIFI_PASS`
   - `TS_API_KEY` (your ThingSpeak channel write key)
   - `EMERGENCY_NUMBER`
   - `LIVE_VIEW_URL` (optional public ThingSpeak channel link)
5. Select Board: Arduino Uno, then upload.

**Never commit real passwords, API keys or phone numbers to this repository.**

## Live location on ThingSpeak

1. In your channel, click Add Widget and choose Map.
2. To share it, go to Channel Settings, then Sharing, and choose "Share channel view with everyone".
3. Only share the link with people who should see the vehicle's location.

## ThingSpeak fields

| Field | Value |
|---|---|
| 1 | Temperature (C) |
| 2 | Humidity (%) |
| 3 | Pulse (BPM) |
| 4 | Vibration detected (0 or 1) |
| 5 | Gyro X (rad/s) |
| lat / long | GPS position (sent when a recent fix exists) |

## Notes and limitations

- The first GPS fix can take several minutes outdoors with a clear view of the sky. It rarely works indoors. Without a fix, the SMS says "Location unavailable" instead of guessing.
- Follow-up SMS messages use SIM credit. Change `TRACK_SMS_COUNT` and `TRACK_SMS_INTERVAL_MS` to adjust.
- The GPS version has been checked with a host-side simulation. Testing on the physical prototype and in a moving vehicle is still to be done.
- The DHT11 measures cabin conditions, not body temperature.
- Crash detection uses the vibration sensor only. Combining it with MPU6050 acceleration would reduce false alarms.
- The Uno has little memory. If the sketch runs out of RAM, use an Arduino Mega or ESP32.

## Future work

- Confirm crashes with MPU6050 acceleration thresholds to reduce false positives.
- Add seat belt buckle detection.
- Add a web dashboard and push notifications.
- Add a battery backup.
- Move to an ESP32 for built-in Wi-Fi and multiple hardware serial ports.

## Repository contents

- `smart_seat_belt_gps.ino` and `smart_seat_belt.ino`: firmware
- `thesis batch 24.docx` and `accidentppt.pptx`: project report and presentation

## Team

Jhansi Singavarapu, Madhu Maheshuni, Aravindh Murkham.
Guide: Mrs. N. Shalini, Assistant Professor.
