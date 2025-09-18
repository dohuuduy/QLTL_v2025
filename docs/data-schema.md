## Cau truc Data – Quan ly Ho so TC/HC (AppSheet + Google Sheets)

Muc tieu: Chuan hoa cau truc 7 bang du lieu, mo ta cot, kieu, quan he de trien khai AppSheet. File nay la nguon tham chieu va cap nhat dai han.

### Quy uoc dat ten
- Bang: IN HOA, khong dau, co gach duoi giua cac tu. Vi du: `HO_SO`, `NGUOI_DUNG`, `PHIEN_BAN`, `TIEU_CHUAN`, `DIEU_KHOAN`, `HO_SO_TIEU_CHUAN`, `NHAC_NHO_CAP_NHAT`.
- Cot: chu thuong, khong dau, `snake_case`. Vi du: `id_hoso`, `ma_hoso`, `ngay_cap_nhat`.
- Cot ao (virtual column): them tien to `_` truoc ten. Vi du: `_phien_ban_list`.

### Tong quan bang
- HO_SO: Trung tam quan ly Policy/SOP/WI/Record
- NGUOI_DUNG: Nguoi phu trach va phan quyen
- PHIEN_BAN: Vong doi phien ban tai lieu
- TIEU_CHUAN: Danh muc tieu chuan
- DIEU_KHOAN: Dieu khoan theo tung tieu chuan
- HO_SO_TIEU_CHUAN: Bang noi nhieu-nhieu Ho so + Tieu chuan/Dieu khoan
- NHAC_NHO_CAP_NHAT: Tan suat va canh bao cap nhat

---

### Tai lieu theo tung bang
- HO_SO: xem `docs/HO_SO.md`
- NGUOI_DUNG: xem `docs/NGUOI_DUNG.md`
- PHIEN_BAN: xem `docs/PHIEN_BAN.md`
- (Se tach tiep cac bang: `TIEU_CHUAN.md`, `DIEU_KHOAN.md`, `HO_SO_TIEU_CHUAN.md`, `NHAC_NHO_CAP_NHAT.md`)

---

### 1) HO_SO
Quan ly toan bo ho so cac cap (Policy, SOP, WI, Record).

| Cot | Kieu du lieu | Mo ta |
| --- | --- | --- |
| `id_hoso` | Text (Key) | Ma he thong duy nhat (UNIQUEID). |
| `ma_hoso` | Text | Ma ISO-style (vd: POL-NS-01, SOP-NS-01). |
| `ten_hoso` | Text | Ten ho so/tai lieu. |
| `cap_tai_lieu` | Enum | C1=Policy, C2=SOP, C3=WI, C4=Record. |
| `nhom_hoso` | Text | Nhom: Phap ly, Nhan su, Luong, Dao tao, ATLD, CSR, Khach hang... |
| `de_muc` | Text | De muc lon (Phap ly, Lao dong, Dao tao...). |
| `mo_ta` | LongText | Mo ta ngan ho so. |
| `id_hoso_cha` | Ref(HO_SO) | Lien ket ho so cha (neu la SOP/WI/Record). |
| `tieu_chuan` | EnumList | Lien ket tieu chuan (tuy chon neu khong dung bang noi). |
| `dieu_khoan` | EnumList | Dieu khoan tham chieu (tuy chon neu khong dung bang noi). |
| `tan_suat` | Text | Tan suat cap nhat: Hang nam, 6 thang, Khi thay doi... |
| `ngay_ban_hanh` | Date | Ngay phat hanh. |
| `ngay_cap_nhat` | Date | Ngay cap nhat gan nhat. |
| `nguoi_phu_trach` | Ref(NGUOI_DUNG) | Ai quan ly ho so. |
| `trang_thai` | Enum | Hieu luc, Het hieu luc, Cho duyet. |

Goi y cau hinh AppSheet:
- `id_hoso` Initial value: `UNIQUEID()`
- `ma_hoso` Valid_If (mau ma ISO): `REGEX_MATCH([ma_hoso], "^(POL|SOP|WI|REC)-[A-Z]{2,}-[0-9]{2,}$")`
- `ngay_cap_nhat` App formula: `MAX(SELECT(PHIEN_BAN[ngay_phat_hanh], [id_hoso]=[_THISROW].[id_hoso]))`
- Virtual column `_phien_ban_list`: `REF_ROWS("PHIEN_BAN","id_hoso")`
- Virtual column `_tieu_chuan_map`: `SELECT(HO_SO_TIEU_CHUAN[id_tieuchuan], [id_hoso]=[_THISROW].[id_hoso])`

---

### 2) NGUOI_DUNG
Danh sach nguoi phu trach, phan quyen AppSheet.

| Cot | Kieu du lieu | Mo ta |
| --- | --- | --- |
| `id_nguoi` | Text (Key) | Khoa chinh. |
| `ten` | Text | Ho ten nguoi dung. |
| `email` | Text | Email dang nhap AppSheet. |
| `phong_ban` | Text/Enum | Phong To Chuc, QA, HSE, San xuat... |
| `vai_tro` | Enum | Admin, HR, QA, HSE, Viewer. |

Goi y cau hinh AppSheet:
- `id_nguoi` Initial value: `UNIQUEID()`

---

### 3) PHIEN_BAN
Quan ly vong doi tai lieu (versioning).

| Cot | Kieu du lieu | Mo ta |
| --- | --- | --- |
| `id_phienban` | Text (Key) | Khoa chinh. |
| `id_hoso` | Ref(HO_SO) | Lien ket ho so. |
| `so_phien_ban` | Text/Decimal | So phien ban (vd: 1.0, 1.1). Khuyen Text. |
| `ngay_phat_hanh` | Date | Ngay phat hanh. |
| `mo_ta_thay_doi` | LongText | Noi dung chinh sua. |
| `nguoi_duyet` | Ref(NGUOI_DUNG) | Nguoi duyet phat hanh. |
| `trang_thai` | Enum | Moi, Da duyet, Huy. |

Goi y cau hinh AppSheet:
- `id_phienban` Initial value: `UNIQUEID()`
- `ngay_phat_hanh` Initial value: `TODAY()`
- Bot cap nhat `HO_SO[ngay_cap_nhat]` khi `trang_thai` chuyen "Da duyet"

---

### 4) TIEU_CHUAN
Danh muc cac tieu chuan.

| Cot | Kieu du lieu | Mo ta |
| --- | --- | --- |
| `id_tieuchuan` | Text (Key) | Khoa chinh. |
| `ten_tieuchuan` | Text | Vi du: ISO 9001:2015, BRC v9, SMETA 7.0. |
| `loai` | Enum | ISO, BRC, ASC, BAP, HACCP, Halal, Khach hang. |

Goi y cau hinh AppSheet:
- `id_tieuchuan` Initial value: `UNIQUEID()`

---

### 5) DIEU_KHOAN
Chi tiet dieu khoan de mapping.

| Cot | Kieu du lieu | Mo ta |
| --- | --- | --- |
| `id_dieu_khoan` | Text (Key) | Khoa chinh. |
| `id_tieuchuan` | Ref(TIEU_CHUAN) | Tieu chuan cha. |
| `ma_dieu_khoan` | Text | Vi du: 7.5, 1.1.2, Pillar 2. |
| `mo_ta` | LongText | Mo ta yeu cau dieu khoan. |

Goi y cau hinh AppSheet:
- `id_dieu_khoan` Initial value: `UNIQUEID()`

---

### 6) HO_SO_TIEU_CHUAN
Bang noi nhieu-nhieu giua ho so va tieu chuan/dieu khoan.

| Cot | Kieu du lieu | Mo ta |
| --- | --- | --- |
| `id_hoso_tc` | Text (Key) | Khoa chinh. |
| `id_hoso` | Ref(HO_SO) | Lien ket ho so. |
| `id_tieuchuan` | Ref(TIEU_CHUAN) | Tieu chuan lien quan. |
| `id_dieu_khoan` | Ref(DIEU_KHOAN) | Dieu khoan lien quan. |

Goi y cau hinh AppSheet:
- `id_hoso_tc` Initial value: `UNIQUEID()`
- `id_dieu_khoan` Valid_If: `SELECT(DIEU_KHOAN[id_dieu_khoan], [id_tieuchuan]=[_THISROW].[id_tieuchuan])`

---

### 7) NHAC_NHO_CAP_NHAT
Quan ly tan suat va canh bao update.

| Cot | Kieu du lieu | Mo ta |
| --- | --- | --- |
| `id_nhacnho` | Text (Key) | Khoa chinh. |
| `id_hoso` | Ref(HO_SO) | Lien ket ho so. |
| `tan_suat` | Text/Enum | Hang nam, 6 thang, Khi thay doi. |
| `ngay_ke_tiep` | Date | Han cap nhat tiep theo. |
| `trang_thai` | Enum | Dang cho, Hoan thanh. |

Goi y cau hinh AppSheet:
- `id_nhacnho` Initial value: `UNIQUEID()`
- `ngay_ke_tiep` App formula:
  - `IFS([tan_suat]="Hang nam", EDATE([id_hoso].[ngay_cap_nhat],12), [tan_suat]="6 thang", EDATE([id_hoso].[ngay_cap_nhat],6), TRUE, [id_hoso].[ngay_cap_nhat])`

---

### Lien ket & Quan he
- HO_SO + PHIEN_BAN: 1-nhieu (mot ho so co nhieu phien ban)
- HO_SO + NGUOI_DUNG: Nguoi phu trach (`nguoi_phu_trach` Ref)
- HO_SO + HO_SO_TIEU_CHUAN + TIEU_CHUAN + DIEU_KHOAN: Mapping nhieu-nhieu
- HO_SO + NHAC_NHO_CAP_NHAT: Nhac lich cap nhat

Rang buoc goi y:
- Chi cho "Het hieu luc" khi co ban "Da duyet":
  - `OR([trang_thai]<>"Het hieu luc", COUNT(SELECT(PHIEN_BAN[id_phienban], AND([id_hoso]=[_THISROW].[id_hoso],[trang_thai]="Da duyet"))) > 0)`

---

### Bao mat & phan quyen (goi y)
- Security Filter PHIEN_BAN (Viewer chi xem da duyet):
  - `OR(LOOKUP(USEREMAIL(),"NGUOI_DUNG","email","vai_tro")<>"Viewer", [trang_thai]="Da duyet")`
- Han che EDIT theo vai tro (`vai_tro`) o tung bang theo nhu cau.

---

### UX & Tu dong hoa (goi y)
- Tao phien ban moi tu HO_SO bang Action "Add a new row to PHIEN_BAN"; set `trang_thai="Moi"`, `ngay_phat_hanh=TODAY()`.
- Bot cap nhat `HO_SO[ngay_cap_nhat]` khi PHIEN_BAN duyet.
- Overdue NHAC_NHO_CAP_NHAT: `AND([trang_thai]="Dang cho", [ngay_ke_tiep] <= TODAY())`.
- Sap toi hon 14 ngay: `AND([trang_thai]="Dang cho", [ngay_ke_tiep] <= TODAY()+14)`.

---

### Luu y trien khai
- Dang Text cho `so_phien_ban` de tranh loi dinh dang thap phan (1.10 vs 1.1).
- Quan ly duy nhat bang `id_*` (UNIQUEID); `ma_hoso` chu yeu de hien thi/tra cuu theo quy tac ma.
- Neu so luong dieu khoan lon, uu tien bang noi `HO_SO_TIEU_CHUAN` de loc va hieu nang tot hon so voi EnumList truc tiep trong HO_SO.

