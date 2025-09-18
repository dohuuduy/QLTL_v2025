## DIEU_KHOAN

Muc dich: Quan ly dieu khoan/yeu cau cu the thuoc moi tieu chuan.

### Cot du lieu
| cot | kieu | mo_ta |
| --- | --- | --- |
| `id_dieu_khoan` | Text (Key) | Khoa chinh duy nhat (UNIQUEID). |
| `id_tieuchuan` | Ref(TIEU_CHUAN) | Tham chieu tieu chuan cha. |
| `ma_dieu_khoan` | Text | Vi du: 7.5, 1.1.2, Pillar 2. |
| `mo_ta` | LongText | Mo ta yeu cau dieu khoan. |

Luu y quy uoc:
- Ten bang IN HOA, khong dau: `DIEU_KHOAN`.
- Ten cot chu thuong, khong dau, `snake_case`.

### Key, Label, Hien thi
- Key: `id_dieu_khoan` (Initial value: `UNIQUEID()`).
- Label: `ma_dieu_khoan`.

### Tham chieu & cong thuc mau
- `id_tieuchuan` (Ref) – chon tu danh sach tieu chuan:
  - Valid_If: `SELECT(TIEU_CHUAN[id_tieuchuan], TRUE)`

