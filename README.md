# HƯỚNG DẪN DỰ ÁN: SMART PET FEEDING SYSTEM USING ESP32

Dự án này là hệ thống cho thú cưng ăn thông minh dùng ESP32, cho phép tự động cho ăn theo giờ hẹn trước, tự động bơm nước khi cạn, đồng thời theo dõi và điều khiển từ xa qua ứng dụng di động Blynk.

---

## 1. Danh sách thiết bị và chân kết nối (Wiring Guide)

Hãy kết nối các linh kiện với bo mạch ESP32 theo bảng dưới đây:

| Tên thiết bị / Cảm biến | Chân linh kiện | Chân nối trên ESP32 | Ghi chú |
| :--- | :--- | :--- | :--- |
| **Màn hình OLED SSD1306** | VCC <br> GND <br> SDA <br> SCL | 3.3V / 5V <br> GND <br> **GPIO 21** <br> **GPIO 22** | Kết nối I2C |
| **Cảm biến RTC DS1307** | VCC <br> GND <br> SDA <br> SCL | 5V <br> GND <br> **GPIO 21** <br> **GPIO 22** | Kết nối I2C (Chung dây SDA/SCL với OLED) |
| **Load Cell + HX711** | VCC <br> GND <br> DT (DOUT) <br> SCK | 5V <br> GND <br> **GPIO 19** <br> **GPIO 18** | Đo khối lượng thức ăn |
| **Water Level Sensor** | + (VCC) <br> - (GND) <br> S (Signal) | 5V <br> GND <br> **GPIO 34 (ADC1_CH6)** | Cảm biến mực nước Analog |
| **Động cơ Servo SG90** | Dây Đỏ (VCC) <br> Dây Nâu (GND) <br> Dây Cam (Signal) | 5V <br> GND <br> **GPIO 13 (PWM)** | Động cơ điều khiển nắp xả |
| **Máy bơm nước Mini 5V** | Chân VCC / GND | Nối qua **Module Relay** | Bơm nước tự động |
| **Module Relay 5V** | VCC <br> GND <br> IN (Tín hiệu) | 5V <br> GND <br> **GPIO 14** | Điều khiển bật/tắt máy bơm |
| **Còi báo Buzzer (Active)**| Chân + <br> Chân - | **GPIO 12** <br> GND | Còi báo động |

---

## 2. Hướng dẫn cài đặt thư viện trên Arduino IDE

Để nạp code thành công, bạn cần cài đặt các thư viện sau trên phần mềm **Arduino IDE**:

1. Mở Arduino IDE, chọn **Tools** -> **Manage Libraries...**
2. Tìm kiếm và cài đặt các thư viện sau (chọn phiên bản mới nhất):
   * **Blynk** (bởi Volodymyr Shymanskyy)
   * **Adafruit SSD1306** (bởi Adafruit)
   * **Adafruit GFX Library** (bởi Adafruit)
   * **RTClib** (bởi Adafruit)
   * **HX711 Arduino Library** (bởi Bogdan Necula)
   * **ESP32Servo** (bởi Kevin Harrington)

---

## 3. Hướng dẫn cấu hình Blynk IoT (Mobile App)

Hệ thống sử dụng Blynk IoT phiên bản mới. Hãy cấu hình bảng điều khiển (Dashboard) trên Blynk như sau:

### Tạo các Datastreams (Virtual Pins):
1. **V1 (Virtual Pin 1)**: Kiểu dữ liệu `Double` hoặc `Float`, tên: `Food Weight` (Trọng lượng đồ ăn) - Dùng hiển thị số (Value Display).
2. **V2 (Virtual Pin 2)**: Kiểu dữ liệu `Integer`, tên: `Water Status` (Trạng thái nước). 1 là Bình thường, 0 là Cạn - Dùng hiển thị đèn LED ảo hoặc chữ.
3. **V3 (Virtual Pin 3)**: Kiểu dữ liệu `Integer`, tên: `Manual Feed` (Cho ăn thủ công) - Dùng nút nhấn (Button Widget), chế độ Push (khi nhấn gửi giá trị 1).
4. **V4 (Virtual Pin 4)**: Kiểu dữ liệu `Integer`, tên: `Manual Pump` (Bơm nước thủ công) - Dùng nút nhấn (Button Widget) hoặc công tắc (Switch Widget).
5. **V5 (Virtual Pin 5)**: Kiểu dữ liệu `Integer` (Min: 0, Max: 23), tên: `Set Hour` (Cài giờ ăn) - Dùng thanh trượt (Slider Widget) hoặc nhập số.
6. **V6 (Virtual Pin 6)**: Kiểu dữ liệu `Integer` (Min: 0, Max: 59), tên: `Set Minute` (Cài phút ăn) - Dùng thanh trượt (Slider Widget) hoặc nhập số.
7. **V7 (Virtual Pin 7)**: Kiểu dữ liệu `String`, tên: `System Status` (Trạng thái hệ thống) - Dùng ô hiển thị chữ.

Sau khi tạo xong thiết bị, hãy copy **Template ID**, **Template Name**, và **Auth Token** dán vào phần đầu file [config.h]

---

## 4. Hướng dẫn nạp code và chạy hệ thống

1. Kết nối bo mạch ESP32 với máy tính qua cáp Micro-USB hoặc Type-C.
2. Mở file [SmartPetFeeder.ino]bằng phần mềm **Arduino IDE**.
3. Sửa thông tin WiFi (`WIFI_SSID` và `WIFI_PASS`) và mã Blynk Token trong file [config.h].
4. Trong Arduino IDE, chọn đúng bo mạch: **Tools** -> **Board** -> **ESP32 Arduino** -> **ESP32 Dev Module** (hoặc loại bo mạch tương ứng bạn đang dùng).
5. Chọn đúng cổng COM kết nối: **Tools** -> **Port**.
6. Nhấn nút **Upload** (mũi tên hướng sang phải) để biên dịch và nạp code vào mạch.
7. Sau khi nạp thành công, hãy mở **Serial Monitor** với tốc độ baud `115200` để xem nhật ký hoạt động của hệ thống.
