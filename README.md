# HP-du-15-corei3
core i3
ram 12gb
ssd 256 gb
cài hackiosh 13.8
NHẬT KÝ CẤU HÌNH PHẦN CỨNG & HACKINTOSH: HP 15S-DU (COMET LAKE)

Tài liệu này lưu trữ toàn bộ thông số kỹ thuật phần cứng thực tế và cấu trúc EFI (OpenCore) đã được tối ưu hóa cho chiếc Laptop HP 15s-du chạy chip Intel Core thế hệ 10 (Comet Lake).

1. THÔNG SỐ PHẦN CỨNG GỐC (HARDWARE SPECS)

Linh kiện

Thông số chi tiết

Trạng thái Hackintosh

Ghi chú kỹ thuật

CPU

Intel Core i3/i5/i7 thế hệ 10 (Comet Lake-U)

Tương thích hoàn hảo

Hỗ trợ quản lý điện năng gốc qua kext SMCProcessor và SSDT-PLUG.

iGPU

Intel UHD Graphics 620 / 630 (Comet Lake)

Tương thích hoàn hảo

• Platform-ID: 0x3E9B0000 (Base64: AACbPg==) 



• Device-ID: 0x3E9B0000 (Base64: mz4AAA==) 



• Boot-arg cài đặt: -igfxvesa (tắt gia tốc đồ họa tạm thời để vượt khóa DVMT BIOS 32MB).

RAM

DDR4 2666MHz (Hỗ trợ 2 khe nâng cấp)

Tương thích hoàn hảo

Nhận diện băng thông gốc đầy đủ.

SSD gốc

Kioxia AGH4101 (Mã OEM của dòng BG4/BG5)

🔴 KHÔNG TƯƠNG THÍCH

Xung đột sâu với IONVMeFamily của Apple, gây treo nhân và đứng hình ở bộ cài. Đã tháo bỏ.

SSD nâng cấp

SK Hynix SC401 SATA 256GB

🟢 Tương thích hoàn hảo

Giao thức SATA cực lành tính. Nhận diện trực tiếp qua driver AppleAHCIPort của Apple.

Audio

Realtek ALC236

Tương thích tốt

Sử dụng AppleALC.kext với layout-id là 13 (hoặc 3, 11, 14, 18).

Touchpad / Bàn phím

Bàn phím chuẩn PS2 & Synaptics/ELAN Precision Touchpad

Tương thích tốt

Nhận diện cử chỉ đa điểm (Gestures) mượt mà sau khi nạp đầy đủ các PlugIns con của VoodooPS2Controller.

2. CẤU TRÚC ACPI & VÁ LỖI PHẦN CỨNG (EFI/OC/ACPI)

Để vượt qua các cơ chế quản lý phần cứng nghiêm ngặt của BIOS HP, bộ EFI bắt buộc phải nạp đầy đủ 4 file bảng vá ACPI đã biên dịch (.aml) sau đây:

SSDT-EC-USBX.aml:

Nhiệm vụ: Khắc phục lỗi đứng hình ApplePS2Controller và lỗi sập nguồn liên quan đến bộ điều khiển nhúng năng lượng (EC - Embedded Controller) trên bo mạch chủ HP. Đồng thời cấu hình lại dòng điện USB để sạc thiết bị ngoại vi ổn định.

SSDT-AWAC.aml:

Nhiệm vụ: Kích hoạt lại đồng hồ thời gian thực (RTC) truyền thống trên dòng chip thế hệ 10 thay thế cho cấu trúc AWAC mới bị macOS từ chối. Sửa dứt điểm lỗi treo đồng hồ hệ thống lúc boot.

SSDT-PLUG.aml:

Nhiệm vụ: Cho phép macOS tự điều khiển tần số và điện áp của CPU Intel thế hệ 10 một cách tự nhiên (Native CPU Power Management), giúp laptop chạy mát và tiết kiệm pin tối đa.

SSDT-PNLF.aml:

Nhiệm vụ: Kích hoạt thanh trượt điều chỉnh độ sáng màn hình laptop mượt mà trong mục Settings của macOS.

3. DANH SÁCH DRIVER KEXT TƯƠNG THÍCH (EFI/OC/Kexts)

Bộ nạp kext được sắp xếp theo đúng thứ tự ưu tiên trong config.plist để tránh lỗi xung đột nạp chồng:

Lilu.kext: Lõi vá lỗi hệ thống cơ bản (Bắt buộc nạp đầu tiên).

VirtualSMC.kext: Giả lập chip SMC của Apple (Bắt buộc).

WhateverGreen.kext: Sửa lỗi hiển thị và cấu hình cổng xuất hình cho card đồ họa Intel UHD.

AppleALC.kext: Sửa lỗi nhận diện và phát âm thanh cho chip Realtek ALC236.

CtlnaAHCIPort.kext: Ép nhân macOS Ventura nhận diện cổng điều khiển SATA đời cũ của HP để làm nổi ổ cứng SK Hynix.

VoodooPS2Controller.kext: Kext tổng quản lý cổng kết nối PS2.

VoodooInput.kext (PlugIn): Bộ xử lý cử chỉ tay.

VoodooPS2Keyboard.kext (PlugIn): Kích hoạt bàn phím laptop.

VoodooPS2Mouse.kext (PlugIn): Kích hoạt chuột phụ trợ.

VoodooPS2Trackpad.kext (PlugIn): Kích hoạt bàn di chuột cảm ứng mượt mà.

SMCBatteryManager.kext: Đọc phần trăm pin và chu kỳ sạc của laptop HP.

4. CÁC THIẾT LẬP BIOS CHUẨN CHO HP LAPTOP (BIOS SETTINGS)

Để bảo đảm hệ thống khởi động OpenCore mượt mà, BIOS của HP cần được thiết lập chính xác như sau:

SATA Mode / Storage Options: Chuyển từ Intel RST Premium with Intel Optane sang AHCI (Bắt buộc - Nếu không máy sẽ ẩn toàn bộ ổ cứng).

Secure Boot: Disabled (Tắt đi để cho phép cài hệ điều hành ngoài Windows).

Virtualization Technology (VT-x): Enabled (Bật).

VT-d: Disabled (Hoặc bật nhưng phải có cờ DisableIoMapper=True trong config.plist).

Fast Boot: Disabled (Tắt để tránh bỏ qua trình nạp USB OpenCore).
