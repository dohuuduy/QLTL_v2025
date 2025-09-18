## NGUOI_DUNG

Muc dich: Quan ly danh sach nguoi dung, phan quyen su dung ung dung AppSheet.

### Cot du lieu
| cot | kieu | mo_ta |
| --- | --- | --- |
| `id_nguoi` | Text (Key) | Khoa chinh duy nhat (UNIQUEID). |
| `ten` | Text | Ho ten nguoi dung. |
| `email` | Text | Email dang nhap AppSheet. |
| `phong_ban` | Text/Enum | Vi du: Phong To Chuc, QA, HSE, San xuat... |
| `vai_tro` | Enum | Admin, HR, QA, HSE, Viewer. |

Luu y quy uoc:
- Ten bang IN HOA, khong dau: `NGUOI_DUNG`.
- Ten cot chu thuong, khong dau, `snake_case`. Khong thay doi ten cot sau khi da su dung trong cong thuc.

### Key, Label, Hien thi
- Key: `id_nguoi` (Initial value: `UNIQUEID()`).
- Label de de doc: `ten`.

### Enum values co dinh
- `vai_tro`: `Admin`, `HR`, `QA`, `HSE`, `Viewer`.
- Nen dat Data validation tren Google Sheets de co dinh gia tri.

### Goi y cau hinh AppSheet
- Cot `id_nguoi` Initial value: `UNIQUEID()`.
- Co the dat `email` la type Email (neu dung AppSheet) de ho tro validate.
- Thiet lap Slice/Behavior tuy vai tro neu can.

### Quan he (Ref)
- Duoc tham chieu boi: `HO_SO[nguoi_phu_trach]`, `PHIEN_BAN[nguoi_duyet]`.

### Bao mat (tham khao)
- Vi du Security Filter o bang PHIEN_BAN de Viewer chi xem ban "Da duyet":
  - `OR(LOOKUP(USEREMAIL(),"NGUOI_DUNG","email","vai_tro")<>"Viewer", [trang_thai]="Da duyet")`
