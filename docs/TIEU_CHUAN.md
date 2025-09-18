## TIEU_CHUAN

Muc dich: Quan ly danh muc tieu chuan ap dung (ISO/BRC/khach hang...).

### Cot du lieu
| cot | kieu | mo_ta |
| --- | --- | --- |
| `id_tieuchuan` | Text (Key) | Khoa chinh duy nhat (UNIQUEID). |
| `ten_tieuchuan` | Text | Vi du: ISO 9001:2015, BRC v9, SMETA 7.0. |
| `loai` | Enum | ISO, BRC, ASC, BAP, HACCP, Halal, Khach hang. |

Luu y quy uoc:
- Ten bang IN HOA, khong dau: `TIEU_CHUAN`.
- Ten cot chu thuong, khong dau, `snake_case`.
- Cot ao dat tien to `_`.

### Key, Label, Hien thi
- Key: `id_tieuchuan` (Initial value: `UNIQUEID()`).
- Label: `ten_tieuchuan`.

### Goi y cau hinh AppSheet
- `id_tieuchuan` Initial value: `UNIQUEID()`.
- Cot ao `_dieu_khoan_list` (danh sach dieu khoan con):
  - `REF_ROWS("DIEU_KHOAN","id_tieuchuan")`

