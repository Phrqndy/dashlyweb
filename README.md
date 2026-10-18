<div align="center">

# 🎛️ Dashy Lite

**A VESC dashboard that runs in your browser, with nothing to install.**

<a href="https://dashy.kugonizer.workers.dev/">
  <img src="https://img.shields.io/badge/▶%20Open%20Dashy%20Lite-dashy.kugonizer.workers.dev-000000?style=for-the-badge&labelColor=34C759" alt="Open Dashy Lite" height="40">
</a>

Live speed and temperatures from your VESC over BLE, in one HTML file. (BLE is so cool + its lightweight and kinda easy to use as variable is html/JS)

![Platform](https://img.shields.io/badge/platform-iOS%20·%20Android%20·%20Desktop-000000?style=for-the-badge)
![Web Bluetooth](https://img.shields.io/badge/Web%20Bluetooth-BLE%20UART-0082FC?style=for-the-badge&logo=bluetooth&logoColor=white)
![Size](https://img.shields.io/badge/size-~22%20KB-34C759?style=for-the-badge)
![Mode](https://img.shields.io/badge/telemetry-read--only-FF9500?style=for-the-badge)

</div>

---

## ✨ What it does

|  |  |
|---|---|
| 🏁 **Big speed display** | Large numbers you can read at a glance, refreshed 10× a second |
| 🌡️ **Temperatures** | Controller (MOSFET) and motor temperatures, refreshed once a second |
| 🧭 **Auto speed** | Uses the wheel and gearing setup already saved in your VESC |
| 🔧 **Manual speed** | Enter the wheel circumference, pole pairs and gear ratio yourself |
| 🌗 **Themes** | Auto, Light or Dark |
| 📏 **Units** | km/h or mph |
| 🔁 **Auto-reconnect** | Reconnects by itself if the Bluetooth link drops mid-ride |
| 🔒 **Read-only** | Only asks the controller for data and never changes its settings |

---

## 🚀 Quick start

1. **Host the folder over HTTPS.** Web Bluetooth only works on secure pages; `localhost` also works for testing.
2. **Open it in a Web Bluetooth browser:**
   - **iPhone / iPad:** [Bluefy](https://apps.apple.com/app/bluefy-web-ble-browser/id1492822055) (Safari has no Web Bluetooth)
   - **Android / Desktop:** Chrome or Edge
3. Tap **Connect → Scan for VESC BLE** and choose your adapter.
4. Ride. 🛹

> [!TIP]
> In Bluefy, use **Add to Home Screen** to open Dashy Lite full-screen, like an app.

### Test it locally

```bash
npx serve .
```

---

## 🛰️ How it talks to your VESC

Dashy Lite speaks the same protocol as **VESC Tool**, over the Nordic UART Service that VESC BLE modules expose.

| | UUID |
|---|---|
| Service | `6e400001-b5a3-f393-e0a9-e50e24dcca9e` |
| Write (RX) | `6e400002-b5a3-f393-e0a9-e50e24dcca9e` |
| Notify (TX) | `6e400003-b5a3-f393-e0a9-e50e24dcca9e` |

```
 Phone                                   VESC
   │ ── COMM_FW_VERSION (0) ─────────────▶ │   once, on connect
   │ ◀──────────── model + firmware ────── │
   │                                       │
   │ ── GET_VALUES_SETUP_SELECTIVE (51) ─▶ │   10 Hz · mask bit 6 = speed
   │ ◀────────────────── speed (mm/s) ──── │
   │                                       │
   │ ── COMM_GET_VALUES (4) ─────────────▶ │   1 Hz
   │ ◀──────── temps · ERPM · voltage ──── │
```

- **Framing:** `0x02 len payload crc16 0x03`, with CRC16-XMODEM (poly `0x1021`, init `0`)
- **Writes:** sent in 20-byte chunks without response, like VESC Tool's `bleuart.cpp`, falling back to write-with-response if needed

---

## 🔧 Manual speed formula

If your VESC has no wheel setup saved, switch to **Manual** in the Connection dialog:

```
km/h = |ERPM| × wheel_circumference_mm × 0.00006 ÷ (pole_pairs × gear_ratio)
```

Use `1` as the gear ratio for hub motors and direct drive. Settings are saved on your device.

---

## 📁 Files

```
webapp/
├── index.html          the whole app: markup, styles and BLE logic
└── (N/A)
```

---

## 🩺 Troubleshooting

| Problem | Fix |
|---|---|
| "Web Bluetooth unavailable" | Open the page in Bluefy (iOS) or Chrome (Android/desktop), over HTTPS |
| "Does not expose the VESC BLE UART service" | Pick the VESC BLE adapter in the list, not another device |
| Connected but speed shows **—** | Your VESC has no wheel setup; switch to **Manual** speed |
| Keeps dropping | Move closer; Dashy Lite reconnects by itself |

---

<div align="center">

**Dashy Lite** · the web version of Dashy · not affiliated with the VESC Project!!!

</div>
