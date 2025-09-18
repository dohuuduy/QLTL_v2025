## HO_SO_TIEU_CHUAN

Muc dich: Bang noi nhieu-nhieu giua HO_SO va TIEU_CHUAN/DIEU_KHOAN.

### Cot du lieu
| cot | kieu | mo_ta |
| --- | --- | --- |
| `id_hoso_tc` | Text (Key) | Khoa chinh duy nhat (UNIQUEID). |
| `id_hoso` | Ref(HO_SO) | Tham chieu ho so. |
| `id_tieuchuan` | Ref(TIEU_CHUAN) | Tham chieu tieu chuan. |
| `id_dieu_khoan` | Ref(DIEU_KHOAN) | Tham chieu dieu khoan. |

Luu y quy uoc:
- Ten bang IN HOA, khong dau: `HO_SO_TIEU_CHUAN`.
- Ten cot chu thuong, khong dau, `snake_case`.

### Goi y cau hinh AppSheet
- `id_hoso_tc` Initial value: `UNIQUEID()`.
- `id_dieu_khoan` Valid_If (loc theo tieu chuan da chon):
  - `SELECT(DIEU_KHOAN[id_dieu_khoan], [id_tieuchuan]=[_THISROW].[id_tieuchuan])`

