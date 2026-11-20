# 📦 Discretization / Binning (การแบ่งข้อมูลเป็นช่วง)

> **ไฟล์นี้เป็นคำแปลภาษาไทยของ Notebook: `02-4-data-preprocessing-discretization.ipynb`**  
> Series 2: Data Preprocessing 🔍

---

## 📌 Discretization คืออะไร?

**Discretization** (หรือเรียกว่า **Binning**) คือการแปลง **ตัวเลขต่อเนื่อง (Continuous)** ให้กลายเป็น **หมวดหมู่ (Categories)**

**ตัวอย่าง:** แปลงอายุเป็นช่วงอายุ
```
อายุจริง → หมวดหมู่
   5      →  เด็ก (0-12)
  25      →  วัยรุ่น/ผู้ใหญ่ (13-40)
  65      →  ผู้สูงอายุ (40+)
```

---

## 🌟 ทำไมต้องทำ Discretization?

| เหตุผล | คำอธิบาย |
|---|---|
| **ลด Noise** | ตัวเลขที่แตกต่างกันเล็กน้อย (19.9 vs 20.1) ถูกมองว่าเหมือนกัน |
| **จัดการ Outlier** | ค่า Extreme ถูกดึงเข้ามาอยู่ใน Bin ปกติ |
| **เพิ่ม Interpretability** | "อุณหภูมิสูง" เข้าใจง่ายกว่า "อุณหภูมิ 35.7 องศา" |
| **บาง Algorithm ต้องการ** | Algorithm บางตัวทำงานได้ดีกว่ากับข้อมูล Categorical |
| **สำหรับ Non-linear Data** | ช่วยให้โมเดลเรียนรู้ความสัมพันธ์แบบ Non-linear |

---

## 📊 ข้อมูลที่ใช้: Golf Dataset (All Numerical)

```python
import pandas as pd
import numpy as np

golf_dataset = pd.read_csv("golf_dataset_mini_allnumerical.csv")
df = golf_dataset.copy()
df
```

**ตัวอย่างข้อมูล:**

| Date | Weekday | Month | Temperature | Humidity | Crowdedness |
|------|---------|-------|-------------|----------|-------------|
| 06-16 | 3 | 6 | 19.7 | 77.0 | 0.46 |
| 06-17 | 4 | 6 | 19.5 | 64.0 | 0.75 |
| 06-25 | 5 | 6 | 21.0 | 82.0 | 0.66 |
| 06-30 | 3 | 6 | 24.9 | 91.0 | 0.16 |

---

## 🛠️ กลยุทธ์การ Binning (Binning Strategies)

มี **3 วิธีหลัก** ในการแบ่ง Bins:

```
Binning Strategies
│
├── ✤ Equal Width Binning    → แบ่งช่วงให้ความกว้างเท่ากัน
├── ✤ Equal Frequency Binning → แบ่งให้แต่ละ Bin มีจำนวนข้อมูลเท่ากัน
└── ✤ Custom Binning          → กำหนดขอบเขตเอง
```

---

## ✤ Equal Width Binning (แบ่งความกว้างเท่ากัน)

**แนวคิด:** แบ่งช่วงข้อมูลออกเป็น N ส่วนที่มีความกว้างเท่ากัน

```
Temperature: 17.5 ─────────────────────────── 25.9
             ├─────┼─────┼─────┼─────┤
             Bin 0  Bin 1  Bin 2  Bin 3
           (17.5-19.6)(19.6-21.7)(21.7-23.8)(23.8-25.9)
```

```python
from sklearn.preprocessing import KBinsDiscretizer

# Equal-width Binning บน Temperature (4 Bins)
temp_discretizer = KBinsDiscretizer(
    n_bins=4,
    encode='ordinal',
    strategy='uniform',  # ← Equal Width
    subsample=None
)

df['Temperature_bins'] = temp_discretizer.fit_transform(df[['Temperature']]).astype(int)
df[['Temperature', 'Temperature_bins']]
```

**ผลลัพธ์:**

| Temperature | Temperature_bins | ความหมาย |
|-------------|-----------------|---------|
| 19.7 | **1** | ช่วงอุณหภูมิปานกลางต่ำ |
| 17.5 | **0** | ช่วงอุณหภูมิต่ำ |
| 24.9 | **3** | ช่วงอุณหภูมิสูง |
| 25.9 | **3** | ช่วงอุณหภูมิสูง |

> ⚠️ **ข้อเสีย:** ถ้าข้อมูลกระจุกตัวอยู่บางช่วง บาง Bin อาจมีข้อมูลน้อยมาก

---

## ✤ Equal Frequency Binning (แบ่งจำนวนข้อมูลเท่ากัน)

**แนวคิด:** แบ่งให้แต่ละ Bin มีจำนวนข้อมูลเท่ากัน (หรือใกล้เคียงกัน)

```python
# Equal-frequency Binning บน Crowdedness (4 Bins)
temp_discretizer = KBinsDiscretizer(
    n_bins=4,
    encode='ordinal',
    strategy='quantile',  # ← Equal Frequency
    subsample=None
)

df['Crowdedness_bins'] = temp_discretizer.fit_transform(df[['Crowdedness']]).astype(int)
df[['Crowdedness', 'Crowdedness_bins']]
```

**ผลลัพธ์:**

| Crowdedness | Crowdedness_bins | ความหมาย |
|-------------|-----------------|---------|
| 0.16 | **0** | ฝูงชนน้อย |
| 0.46 | **1** | ฝูงชนปานกลางต่ำ |
| 0.60 | **2** | ฝูงชนปานกลางสูง |
| 0.75 | **3** | ฝูงชนมาก |

> ✅ **ข้อดี:** แต่ละ Bin มีข้อมูลเท่ากัน → โมเดลไม่ Bias ไปหา Bin ที่มีข้อมูลมาก

---

## ✤ Custom Binning (กำหนดขอบเขตเอง)

```python
# กำหนดขอบเขตเองตามความเข้าใจ Domain
bins = [0, 20, 22, 24, float('inf')]  # ขอบเขต Bins
labels = [0, 1, 2, 3]  # Label/หมายเลขของแต่ละ Bin

df['Temperature_custom'] = pd.cut(
    df['Temperature'],
    bins=bins,
    labels=labels,
    include_lowest=True
).astype(int)
```

> 💡 เหมาะมากเมื่อมี Domain Knowledge เช่น รู้ว่าอุณหภูมิ > 24°C คือร้อนมาก

---

## 🆚 เปรียบเทียบ Equal Width vs Equal Frequency

| หัวข้อ | Equal Width | Equal Frequency |
|---|---|---|
| **แนวคิด** | ความกว้างเท่ากัน | จำนวนข้อมูลเท่ากัน |
| **sklearn strategy** | `'uniform'` | `'quantile'` |
| **ข้อดี** | เข้าใจง่าย, ช่วงชัดเจน | แต่ละ Bin มีข้อมูลสมดุล |
| **ข้อเสีย** | ข้อมูลในแต่ละ Bin อาจไม่สมดุล | ขอบเขต Bin ตีความยาก |
| **เหมาะกับ** | ข้อมูลกระจายสม่ำเสมอ | ข้อมูลเบ้ หรือมี Outlier |

---

## 💡 ตัวอย่างการใช้งานจริง

| ตัวอย่าง | ข้อมูลเดิม | หลัง Discretize |
|---|---|---|
| อายุลูกค้า | 23, 45, 67, 12 | เด็ก, วัยกลางคน, ผู้สูงอายุ, เด็ก |
| รายได้ | 15000, 50000, 200000 | Low, Medium, High |
| คะแนนสอบ | 85, 72, 55, 91 | A, B, C, A |
| อุณหภูมิ | 17.5, 21.0, 25.9 | เย็น, ปานกลาง, ร้อน |

---

> 🔗 **บทก่อนหน้า:** [Categorical Encoding ←](./02-3-data-preprocessing-categorical-encoding-TH.md)  
> 🔗 **บทถัดไป:** [Scaling & Normalization →](./02-5-data-preprocessing-scaling-TH.md)
