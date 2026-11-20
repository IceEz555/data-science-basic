# 🔍 Data Preprocessing คืออะไร? (Introduction)

> **ไฟล์นี้เป็นคำแปลภาษาไทยของ Notebook: `02-1-data-preprocessing-introduction.ipynb`**  
> Series 2: Data Preprocessing 🔍

---

## 📌 Data Preprocessing คืออะไร?

**Data Preprocessing** หมายถึงกระบวนการ **เตรียมข้อมูลดิบ (Raw Data)** ให้พร้อมสำหรับวิเคราะห์หรือสร้างโมเดล Machine Learning

ข้อมูลดิบในโลกจริงมักมีปัญหาหลายอย่าง เช่น:
- ❌ ค่าที่หายไป (Missing Values)
- ❌ ข้อมูลที่ผิดปกติ (Outliers)
- ❌ ข้อมูลประเภทข้อความที่ต้องแปลงเป็นตัวเลข
- ❌ ตัวเลขที่มีขนาด (Scale) ต่างกันมาก

---

## 🌟 ความสำคัญของ Data Preprocessing

> **"Garbage in, Garbage out"**

ถ้าใส่ข้อมูลห่วยเข้าไป → โมเดลก็จะได้ผลลัพธ์ห่วยออกมา

การทำ Preprocessing ที่ดีช่วย:
- ✅ เพิ่มความแม่นยำของโมเดล
- ✅ ลดระยะเวลาการฝึกโมเดล
- ✅ ทำให้โมเดล Generalize ได้ดีขึ้น
- ✅ ป้องกันโมเดลเรียนรู้จากข้อมูลที่ผิดพลาด

---

## 📊 ข้อมูลที่ใช้ในตัวอย่าง

Dataset เป็นข้อมูล Golf Play ที่มีปัญหาจงใจ (ใช้สำหรับสาธิตการ Preprocessing)

```python
import pandas as pd

golf_dataset = pd.read_csv("golf_dataset_mini_unprocessed.csv")
df = golf_dataset.copy()
df
```

**ตัวอย่างข้อมูล (มีปัญหาหลายอย่างจงใจ):**

| Outlook | Temperature | Humidity | Wind | Play |
|---------|-------------|----------|------|------|
| sunny | 85.0 | 85.0 | 0 | 0 |
| sunny | **NaN** | 90.0 | 1 | 0 |
| overcast | 83.0 | 78.0 | 0 | 1 |
| rain | 70.0 | **10.0** ← Outlier! | 0 | 1 |
| **NaN** | 71.0 | 80.0 | 1 | 0 |

> 🔍 สังเกตว่ามีทั้ง Missing Values (NaN) และ Outliers (Humidity=10 ผิดปกติมาก)

---

## 🛠️ งานหลักของ Data Preprocessing

Data Preprocessing แบ่งออกเป็น **3 หมวดใหญ่**:

```
Data Processing Tasks
│
├── ◎ Data Cleaning (ทำความสะอาดข้อมูล)
│   ├── จัดการ Missing Values
│   └── จัดการ Outliers
│
├── ◎ Data Structuring (จัดโครงสร้างข้อมูล)
│   ├── Feature Engineering (สร้าง Features ใหม่)
│   └── Data Transformation
│
└── ◎ Data Reduction (ลดขนาดข้อมูล)
    ├── Feature Selection (เลือก Features ที่สำคัญ)
    └── Dimensionality Reduction
```

---

## ◎ Data Cleaning (ทำความสะอาดข้อมูล)

### 1. จัดการค่าที่หายไป (Handle Missing Values)

```python
# ลบแถวที่ Outlook ว่างเปล่า (ข้อมูลสำคัญเกินไปที่จะเดา)
df = df[df['Outlook'].notna()]

# เติมค่าที่หายของ Temperature ด้วยค่าเฉลี่ย (Mean Imputation)
df['Temperature'].fillna(df['Temperature'].mean(), inplace=True)

df[['Outlook', 'Temperature']]
```

**ผลลัพธ์:**
- แถวที่ Outlook เป็น NaN → ถูกลบออก (row 13)
- Temperature ที่ NaN → ถูกแทนด้วยค่าเฉลี่ย (73.25)

### 2. จัดการ Outliers

```python
from scipy.stats import zscore
import numpy as np

# คำนวณ Z-Score ของ Humidity
df['Humidity_zscore'] = zscore(df['Humidity'])

# หาแถวที่เป็น Outlier (Z-Score > 3)
outliers = df[np.abs(df['Humidity_zscore']) > 3]

# แทนค่า Outlier ด้วยค่าเฉลี่ยของกลุ่ม Outlook เดียวกัน
for index, row in outliers.iterrows():
    outlook = row['Outlook']
    avg_humidity = df[df['Outlook'] == outlook]['Humidity'].mean()
    df.loc[index, 'Humidity'] = avg_humidity

df.drop(['Humidity_zscore'], axis=1, inplace=True)
```

> 💡 Humidity = 10.0 ถูกตรวจจับว่าเป็น Outlier และถูกแทนด้วยค่าเฉลี่ยของ rain group

---

## ◎ Data Structuring (จัดโครงสร้างข้อมูล)

### Feature Engineering — การสร้าง Features ใหม่

บางครั้งเราสามารถสร้าง Feature ใหม่ที่มีความหมายมากขึ้นจาก Features เดิม

**ตัวอย่าง:** สร้าง `AirQualityIndex` จาก Temperature และ Humidity:

```python
# สร้าง Feature ใหม่: AirQualityIndex = ค่าเฉลี่ยของ Temperature และ Humidity
df['AirQualityIndex'] = (df['Temperature'] + df['Humidity']) / 2
```

| Temperature | Humidity | AirQualityIndex |
|---|---|---|
| 85.0 | 85.0 | **85.0** |
| 73.25 | 90.0 | **81.6** |
| 83.0 | 78.0 | **80.5** |

> 💡 Feature Engineering คือ "ศิลปะ" ที่ต้องอาศัยความเข้าใจ Domain เพื่อสร้าง Features ที่มีประโยชน์

---

## 📋 สรุป: งานใน Data Preprocessing

| หมวด | งาน | เครื่องมือ |
|---|---|---|
| **Data Cleaning** | จัดการ Missing Values | `fillna()`, `dropna()` |
| **Data Cleaning** | จัดการ Outliers | Z-Score, IQR |
| **Data Structuring** | Feature Engineering | การคำนวณ Feature ใหม่ |
| **Data Structuring** | การแปลงข้อมูล | Encoding, Scaling |
| **Data Reduction** | Feature Selection | Correlation, Feature Importance |

---

## 🗺️ ภาพรวม Series นี้

> ใน Series 2 นี้จะเรียนรู้แต่ละหัวข้อโดยละเอียด:

| บทที่ | หัวข้อ | เนื้อหา |
|---|---|---|
| **02-1** | Introduction | ภาพรวม Preprocessing ทั้งหมด |
| **02-2** | Missing Values | วิธีจัดการค่าที่หายไป |
| **02-3** | Categorical Encoding | แปลงข้อความเป็นตัวเลข |
| **02-4** | Discretization | แปลงตัวเลขเป็นช่วง (Bins) |
| **02-5** | Scaling & Normalization | ปรับขนาดตัวเลข |

---

> 🔗 **บทถัดไป:** [Missing Values →](./02-2-data-preprocessing-missing-values-TH.md)
