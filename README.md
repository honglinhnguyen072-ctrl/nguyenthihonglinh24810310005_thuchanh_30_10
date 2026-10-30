# BÀI TẬP 5.3 – HỆ THỐNG QUẢN LÝ QUÁN CAFE & BÁN HÀNG (POS CAFE SYSTEM)

Ứng dụng WinForms (C#, .NET Framework 4.8) kết nối SQL Server bằng ADO.NET, xây dựng theo kiến trúc 3 lớp **DTO – DAL – BUS – GUI**.

## MỤC 1: THÔNG TIN SINH VIÊN

- **Họ và tên sinh viên:** Nguyễn Thị Hồng Linh
- **Mã sinh viên:** 24810310005
- **Lớp:** D19QTANM1
- **Tên Đề tài / Bài tập:** Bài 5.3 – Hệ thống quản lý quán Cafe & bán hàng (POS)

## MỤC 2: HƯỚNG DẪN CÀI ĐẶT & CHẠY DỰ ÁN

**Yêu cầu:** Visual Studio 2022 (workload *.NET desktop development*, đã kèm SQL Server Express LocalDB).

1. Mở file `CafePOS.sln` bằng Visual Studio 2022.
2. Build & Run dự án (F5). Ở lần chạy đầu, chương trình tự kiểm tra: nếu chưa có CSDL `CafePOSDB` thì tự chạy file `Database/ScriptDB.sql` để tạo bảng và dữ liệu mẫu (lớp `DAL/DbInit.cs`).
3. Mặc định chuỗi kết nối dùng `(localdb)\MSSQLLocalDB`. Nếu muốn dùng SQL Server khác, sửa `Data Source` trong `CafePOS/App.config` (ví dụ `.\SQLEXPRESS`).
4. Có thể chạy thủ công `Database/ScriptDB.sql` bằng SQL Server Management Studio (SSMS) nếu muốn tạo lại CSDL từ đầu.

## Cấu trúc thư mục

```
├── Database/
│   └── ScriptDB.sql            # Tạo CSDL CafePOSDB (5 bảng) + dữ liệu mẫu
├── CafePOS/
│   ├── DTO/                    # BanAnDTO, DanhMucDTO, MonAnDTO, HoaDonDTO
│   ├── DAL/                    # DatabaseHelper, BanAnDAL, DanhMucDAL, MonAnDAL, HoaDonDAL
│   ├── BUS/                    # BanAnBUS, DanhMucBUS, MonAnBUS, HoaDonBUS (validation nghiệp vụ)
│   ├── GUI/                    # FrmMain, FrmQuanLyThucDon, FrmBaoCao
│   ├── App.config              # Chuỗi kết nối
│   └── Program.cs
├── images/                     # Ảnh chụp màn hình kết quả
├── README.md
└── CafePOS.sln
```

## Chức năng đã thực hiện

| Yêu cầu đề bài | Cách thực hiện |
|---|---|
| Mô hình ngắt kết nối nạp menu | `MonAnDAL.LoadMenuDataSet()` dùng **SqlDataAdapter** đổ 2 bảng DanhMuc, MonAn vào một **DataSet** (có DataRelation); khi đổi danh mục chỉ lọc bằng **DataView.RowFilter**, không truy vấn lại CSDL |
| Sơ đồ bàn | Danh sách **Button tạo động** theo bảng BanAn; bàn *Trống* màu xanh, *Có khách* màu đỏ, bàn đang chọn có viền vàng |
| Gọi món | Chọn bàn → nạp các món đã gọi vào **DataGridView** bằng SqlDataAdapter + DataTable; chọn danh mục, món, số lượng → *Thêm món*; bàn tự chuyển sang *Có khách*. Có thêm chức năng *Bớt món* |
| Đổi bàn | Chuyển hóa đơn đang mở sang bàn trống khác (trong SqlTransaction) |
| Thanh toán | `HoaDonDAL.ThanhToan()`: tính tổng tiền → cập nhật GioRa, TongTien, TrangThai = 1 → trả bàn về *Trống*; **tất cả nằm trong một SqlTransaction**, lỗi thì Rollback |
| Quản lý thực đơn (3-Tier CRUD) | `FrmQuanLyThucDon`: Thêm / Sửa / Xóa / Tìm kiếm món và danh mục. Lớp BUS chặn **tên món để trống** và **đơn giá âm** |
| Báo cáo | `FrmBaoCao`: hóa đơn đã thanh toán theo khoảng ngày, Top 5 món bán chạy, tổng doanh thu tính bằng **ExecuteScalar** |
| Chống SQL Injection | Mọi câu lệnh đều dùng **SqlParameter** |

## MỤC 3: HÌNH ẢNH KẾT QUẢ CÁC MÀN HÌNH CHỨC NĂNG

1. Màn hình Chính / Tổng quan (sơ đồ bàn):
 <img width="432" height="230" alt="Screenshot 2026-10-30 191929" src="https://github.com/user-attachments/assets/d98dc429-ba94-4d27-8394-8f6f3209dd35" />

2. Màn hình Quản lý Danh mục / Thêm Sửa Xóa (CRUD):
  <img width="455" height="245" alt="Screenshot 2026-10-30 191958" src="https://github.com/user-attachments/assets/03f13971-d43e-4b2b-b17b-d405fc1b7ecb" />

3. Màn hình Thực thi Nghiệp vụ chính (Gọi món / Thanh toán):
  <img width="722" height="382" alt="Screenshot 2026-10-30 192016" src="https://github.com/user-attachments/assets/a088a7eb-baf4-4b20-ac8e-f292cf584651" />

4. Màn hình Báo cáo - Thống kê:
  <img width="722" height="303" alt="Screenshot 2026-10-30 192033" src="https://github.com/user-attachments/assets/9dbbc1b0-d0f0-4e1a-afe4-97ce690d812c" />

5. Minh họa xử lý lỗi (nhập đơn giá âm / tên món trống bị chặn):
 <img width="530" height="280" alt="Screenshot 2026-10-30 192046" src="https://github.com/user-attachments/assets/92e2cbb6-fae1-4362-a16f-35d884c2f325" />
# BÀI TẬP 5.4 – HỆ THỐNG QUẢN LÝ TIỆN ÍCH DỊCH VỤ & KHÁCH SẠN (HOTEL MANAGEMENT SYSTEM)

Ứng dụng WinForms (C#, .NET Framework 4.8) kết nối SQL Server bằng ADO.NET, kiến trúc 3 lớp **DTO – DAL – BUS – GUI**.

## MỤC 1: THÔNG TIN SINH VIÊN

- **Họ và tên sinh viên:** nguyễn Thị Hồng Linh
- **Mã sinh viên:** 248103110005
- **Lớp:** D19QTANM1
- **Tên Đề tài / Bài tập:** Bài 5.4 – Hệ thống quản lý tiện ích dịch vụ & khách sạn

## MỤC 2: HƯỚNG DẪN CÀI ĐẶT & CHẠY DỰ ÁN

**Yêu cầu:** Visual Studio 2022 (workload *.NET desktop development*, đã kèm SQL Server Express LocalDB).

1. Mở file `HotelManagement.sln` bằng Visual Studio 2022.
2. Build & Run dự án (F5). Ở lần chạy đầu, chương trình tự kiểm tra: nếu chưa có CSDL `HotelDB` thì tự chạy file `Database/ScriptDB.sql` để tạo bảng và dữ liệu mẫu (lớp `DAL/DbInit.cs`).
3. Mặc định chuỗi kết nối dùng `(localdb)\MSSQLLocalDB`. Nếu muốn dùng SQL Server khác, sửa `Data Source` trong `HotelManagement/App.config` (ví dụ `.\SQLEXPRESS`).
4. Có thể chạy thủ công `Database/ScriptDB.sql` bằng SQL Server Management Studio (SSMS) nếu muốn tạo lại CSDL từ đầu.

## Cấu trúc thư mục

```
├── Database/
│   └── ScriptDB.sql              # Tạo CSDL HotelDB (5 bảng) + dữ liệu mẫu
├── HotelManagement/
│   ├── DTO/                      # PhongDTO, KhachHangDTO, DichVuDTO (ServiceDTO), PhieuDatPhongDTO,
│   │                             # SuDungDichVuDTO, HoaDonThanhToanDTO
│   ├── DAL/                      # DatabaseHelper, PhongDAL, KhachHangDAL, DichVuDAL, PhieuDatPhongDAL
│   ├── BUS/                      # PhongBUS, KhachHangBUS, DichVuBUS, DatPhongBUS
│   ├── GUI/                      # FrmMain, FrmQuanLy, FrmBaoCao, UiHelper
│   ├── App.config
│   └── Program.cs
├── images/
├── README.md
└── HotelManagement.sln
```

## Chức năng đã thực hiện

| Yêu cầu đề bài | Cách thực hiện |
|---|---|
| Check-in | Tra cứu khách theo **CCCD bằng SqlParameter**. Khách mới thì nhập họ tên, SĐT. Chọn phòng trống → `PhieuDatPhongDAL.CheckIn()` trong **một SqlTransaction**: thêm khách (nếu mới) → lập phiếu → phòng sang *Đang ở* (có khóa `UPDLOCK` để 2 người không nhận trùng phòng) |
| Gọi dịch vụ | Chọn phòng *Đang ở* → chọn dịch vụ + số lượng; dữ liệu đi qua **SuDungDichVuDTO / DichVuDTO** từ GUI → BUS → DAL |
| Check-out & hóa đơn | Tổng tiền = **(số giờ ở × giá theo giờ) + tổng tiền dịch vụ**, số giờ làm tròn lên (tối thiểu 1 giờ). Xác nhận → `CheckOut()` trong **SqlTransaction**: lưu TongTienThanhToan, NgayTraPhong → phòng sang *Đang dọn dẹp*. Nút *Dọn xong* trả phòng về *Trống* |
| Lọc & tìm phòng ngắt kết nối | Danh sách phòng nạp 1 lần vào **DataTable**, lọc theo Loại phòng / Trạng thái / Số phòng bằng **DataView.RowFilter** ngay trên giao diện, không truy vấn lại CSDL. Các dòng tô màu theo trạng thái |
| CRUD | `FrmQuanLy`: Thêm / Sửa / Xóa phòng và dịch vụ, xem danh sách khách hàng; BUS kiểm tra dữ liệu (giá > 0, tên không trống, CCCD 12 số…) |
| Báo cáo | `FrmBaoCao`: lịch sử thanh toán theo ngày, doanh thu theo loại phòng, tổng doanh thu tính bằng **ExecuteScalar** |
| Chống SQL Injection | Mọi truy vấn đều dùng **SqlParameter** |

## MỤC 3: HÌNH ẢNH KẾT QUẢ CÁC MÀN HÌNH CHỨC NĂNG

1. Màn hình Chính / Tổng quan (danh sách phòng + lọc):
   <img width="720" height="374" alt="Screenshot 2026-10-30 193341" src="https://github.com/user-attachments/assets/4e593d65-e001-4106-97cb-658fa0e7b059" />

2. Màn hình Quản lý Danh mục / Thêm Sửa Xóa (CRUD):
  <img width="530" height="278" alt="Screenshot 2026-10-30 193355" src="https://github.com/user-attachments/assets/dd943ced-1e25-471e-afdc-5c07d545fcec" />

3. Màn hình Thực thi Nghiệp vụ chính (Check-in / Gọi dịch vụ / Check-out):
  <img width="720" height="380" alt="Screenshot 2026-10-30 193419" src="https://github.com/user-attachments/assets/1a69d720-ac2a-4134-a38d-dd8b59378121" />

4. Màn hình Báo cáo - Thống kê:
  <img width="722" height="375" alt="Screenshot 2026-10-30 193432" src="https://github.com/user-attachments/assets/33afa353-91fc-4c85-b81d-aaaac3e3c5b8" />

5. Minh họa xử lý lỗi (CCCD sai định dạng / nhận phòng không còn trống):
  
<img width="733" height="382" alt="Screenshot 2026-10-30 193445" src="https://github.com/user-attachments/assets/b6c78569-f1f3-4e32-908f-c006bb88475e" />

# BÀI TẬP 5.5 – HỆ THỐNG QUẢN LÝ QUY TRÌNH XUẤT - NHẬP KHO NGUYÊN VẬT LIỆU (INVENTORY MANAGEMENT)

Ứng dụng WinForms (C#, .NET Framework 4.8) kết nối SQL Server bằng ADO.NET, kiến trúc 3 lớp **DTO – DAL – BUS – GUI**.

## MỤC 1: THÔNG TIN SINH VIÊN

- **Họ và tên sinh viên:** nguyễn Thị Hồng Linh
- **Mã sinh viên:** 24810310005
- **Lớp:** D19QTANM1
- **Tên Đề tài / Bài tập:** Bài 5.5 – Hệ thống quản lý quy trình xuất - nhập kho nguyên vật liệu

## MỤC 2: HƯỚNG DẪN CÀI ĐẶT & CHẠY DỰ ÁN

**Yêu cầu:** Visual Studio 2022 (workload *.NET desktop development*, đã kèm SQL Server Express LocalDB).

1. Mở file `InventoryManagement.sln` bằng Visual Studio 2022.
2. Build & Run dự án (F5). Ở lần chạy đầu, chương trình tự kiểm tra: nếu chưa có CSDL `InventoryDB` thì tự chạy file `Database/ScriptDB.sql` để tạo bảng và dữ liệu mẫu (lớp `DAL/DbInit.cs`).
3. Mặc định chuỗi kết nối dùng `(localdb)\MSSQLLocalDB`. Nếu muốn dùng SQL Server khác, sửa `Data Source` trong `InventoryManagement/App.config` (ví dụ `.\SQLEXPRESS`).
4. Có thể chạy thủ công `Database/ScriptDB.sql` bằng SQL Server Management Studio (SSMS) nếu muốn tạo lại CSDL từ đầu.

## Cấu trúc thư mục

```
├── Database/
│   └── ScriptDB.sql              # Tạo CSDL InventoryDB + dữ liệu mẫu (tồn kho khớp với các phiếu mẫu)
├── InventoryManagement/
│   ├── DTO/                      # NhaCungCapDTO, VatTuDTO, PhieuKhoDTO, ChiTietPhieuKhoDTO
│   ├── DAL/                      # DatabaseHelper, NhaCungCapDAL, VatTuDAL, PhieuKhoDAL
│   ├── BUS/                      # NhaCungCapBUS, VatTuBUS, PhieuKhoBUS
│   ├── GUI/                      # FrmMain, UcPhieuKho, UcVatTu, UcNhaCungCap, UcBaoCao, UiHelper
│   ├── App.config
│   └── Program.cs
├── images/
├── README.md
└── InventoryManagement.sln
```

> Bảng `VatTu` có thêm cột `DinhMucToiThieu` (ngưỡng tồn kho an toàn) để phục vụ chức năng cảnh báo tồn kho.

## Chức năng đã thực hiện

| Yêu cầu đề bài | Cách thực hiện |
|---|---|
| Quản lý Vật tư & Nhà cung cấp (Connected Model) | Thêm / Sửa / Xóa / Tra cứu qua lớp BUS → DAL, dùng `SqlConnection` + `SqlCommand` + `SqlDataReader`. Không cho xóa vật tư / NCC đã phát sinh phiếu |
| Tìm kiếm chống SQL Injection | Tìm vật tư theo **mã hoặc tên** bằng `SqlParameter` (nhập `' OR 1=1 --` chỉ được coi là chuỗi tìm kiếm). Mọi câu lệnh khác cũng dùng SqlParameter |
| Lập phiếu Nhập / Xuất (SqlTransaction) | Chọn loại phiếu → thêm danh sách vật tư, số lượng, đơn giá → **Lưu phiếu kho**: `PhieuKhoDAL.LuuPhieuKho()` mở một `SqlTransaction` và lần lượt: (1) chèn `PhieuKho`, (2) chèn `ChiTietPhieuKho`, (3) **NHAP** cộng `SoLuongTon`; **XUAT** kiểm tra tồn, nếu `SoLuongTon < SoLuong` thì gọi `trans.Rollback()` và báo **"Không đủ hàng trong kho"**, (4) `trans.Commit()` khi tất cả thành công |
| Cảnh báo tồn kho | Danh sách vật tư có `SoLuongTon < DinhMucToiThieu`, kèm số lượng cần nhập thêm. Ở tab Vật tư, các dòng này hiện chữ đỏ |
| Tổng giá trị tồn kho | Tính bằng **`ExecuteScalar`**: Σ (SoLuongTon × đơn giá nhập gần nhất) |
| Báo cáo | Lịch sử phiếu kho theo ngày / loại phiếu và chi tiết từng phiếu |

## MỤC 3: HÌNH ẢNH KẾT QUẢ CÁC MÀN HÌNH CHỨC NĂNG

1. Màn hình Chính / Tổng quan:
   <img width="724" height="383" alt="Screenshot 2026-10-30 193745" src="https://github.com/user-attachments/assets/4dea72e8-c891-471a-b186-31e3c6f7a24a" />

2. Màn hình Quản lý Danh mục / Thêm Sửa Xóa (CRUD):
   <img width="722" height="381" alt="Screenshot 2026-10-30 193804" src="https://github.com/user-attachments/assets/c208c25f-8b49-43e0-a197-01df7f69b976" />

3. Màn hình Thực thi Nghiệp vụ chính (Lập phiếu nhập / xuất):
  <img width="731" height="388" alt="Screenshot 2026-10-30 193909" src="https://github.com/user-attachments/assets/363d8484-b692-4124-986c-45fee617db96" />

4. Màn hình Báo cáo - Thống kê (cảnh báo tồn kho, tổng giá trị tồn):
  <img width="722" height="306" alt="Screenshot 2026-10-30 193921" src="https://github.com/user-attachments/assets/763dbe6f-306a-457c-aae5-5b121db7d67d" />

5. Minh họa báo lỗi giao dịch – xuất quá số tồn, phiếu bị Rollback:
  <img width="719" height="380" alt="Screenshot 2026-10-30 193934" src="https://github.com/user-attachments/assets/d623d455-1268-4aff-bfb5-f486374b9d79" />

