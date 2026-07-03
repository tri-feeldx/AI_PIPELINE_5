# 5 Phút Setup & Chạy Thử

## 1️⃣ Cài đặt (2 phút)

```bash
# Clone repo
git clone https://github.com/tri-feeldx/AI_PIPELINE_5.git
cd AI_PIPELINE_5
git checkout submit-minimal

# Cài Python packages từ requirements.txt
pip install -r requirements.txt
```

## 2️⃣ Chạy Web App (Streamlit)

**Cách dễ nhất — giao diện web:**

```bash
streamlit run app.py
```

Sau đó:
1. Mở browser → `http://localhost:8501`
2. Upload file PDF
3. Chọn page + scale (1:100, 1:50, vv)
4. Click "▶️ Run Extraction"
5. Xem kết quả + download JSON

---

## 3️⃣ Chạy từ Python Script (advanced)

Nếu không dùng web, tạo file `test_extract.py`:

```python
from src.slab_v2.config import SlabV2Config
from src.slab_v2.pipeline import extract_slabs_v2

cfg = SlabV2Config(
    debug_images=False,
    manual_scale=100  # 1:100 scale
)

result = extract_slabs_v2(
    pdf_path="drawing.pdf",
    page_index=10,
    config=cfg,
    use_ai=False
)

print(f"Status: {result.status}")
print(f"Slabs: {len(result.slabs)}")
print(f"Columns: {len(result.columns)}")
print(f"Walls: {len(result.walls)}")
```

Chạy:
```bash
python test_extract.py
```

---

## 4️⃣ Batch processing (nhiều trang)

```python
for page_idx in range(5):  # trang 1-5
    result = extract_slabs_v2("drawing.pdf", page_idx, cfg, use_ai=False)
    print(f"p{page_idx+1}: {result.status} - "
          f"{len(result.slabs)} slab, "
          f"{len(result.columns)} col, "
          f"{len(result.walls)} wall")
```

---

## ❓ FAQ

**Q: Dùng web hay script?**
- **Web (Streamlit)** → dễ, upload click-click, xem kết quả liền
- **Script** → batch nhanh, tự động hóa được

**Q: Làm sao biết scale bản vẽ?**
- Thường viết: "1:100" hay "1:50" trên title block
- Nếu không biết → thử 100 trước (phổ biến nhất)

**Q: Kết quả sai?**
- Check: Trang là GA plan (mặt bằng) chứ?
- Check: Sàn tô màu chưa? (không phải dashed line)
- Check: Scale có đúng không?

**Q: Lỗi khi install?**
- Windows: Bạn dùng Python 3.9+ chưa? `python --version`
- Streamlit không cài được? → `pip install --upgrade pip` rồi retry

---

## 📊 Output

Kết quả trả về JSON:
```json
{
  "page": 11,
  "status": "OK",
  "slabs_count": 1,
  "slabs_area_m2": 3450.5,
  "columns_count": 15,
  "columns": { "C1": {...}, "C2": {...} },
  "walls_count": 12,
  "walls": { "W1": {...}, "W2": {...} }
}
```

- `status="OK"` → thành công
- `slabs_area_m2` → tổng diện tích sàn
- `columns` → từ COLUMN SCHEDULE của trang
- `walls` → từ WALL SCHEDULE của trang

---

**Version**: 1.0 (Slab + Columns + Walls)
**Repo**: https://github.com/tri-feeldx/AI_PIPELINE_5

