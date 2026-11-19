# Duscu-ESP Precompiled Firmware Distribution

Kho chứa chính thức phân phối các bản firmware đã biên dịch sẵn (**precompiled binaries**) cho thiết bị **Duscu-ESP** (ESP32-S3).

> [!NOTE]
> Kho chứa này **chỉ chứa file nhị phân (`.bin`), tệp nén phân phối (`.zip`) và hướng dẫn nạp flash**, tuyệt đối **không chứa mã nguồn (no source code)**.

---

## 📦 Danh sách bo mạch hỗ trợ

| Thiết bị | Vi điều khiển | Flash / PSRAM | Màn hình | Cơ chế điều hướng | File nạp nguyên khối (0x0) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **M5Stack StickS3** | ESP32-S3 | 8MB / 8MB OPI | ST7789v3 (135x240) | Bruce 2-button (M5 & Side) | `m5stack-sticks3/duscu-m5stack-sticks3-merged.bin` |
| **Xingzhi Cube** | ESP32-S3 | 16MB / 8MB OPI | ST7789 (240x240) | 3-button cơ bản | `xingzhi-cube/duscu-xingzhi-cube-merged.bin` |
| **ESP32-S3 Super Mini** | ESP32-S3 | 4MB / Không PSRAM | ST7789 1.47" (320x170) | 3-button (Up:3, Down:2, OK:1) | `s3-super-mini/duscu-s3-super-mini-merged.bin` |

---

## 🚀 Hướng dẫn nạp Firmware (Flashing Guide)

### Cách 1: Nạp file gộp nguyên khối (Khuyên dùng - Nhanh nhất)

Nạp một file duy nhất bắt đầu từ địa chỉ `0x0`.

#### Cho M5Stack StickS3:
```bash
python -m esptool --chip esp32s3 -p <PORT> -b 921600 write_flash 0x0 m5stack-sticks3/duscu-m5stack-sticks3-merged.bin
```

#### Cho Xingzhi Cube:
```bash
python -m esptool --chip esp32s3 -p <PORT> -b 921600 write_flash 0x0 xingzhi-cube/duscu-xingzhi-cube-merged.bin
```

#### Cho ESP32-S3 Super Mini:
```bash
python -m esptool --chip esp32s3 -p <PORT> -b 921600 write_flash 0x0 s3-super-mini/duscu-s3-super-mini-merged.bin
```

---

### Cách 2: Nạp theo từng phân vùng (Giữ nguyên cấu hình NVS)

Nếu bạn muốn giữ lại dữ liệu cấu hình WiFi / thiết lập trong phân vùng NVS:

#### Cho M5Stack StickS3:
```bash
python -m esptool --chip esp32s3 -p <PORT> -b 921600 write_flash \
  0x0 m5stack-sticks3/bootloader.bin \
  0x8000 m5stack-sticks3/partitions.bin \
  0x10000 m5stack-sticks3/firmware.bin
```

#### Cho Xingzhi Cube:
```bash
python -m esptool --chip esp32s3 -p <PORT> -b 921600 write_flash \
  0x0 xingzhi-cube/bootloader.bin \
  0x8000 xingzhi-cube/partitions.bin \
  0x10000 xingzhi-cube/firmware.bin
```

#### Cho ESP32-S3 Super Mini:
```bash
python -m esptool --chip esp32s3 -p <PORT> -b 921600 write_flash \
  0x0 s3-super-mini/bootloader.bin \
  0x8000 s3-super-mini/partitions.bin \
  0x10000 s3-super-mini/firmware.bin
```

---

### Cách 3: Nạp trực tiếp qua Web Flasher (Trình duyệt Chrome / Edge)

1. Mở trình duyệt hỗ trợ Web Serial (Google Chrome hoặc MS Edge).
2. Kết nối ESP32-S3 với máy tính qua cáp Type-C chất lượng tốt (hỗ trợ Data).
3. Chọn chip `ESP32-S3`, chọn baud rate `921600`.
4. Tải file `duscu-m5stack-sticks3-merged.bin` hoặc `duscu-xingzhi-cube-merged.bin` và đặt offset là `0x0000`.
5. Nhấn **Program / Flash**.

---

## 🎮 Cơ chế điều hướng trên M5Stick S3 (Bruce 2-Button Model)

Firmware M5Stick S3 sử dụng mô hình điều hướng 2 nút bấm tương thích 100% với Bruce:

* **Nút M5 Mặt trước (Btn 1 / GPIO 11 / SEL_BTN)**:
  - **Nhấn 1 lần (Click)**: `Next` (Di chuyển đến mục tiếp theo).
  - **Nhấp đúp (Double Click)** hoặc **Nhấn giữ $\ge 500\,\text{ms}$**: `Select` (Kích hoạt / Chọn mục).
* **Nút Cạnh bên (Btn 2 / GPIO 12 / DW_BTN)**:
  - **Nhấn 1 lần (Click)**: `Previous` (Quay lại mục trước).
  - **Nhấp đúp (Double Click)** hoặc **Nhấn giữ $\ge 500\,\text{ms}$**: `Back` (Thoát ra màn hình trước).
* **Nhấn giữ đồng thời cả 2 nút**: `Combo / Alternate` (Phím chức năng phụ).

---

## 🛠 Sơ đồ chân phần cứng M5Stick S3

* **Màn hình ST7789v3**: CS: `41` | DC: `45` | RST: `21` | MOSI: `39` | CLK: `40` | BL: `38`
* **Nguồn & Quản lý pin**: PMIC M5PM1 (`0x6E`) qua I2C SDA: `47`, SCL: `48`
* **Âm thanh (Speaker/Mic)**: ES8311 Codec (`0x18`) qua I2S BCLK: `17`, WS: `15`, DOUT: `14`, MCLK: `18`, DIN: `16`
* **Hồng ngoại (Infrared)**: RMT Channel TX: `46`, RX: `42`
* **Bus SPI ngoại vi**: SCK: `5`, MISO: `4`, MOSI: `6` | SD CS: `7`, CC1101 CS: `2`, GDO0: `3`, nRF24 CS: `8`, CE: `1`
* **Giao tiếp đồng xử lý BW16**: UART TX: `43`, RX: `44`
* **Cổng mở rộng Grove**: SDA: `9`, SCL: `10`

---

## 🛠 Sơ đồ chân phần cứng ESP32-S3 Super Mini (Màn hình 1.47")

* **Màn hình ST7789 1.47" (320x170)**: CS: `44` | DC: `13` | RST: `12` | MOSI: `11` | CLK: `10` | BL: `43` (Offset X: `35`, Y: `0`)
* **Nút bấm điều hướng (3 nút vật lý)**:
  - **Up / Previous**: GPIO `3`
  - **Down / Next**: GPIO `2`
  - **Select / OK**: GPIO `1`
* **Hồng ngoại (Infrared)**: TX: `14`, RX: `15`
* **Bus SPI ngoại vi**: MOSI: `11`, MISO: `8`, SCK: `10` | SD CS: `7`, CC1101 CS: `17`, GDO0: `16`, nRF24 CS: `18`, CE: `21`
* **Giao tiếp đồng xử lý BW16**: UART TX: `4`, RX: `5`
