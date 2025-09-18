## PHIEN_BAN

Muc dich: Quan ly vong doi phien ban tai lieu (versioning) va trang thai phe duyet.

### Cot du lieu
| cot | kieu | mo_ta |
| --- | --- | --- |
| `id_phienban` | Text (Key) | Khoa chinh duy nhat (UNIQUEID). |
| `id_hoso` | Ref(HO_SO) | Tham chieu ho so. |
| `so_phien_ban` | Text | Dinh dang hien thi: `vX.Y_YYYY` (vd: `v1.0_2025`, `v1.1_2026`). |
| `ngay_phat_hanh` | Date | Ngay phat hanh. |
| `mo_ta_thay_doi` | LongText | Noi dung chinh sua. |
| `nguoi_duyet` | Ref(NGUOI_DUNG) | Nguoi duyet phat hanh. |
| `trang_thai` | Enum | Moi, Da duyet, Het hieu luc, Huy. |

Luu y quy uoc:
- Ten bang IN HOA, khong dau: `PHIEN_BAN`.
- Ten cot chu thuong, khong dau, `snake_case`. Khong thay doi ten cot sau khi da su dung trong cong thuc.

### Key, Label, Hien thi
- Key: `id_phienban` (Initial value: `UNIQUEID()`).
- Co the hien thi `so_phien_ban` lam Label hoac ket hop voi `ngay_phat_hanh` o Display name.

### Enum values co dinh
- `trang_thai`: `Moi`, `Da duyet`, `Huy`.

### Goi y cau hinh AppSheet
- `id_phienban` Initial value: `UNIQUEID()`.
- `ngay_phat_hanh` Initial value: `TODAY()`.
- `so_phien_ban` Initial value (tinh tu dong theo nam hien tai va so ban da duyet):
  - `"v" & IF(\
        COUNT(SELECT(PHIEN_BAN[id_phienban], AND([id_hoso]=[_THISROW].[id_hoso], [trang_thai]="Da duyet")))=0,\
        "1.0",\
        "1." & TEXT(COUNT(SELECT(PHIEN_BAN[id_phienban], AND([id_hoso]=[_THISROW].[id_hoso], [trang_thai]="Da duyet"))))\
     ) & "_" & TEXT(TODAY(),"YYYY")`

### Automation (Bot/Action) de quan ly vong doi
- Action A (tren HO_SO): "Tao phien ban moi" → Add a new row to `PHIEN_BAN` voi gia tri mac dinh o tren.
- Bot 1 (tren PHIEN_BAN): Khi `[trang_thai]` chuyen sang `Da duyet`:
  - Step 1: Data action cap nhat `HO_SO[ngay_cap_nhat]` = `[ngay_phat_hanh]`.
  - Step 2: Data action bulk: Dat `PHIEN_BAN[trang_thai]="Het hieu luc"` cho tat ca ban cung `id_hoso` khac `[_THISROW]` dang `Moi` hoac `Da duyet`.
  - Step 3: (tuy chon) Gui thong bao/ghi nhat ky thay doi.

### Rang buoc, hop le (tham khao)
- Chi cho phep `trang_thai = "Da duyet"` khi co du `ngay_phat_hanh` va `so_phien_ban` khong rong:
  - `OR([trang_thai]<>"Da duyet", AND(ISNOTBLANK([ngay_phat_hanh]), ISNOTBLANK([so_phien_ban])))`

### Bao mat (tham khao)
- Security Filter de Viewer chi xem ban `Da duyet`:
  - `OR(LOOKUP(USEREMAIL(),"NGUOI_DUNG","email","vai_tro")<>"Viewer", [trang_thai]="Da duyet")`
