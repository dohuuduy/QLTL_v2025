## NHAC_NHO_CAP_NHAT

Muc dich: Quan ly tan suat va nhac nho cap nhat ho so.

### Cot du lieu
| cot | kieu | mo_ta |
| --- | --- | --- |
| `id_nhacnho` | Text (Key) | Khoa chinh duy nhat (UNIQUEID). |
| `id_hoso` | Ref(HO_SO) | Tham chieu ho so. |
| `tan_suat` | Text/Enum | Hang nam, 6 thang, Khi thay doi. |
| `ngay_ke_tiep` | Date | Han cap nhat tiep theo. |
| `trang_thai` | Enum | Dang cho, Hoan thanh. |

Luu y quy uoc:
- Ten bang IN HOA, khong dau: `NHAC_NHO_CAP_NHAT`.
- Ten cot chu thuong, khong dau, `snake_case`.
- Cot ao dat tien to `_`.

### Goi y cau hinh AppSheet
- `id_nhacnho` Initial value: `UNIQUEID()`.
- `ngay_ke_tiep` App formula (dua tren `HO_SO[ngay_cap_nhat]`):
  - `IFS([tan_suat]="Hang nam", EDATE([id_hoso].[ngay_cap_nhat],12), [tan_suat]="6 thang", EDATE([id_hoso].[ngay_cap_nhat],6), TRUE, [id_hoso].[ngay_cap_nhat])`
- Cot ao `_canh_bao` (tinh trang han):
  - Qua han: `AND([trang_thai]="Dang cho", [ngay_ke_tiep] <= TODAY())`
  - Sap toi 14 ngay: `AND([trang_thai]="Dang cho", [ngay_ke_tiep] <= TODAY()+14)`

