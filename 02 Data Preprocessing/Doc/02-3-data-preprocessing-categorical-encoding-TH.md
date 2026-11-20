# 🏷️ Categorical Encoding (การเข้ารหัสข้อมูลหมวดหมู่)

> **ไฟล์นี้เป็นคำแปลภาษาไทยของ Notebook: `02-3-data-preprocessing-categorical-encoding.ipynb`**  
> Series 2: Data Preprocessing 🔍

---

## 📌 Categorical Data คืออะไร?

**Categorical Data** คือข้อมูลที่แบ่งออกเป็น **หมวดหมู่ (Categories)** หรือ **กลุ่ม (Groups)**

### ประเภทของ Categorical Data:

| ประเภท | คำอธิบาย | ตัวอย่าง |
|---|---|---|
| **Nominal** | ไม่มีลำดับ ไม่มีความสำคัญมากน้อย | sunny, overcast, rainy |
| **Ordinal** | มีลำดับ มีความสำคัญมากน้อย | Low < Medium < High < Very High |
| **Binary** | มีแค่ 2 ค่า | Yes/No, True/False, 0/1 |

---

## 📌 Encoding คืออะไร?

**Encoding** คือการ **แปลงข้อมูลหมวดหมู่ (ข้อความ) ให้เป็นตัวเลข**

**ทำไมต้องแปลง?**  
เพราะ Machine Learning อ่านเข้าใจแค่ **ตัวเลข** เท่านั้น!

```
"sunny"    → [1, 0, 0]   ← โมเดลอ่านได้
"overcast" → [0, 1, 0]   ← โมเดลอ่านได้
"rainy"    → [0, 0, 1]   ← โมเดลอ่านได้
```

---

## 📊 ข้อมูลที่ใช้: Golf Dataset (All Categorical)

```python
import pandas as pd

golf_dataset = pd.read_csv("golf_dataset_mini_allcategorical.csv")
df = golf_dataset.copy()
df
```

**ตัวอย่างข้อมูล:**

| Date | Weekday | Temperature | Humidity | Windy | Outlook | Crowdedness |
|------|---------|-------------|----------|-------|---------|-------------|
| 03-18 | Fri | Low | Dry | Yes | sunny | High |
| 03-21 | Mon | Low | Humid | Yes | overcast | High |
| 03-22 | Tue | High | Dry | No | sunny | Very High |
| 04-02 | Sat | High | Humid | Yes | rainy | Very Low |

> 🔍 ทุกคอลัมน์เป็นข้อความ! ต้องแปลงเป็นตัวเลขทั้งหมด

---

## 🛠️ ประเภทของ Categorical Encoding

```
ประเภท Encoding
│
├── ❖ One-Hot Encoding     → แปลงเป็นคอลัมน์ 0/1 (สำหรับ Nominal)
├── ❖ Label Encoding       → แปลงเป็นตัวเลข 0, 1, 2... (เรียบง่าย)
├── ❖ Ordinal Encoding     → แปลงตามลำดับ (สำหรับ Ordinal)
└── ❖ Target Encoding      → แปลงด้วยความสัมพันธ์กับ Target
```

---

## ❖ One-Hot Encoding

**One-Hot Encoding** แปลงค่าหมวดหมู่เป็น **คอลัมน์ใหม่ 0/1**:

- แต่ละ Category → ได้คอลัมน์ใหม่ 1 คอลัมน์
- ถ้า Category ตรงกัน → ค่า = 1, ไม่ตรง → ค่า = 0

### ตัวอย่าง: Encode `Outlook` (3 Categories: sunny, overcast, rainy)

```python
# One-Hot Encoding บนคอลัมน์ Outlook
df_outlook_one_hot = pd.get_dummies(df['Outlook'], prefix='Outlook').astype(int)

pd.concat([df['Outlook'], df_outlook_one_hot], axis=1)
```

**ผลลัพธ์:**

| Outlook | Outlook_overcast | Outlook_rainy | Outlook_sunny |
|---------|----------|-------|-------|
| sunny | 0 | 0 | **1** |
| overcast | **1** | 0 | 0 |
| rainy | 0 | **1** | 0 |

> 💡 Outlook 1 คอลัมน์ → กลายเป็น **3 คอลัมน์**

### ตัวอย่าง: Encode `Windy` (Binary: Yes/No)

```python
# สำหรับ Binary ใช้ drop_first=True เพื่อลดคอลัมน์ที่ซ้ำซ้อน
df_windy_one_hot = pd.get_dummies(df['Windy'], prefix='Windy', drop_first=True).astype(int)

pd.concat([df['Windy'], df_windy_one_hot], axis=1)
```

**ผลลัพธ์:**

| Windy | Windy_Yes |
|-------|-----------|
| Yes | **1** |
| No | **0** |

> 💡 `drop_first=True` ช่วยหลีกเลี่ยง **Dummy Variable Trap** (คอลัมน์ที่ซ้ำซ้อน)

### ⚠️ ข้อควรระวัง One-Hot Encoding:
- ถ้ามีหมวดหมู่มาก (เช่น 100 จังหวัด) → จะได้คอลัมน์เพิ่มอีก 100 คอลัมน์!
- เหมาะกับข้อมูลที่มีหมวดหมู่ไม่มาก (< 10-15 categories)

---

## ❖ Label Encoding

**Label Encoding** แปลงค่า Category เป็นตัวเลขลำดับ (0, 1, 2, ...)

```python
from sklearn.preprocessing import LabelEncoder

le = LabelEncoder()
df['Outlook_Label'] = le.fit_transform(df['Outlook'])
```

**ผลลัพธ์:**

| Outlook | Outlook_Label |
|---------|---------------|
| sunny | **2** |
| overcast | **0** |
| rainy | **1** |

> ⚠️ **ข้อเสีย:** โมเดลอาจคิดว่า rainy (1) < sunny (2) ซึ่งอาจไม่ถูกต้อง  
> → ใช้กับข้อมูลที่มีลำดับ (Ordinal) เท่านั้น หรือกับ Tree-based Models

---

## ❖ Ordinal Encoding

สำหรับข้อมูลที่มีลำดับ (Ordinal) ต้องระบุลำดับเองให้ถูกต้อง

```python
from sklearn.preprocessing import OrdinalEncoder

# กำหนดลำดับของ Temperature
temp_order = [['Very Low', 'Low', 'High', 'Very High']]
ordinal_encoder = OrdinalEncoder(categories=temp_order)
df['Temperature_Ordinal'] = ordinal_encoder.fit_transform(df[['Temperature']])
```

**ผลลัพธ์:**

| Temperature | Temperature_Ordinal |
|-------------|---------------------|
| Very Low | **0** |
| Low | **1** |
| High | **2** |
| Very High | **3** |

> ✅ ถูกต้อง! เพราะ Very Low (0) < Low (1) < High (2) < Very High (3) สมเหตุสมผล

---

## ❖ Target Encoding

แปลงค่า Category โดยใช้ **ค่าเฉลี่ยของ Target** ในแต่ละ Category

```python
# Target Encoding: แปลง Outlook โดยใช้ค่าเฉลี่ย Crowdedness ในแต่ละ Outlook
target_mean = df.groupby('Outlook')['Crowdedness_numeric'].mean()
df['Outlook_Target'] = df['Outlook'].map(target_mean)
```

> 💡 มีประสิทธิภาพดีสำหรับข้อมูลที่มีหมวดหมู่มาก แต่ต้องระวัง **Data Leakage**

---

## 📊 สรุปเปรียบเทียบ: เลือก Encoding วิธีไหน?

| วิธี | เหมาะกับ | ข้อดี | ข้อเสีย |
|---|---|---|---|
| **One-Hot** | Nominal (ไม่มีลำดับ) | ไม่สร้าง Order ปลอม | หมวดมากมาก = คอลัมน์มาก |
| **Label** | Binary หรือ Tree Models | ง่าย ประหยัดคอลัมน์ | โมเดลอาจตีความผิด |
| **Ordinal** | Ordinal (มีลำดับ) | สะท้อนลำดับที่แท้จริง | ต้องกำหนดลำดับเอง |
| **Target** | Nominal ที่มีหมวดมาก | แม่นยำ | เสี่ยง Data Leakage |

---

## 🎯 หลักการเลือก Encoding

```
ข้อมูล Categorical
│
├── Binary (Yes/No, True/False)
│   └── → Label Encoding หรือ One-Hot (drop_first=True)
│
├── Nominal (ไม่มีลำดับ, หมวดน้อย)
│   └── → One-Hot Encoding
│
├── Nominal (ไม่มีลำดับ, หมวดมาก)
│   └── → Target Encoding
│
└── Ordinal (มีลำดับ)
    └── → Ordinal Encoding
```

---

> 🔗 **บทก่อนหน้า:** [Missing Values ←](./02-2-data-preprocessing-missing-values-TH.md)  
> 🔗 **บทถัดไป:** [Discretization →](./02-4-data-preprocessing-discretization-TH.md)
