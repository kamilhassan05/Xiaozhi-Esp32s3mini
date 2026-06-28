# Modified Xiaozhi AI Assistant

> AI voice assistant built on ESP32-S3 Super Mini Zero · Powered by Xiaozhi AI
> I built this for the ESP32-S3 Super Mini Zero

> (4 MB flash, 2 MB PSRAM). It runs faster, gives more accurate results, and performs better than the other models I've tested.

> I also managed to reduce the overall size of the assistant, and added cute animated eyes that come to life while it's listening and speaking. mode.

---

## Hardware Required

| Component | Spec | Qty |
|-----------|------|-----|
| ESP32-S3 Super Mini Zero | 4MB flash, 2MB PSRAM | 1 |
| INMP441 Microphone | I2S digital mic module | 1 |
| MAX98357A Amplifier | I2S Class D amp | 1 |
| Speaker | 3W 4ohm recommended | 1 |
| OLED Display | SSD1306 128x64 I2C | 1 |
| Push buttons | Momentary, to GND | 4 |
| 1-channel relay module | 5V trigger | 1 |
| Breadboard / Perfboard | For connections | 1 |

---

## Wiring Connections

### INMP441 Microphone → ESP32-S3

> ⚠️ L/R pin must be connected to GND — if left floating, mic will behave unstably.

| INMP441 Pin | Also Called | Connect To |
|-------------|-------------|------------|
| VDD | VCC / 3V3 | 3.3V |
| GND | GND | GND |
| WS | LRCK / LR | GPIO 4 |
| SCK | BCLK / CLK | GPIO 5 |
| SD | DIN / DATA / DOUT | GPIO 6 |
| L/R | Channel select | GND (left channel) |

---

### MAX98357A Amplifier → ESP32-S3

> ⚠️ VIN must be 5V — using 3.3V causes distorted/broken audio.

| MAX98357A Pin | Also Called | Connect To |
|---------------|-------------|------------|
| VIN | VCC / 5V / PWR | 5V |
| GND | GND | GND |
| DIN | DATA / SD / SDIN | GPIO 1 |
| BCLK | CLK / SCK | GPIO 2 |
| LRC | WS / LRCLK | GPIO 3 |
| SD | SHDN / EN / ENABLE | 3.3V (always ON) |
| OUT+ / OUT- | Speaker+ / Speaker- | Speaker terminals |

---

### OLED Display (I2C) → ESP32-S3

| OLED Pin | Also Called | Connect To |
|----------|-------------|------------|
| VCC | VDD / 3V3 | 3.3V |
| GND | GND | GND |
| SDA | DATA / DAT | GPIO 8 |
| SCL | CLK / SCK | GPIO 9 |

---

### Relay Module (Lamp Control) → ESP32-S3

| Relay Pin | Also Called | Connect To |
|-----------|-------------|------------|
| VCC | VIN / 5V | 5V |
| GND | GND | GND |
| IN | CH1 / S / SIG | GPIO 12 |

---

### Buttons → ESP32-S3 (each wired between GPIO and GND)

| Button | GPIO | Function |
|--------|------|----------|
| Boot / Talk | GPIO 0 | Start / stop conversation |
| Touch | GPIO 10 | Hold to talk |
| Volume Up | GPIO 40 | Increase volume by 10% |
| Volume Down | GPIO 39 | Decrease volume by 10% |

---

## Key Config Files

### `main/boards/bread_compact_wifi/config.h`

```cpp
#define AUDIO_INPUT_SAMPLE_RATE  16000
#define AUDIO_OUTPUT_SAMPLE_RATE 24000

#define AUDIO_I2S_METHOD_SIMPLEX

#define AUDIO_I2S_MIC_GPIO_WS   GPIO_NUM_4
#define AUDIO_I2S_MIC_GPIO_SCK  GPIO_NUM_5
#define AUDIO_I2S_MIC_GPIO_DIN  GPIO_NUM_6

#define AUDIO_I2S_SPK_GPIO_DOUT GPIO_NUM_1
#define AUDIO_I2S_SPK_GPIO_BCLK GPIO_NUM_2
#define AUDIO_I2S_SPK_GPIO_LRCK GPIO_NUM_3

#define BUILTIN_LED_GPIO        GPIO_NUM_48
#define BOOT_BUTTON_GPIO        GPIO_NUM_0
#define TOUCH_BUTTON_GPIO       GPIO_NUM_10
#define VOLUME_UP_BUTTON_GPIO   GPIO_NUM_40
#define VOLUME_DOWN_BUTTON_GPIO GPIO_NUM_39

#define DISPLAY_SDA_PIN GPIO_NUM_8
#define DISPLAY_SCL_PIN GPIO_NUM_9
#define DISPLAY_WIDTH   128
#define DISPLAY_HEIGHT  32

#define LAMP_GPIO GPIO_NUM_12
```

---

### `partitions.csv`

```
# Name,   Type, SubType, Offset,   Size,  Flags
nvs,      data, nvs,     0x9000,   0x5000,
otadata,  data, ota,     0xe000,   0x2000,
factory,  app,  factory, 0x10000,  0x2F0000,
storage,  data, spiffs,  0x300000, 0x0,
```

---

### `config.json`

```json
{
    "target": "esp32s3",
    "builds": [
        {
            "name": "Kamil-Hassan",
            "sdkconfig_append": [
                "CONFIG_OLED_SSD1306_128X64=y"
            ]
        }
    ]
}
```

---

## Flashing Firmware

Use **ESP Flash Download Tool** with settings: ESP32-S3, 4MB, DIO, 80MHz

| File | Address |
|------|---------|
| bootloader.bin | 0x0 |
| partition-table.bin | 0x8000 |
| ota_data_initial.bin | 0xd000 |
| xiaozhi.bin | 0x10000 |
| generated_assets.bin | 0x300000 |

All files are located in: `C:\Users\YourName\Desktop\xiaozhi-esp32\build\`

---

## First Time WiFi Setup

1. Power on the device — it creates a hotspot named **Xiaozhi-XXXXXX**
2. Connect your phone to that hotspot
3. Open browser and go to `http://192.168.4.1`
4. Select your home WiFi and enter password
5. Device connects and shows an **activation code**
6. Go to `https://xiaozhi.me` → create account → enter the activation code
7. Device activates and enters standby — ready to use!

---

## Using the Assistant

### Button Controls

| Action | Button |
|--------|--------|
| Start / stop talking | Boot button (GPIO 0) |
| Hold to talk | Touch button (GPIO 10) |
| Volume up | Volume Up (GPIO 40) |
| Volume down | Volume Down (GPIO 39) |
| Re-enter WiFi setup | Hold Boot at startup |

### Voice Commands — Lamp (Relay GPIO 12)

| Say | Result |
|-----|--------|
| "Turn on the lamp" | Relay ON |
| "Turn off the lamp" | Relay OFF |
| "Switch on the lamp" | Relay ON |
| "Switch off the lamp" | Relay OFF |

---

## Configuring AI Personality

> Changes on xiaozhi.me apply instantly — no reflashing needed.

1. Log in to `https://xiaozhi.me`
2. Go to your device → Settings
3. Set system prompt, voice, and language

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| No WiFi hotspot | Check power, replug USB cable |
| Distorted / broken audio | MAX98357A VIN must be 5V not 3.3V |
| Very low volume | Use 3W 4ohm speaker minimum |
| Mic not working | Connect L/R pin to GND |
| Device crashes after talking | Check all GND connections are solid |
| OLED not working | Verify SDA=GPIO 8, SCL=GPIO 9 |
| Relay not responding | Verify IN pin=GPIO 12, VCC=5V |

---

## Built With

- [Xiaozhi ESP32](https://github.com/78/xiaozhi-esp32)
- ESP-IDF v5.5.4
- ESP32-S3 Super Mini Zero

---

*Built by Kamil Hassan · Quetta, Pakistan*
