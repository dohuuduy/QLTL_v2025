## HO_SO

Muc dich: Quan ly toan bo ho so/tai lieu cac cap (Policy, SOP, WI, Record).

### Cot du lieu
| cot | kieu | mo_ta |
| --- | --- | --- |
| `id_hoso` | Text (Key) | Khoa chinh duy nhat (UNIQUEID). |
| `ma_hoso` | Text | Ma ISO-style, vi du: `POL-NS-01`, `SOP-NS-01`. |
| `ten_hoso` | Text | Ten ho so/tai lieu. |
| `cap_tai_lieu` | Enum | C1, C2, C3, C4 (phan cap tai lieu). |
| `nhom_hoso` | Text (App formula) | Hien thi ten cap tuong ung voi `cap_tai_lieu`. |
| `de_muc` | Text | De muc lon (Phap ly, Lao dong, Dao tao...). |
| `mo_ta` | LongText | Mo ta ngan ho so. |
| `id_hoso_cha` | Ref(HO_SO) | Tham chieu ho so cha (neu la SOP/WI/Record). |
| `tieu_chuan` | EnumList | Lien ket tieu chuan (tuy chon neu khong dung bang noi). |
| `dieu_khoan` | EnumList | Dieu khoan tham chieu (tuy chon neu khong dung bang noi). |
| `tan_suat` | Text | Hang nam, 6 thang, Khi thay doi... |
| `ngay_ban_hanh` | Date | Ngay phat hanh. |
| `ngay_cap_nhat` | Date | Ngay cap nhat gan nhat. |
| `nguoi_phu_trach` | Ref(NGUOI_DUNG) | Nguoi quan ly ho so. |
| `trang_thai` | Enum | Hieu luc, Het hieu luc, Cho duyet. |

Luu y quy uoc:
- Ten bang IN HOA, khong dau: `HO_SO`.
- Ten cot chu thuong, khong dau, `snake_case`. Khong thay doi ten cot sau khi da su dung trong cong thuc.

### Key, Label, Hien thi
- Key: `id_hoso` (Initial value: `UNIQUEID()`).
- Label de de doc: uu tien `ma_hoso`; co the dung `ten_hoso` neu khong co ma.

### Enum values co dinh
- `cap_tai_lieu`: `C1`, `C2`, `C3`, `C4`.
- `trang_thai`: `Hieu luc`, `Het hieu luc`, `Cho duyet`.
- `tan_suat`: `Hang nam`, `6 thang`, `Khi thay doi`.
- Nen dat Data validation tren Google Sheets de co dinh gia tri.

- ### Goi y cau hinh AppSheet
- `id_hoso` Initial value: `UNIQUEID()`.
- Cau truc ma ho so (khong dung regex): `[PHONG_BAN]-[LINH_VUC]-[CAP]-[SO].[CAP_CON...]`
  - Vi du:
    - `HR-CSR-POL-01`
    - `HR-CSR-POL-01.QP01`
    - `HR-CSR-POL-01.QP01.BM01`
- Mapping CAP theo `cap_tai_lieu`:
  - C1 → `POL` (Chinh sach)
  - C2 → `QP`  (Quy trinh)
  - C3 → `WI`  (Huong dan)
  - C4 → `BM`  (Bieu mau)
- `ma_hoso` Valid_If (tach cong thuc thanh cac buoc, xem ben duoi):
  - `AND(_ok_basic, _ok_cap_level, _parent_prefix_ok, _ok_transition)`
- `cap_tai_lieu` Enum values: `C1`, `C2`, `C3`, `C4`.
- `cap_tai_lieu` Valid_If: `IN([cap_tai_lieu], LIST("C1","C2","C3","C4"))`.
- `nhom_hoso` App formula (tu dong hien ten cap):
  - `SWITCH([cap_tai_lieu],\
      "C1","Chinh sach",\
      "C2","Quy trinh",\
      "C3","Huong dan",\
      "C4","Bieu mau",\
      "")`
- `ngay_cap_nhat` App formula: `MAX(SELECT(PHIEN_BAN[ngay_phat_hanh], [id_hoso]=[_THISROW].[id_hoso]))`.
- Virtual column `_phien_ban_list`: `REF_ROWS("PHIEN_BAN","id_hoso")`.
- Virtual column `_tieu_chuan_map` (neu dung bang noi): `SELECT(HO_SO_TIEU_CHUAN[id_tieuchuan], [id_hoso]=[_THISROW].[id_hoso])`.
 - Virtual column `_phien_ban_hien_hanh` (so phien ban hien tai hien thi tren HO_SO):
   - `ANY(ORDERBY(SELECT(PHIEN_BAN[so_phien_ban], AND([id_hoso]=[_THISROW].[id_hoso], [trang_thai]="Da duyet")), [ngay_phat_hanh], TRUE))`

### Tach cong thuc (khuyen nghi de de bao tri)
- Cot ao `_phong_ban`: `INDEX(SPLIT([ma_hoso], "-"),1)`
- Cot ao `_linh_vuc`: `INDEX(SPLIT([ma_hoso], "-"),2)`
- Cot ao `_cap_ma`: `INDEX(SPLIT([ma_hoso], "-"),3)`  (POL/QP/WI/BM)
- Cot ao `_so_thu_tu`: `INDEX(SPLIT(INDEX(SPLIT([ma_hoso], "-"),4), "."),1)`
- Cot ao `_segments_dot_count`: `COUNT(SPLIT([ma_hoso], "."))`
- Cot ao `_cap_con_1`: `IF(COUNT(SPLIT([ma_hoso], "."))>=2, INDEX(SPLIT([ma_hoso], "."),2), "")`
- Cot ao `_cap_con_2`: `IF(COUNT(SPLIT([ma_hoso], "."))>=3, INDEX(SPLIT([ma_hoso], "."),3), "")`
- Cot ao `_ma_root`: `IF(CONTAINS([ma_hoso], "."), LEFT([ma_hoso], FIND(".", [ma_hoso])-1), [ma_hoso])`

- Cot ao `_ok_basic` (Boolean) — cau truc co ban:
  - `AND(\
       COUNT(SPLIT([ma_hoso], "-"))>=4,\
       LEN([_phong_ban])>=2,\
       LEN([_linh_vuc])>=2,\
       IN([_cap_ma], LIST("POL","QP","WI","BM")),\
       ISNOTBLANK(VALUE([_so_thu_tu]))\
     )`

- Cot ao `_ok_cap_C1`: `AND([cap_tai_lieu]="C1", [_segments_dot_count]=1, [_cap_ma]="POL")`
- Cot ao `_ok_cap_C2`: `AND([cap_tai_lieu]="C2", [_segments_dot_count]=2, LEFT([_cap_con_1],2)="QP")`
- Cot ao `_ok_cap_C3`: `AND([cap_tai_lieu]="C3", [_segments_dot_count]=3, LEFT([_cap_con_1],2)="QP", LEFT([_cap_con_2],2)="WI")`
- Cot ao `_ok_cap_C4`: `AND([cap_tai_lieu]="C4", [_segments_dot_count]=3, LEFT([_cap_con_1],2)="QP", LEFT([_cap_con_2],2)="BM")`
- Cot ao `_ok_cap_level`: `OR([_ok_cap_C1], [_ok_cap_C2], [_ok_cap_C3], [_ok_cap_C4])`

- Cot ao `_parent_prefix_ok`: `OR(ISBLANK([id_hoso_cha]), LEFT([ma_hoso], LEN([id_hoso_cha].[ma_hoso])) = [id_hoso_cha].[ma_hoso])`

- Cot ao `_transition_str`: `[id_hoso_cha].[cap_tai_lieu] & "->" & [cap_tai_lieu]`
- Cot ao `_ok_transition` (khong bat buoc neu khong dung cap cha):
  - Quyen chinh: `OR(ISBLANK([id_hoso_cha]), IN([_transition_str], LIST("C1->C2","C2->C3","C2->C4","C3->C4")))`
  - Neu muon thoang hon: thay LIST thanh `LIST("C1->C2","C1->C3","C1->C4","C2->C3","C2->C4","C3->C4")`

### Hanh dong & Tu dong hoa lien quan phien ban
- Action (tren HO_SO): "Tao phien ban moi" → Add a new row to `PHIEN_BAN` (se tu dong set `so_phien_ban` = `vX.Y_YYYY`, `ngay_phat_hanh` = `TODAY()`, `trang_thai` = `Moi`).
- Bot (tren PHIEN_BAN): Khi `[trang_thai]` chuyen `Da duyet` thi:
  - Cap nhat `HO_SO[ngay_cap_nhat]` bang `[ngay_phat_hanh]` cua phien ban moi.
  - Dat cac phien ban khac cung `id_hoso` sang `Het hieu luc`.
  - Cot ao `_phien_ban_hien_hanh` tren HO_SO se hien thi dung phien ban moi nhat `Da duyet`.

### Cot ao de tach phan ma (khuyen nghi)
- `_phong_ban`: `INDEX(SPLIT([ma_hoso], "-"),1)`
- `_linh_vuc`: `INDEX(SPLIT([ma_hoso], "-"),2)`
- `_cap_ma`: `INDEX(SPLIT([ma_hoso], "-"),3)`  (POL/QP/WI/BM)
- `_so_thu_tu`: `INDEX(SPLIT(INDEX(SPLIT([ma_hoso], "-"),4), "."),1)`
- `_ma_root`: `IF(CONTAINS([ma_hoso], "."), LEFT([ma_hoso], FIND(".", [ma_hoso])-1), [ma_hoso])`
- `_cap_con_1`: `IF(COUNT(SPLIT([ma_hoso], "."))>=2, INDEX(SPLIT([ma_hoso], "."),2), "")`
- `_cap_con_2`: `IF(COUNT(SPLIT([ma_hoso], "."))>=3, INDEX(SPLIT([ma_hoso], "."),3), "")`

### Tham chieu & cong thuc mau
- `id_hoso_cha` (Ref(HO_SO)) – chon ho so cha tu danh sach ho so khac:
  - Valid_If: `SELECT(HO_SO[id_hoso], [id_hoso] <> [_THISROW].[id_hoso])`
- `nguoi_phu_trach` (Ref(NGUOI_DUNG)) – chon tu danh sach nguoi dung:
  - Valid_If: `SELECT(NGUOI_DUNG[id_nguoi], TRUE)`

### Quan he (Ref)
- 1-n voi: `PHIEN_BAN` (moi HO_SO co nhieu phien ban).
- n-n qua bang noi: `HO_SO_TIEU_CHUAN` toi `TIEU_CHUAN` va `DIEU_KHOAN`.
- Self-ref: `id_hoso_cha` (cau truc cap bac ho so).
- Ref toi `NGUOI_DUNG` qua `nguoi_phu_trach`.

### Rang buoc, hop le (tham khao)
- Chi cho `trang_thai = "Het hieu luc"` khi da co it nhat 1 `PHIEN_BAN` `Da duyet`:
  - `OR([trang_thai]<>"Het hieu luc", COUNT(SELECT(PHIEN_BAN[id_phienban], AND([id_hoso]=[_THISROW].[id_hoso],[trang_thai]="Da duyet"))) > 0)`.

### Ghi chu thiet ke
- Khuyen nghi su dung bang noi `HO_SO_TIEU_CHUAN` de quan ly lien ket tieu chuan/dieu khoan thay vi `EnumList` truc tiep trong `HO_SO` (de tranh trung lap, de loc va mo rong).
- Giua hai cach, chi chon mot cach de tranh sai lech du lieu. Neu da dung bang noi, giu `tieu_chuan`, `dieu_khoan` o trang thai tuy chon/khong dung trong form.
