# ⚖️ Scaling & Normalization (การปรับขนาดข้อมูล)

> **ไฟล์นี้เป็นคำแปลภาษาไทยของ Notebook: `02-5-data-preprocessing-scaling.ipynb`**  
> Series 2: Data Preprocessing 🔍

---

## 📌 Scaling คืออะไร?

**Scaling** (หรือ Normalization) คือการ **ปรับขนาดของตัวเลข** ให้อยู่ในช่วงที่เหมาะสม โดยที่ยังรักษา **รูปแบบการกระจาย (Distribution)** ของข้อมูลไว้

**ตัวอย่างปัญหา:**
```
Temperature: 17.5 – 25.9 °C   (ช่วงแคบ)
Crowdedness:  4 – 23 คน        (ช่วงกว้างกว่า)
Drive Distance: 150 – 582 เมตร (ช่วงกว้างมาก!)
```

> ⚠️ โมเดลอาจ "เชื่อ" ว่า Distance สำคัญกว่า Temperature เพียงเพราะตัวเลขใหญ่กว่า!

---

## 🌟 ทำไมต้อง Scale?

| เหตุผล | คำอธิบาย |
|---|---|
| **Algorithm ที่ใช้ Distance** | KNN, SVM, K-Means คำนวณระยะทาง → ตัวเลขใหญ่อาจครอบงำ |
| **Gradient Descent** | Neural Networks, Logistic Regression → ลู่เข้าเร็วขึ้น |
| **Regularization** | Ridge, Lasso → น้ำหนักของ Feature ต้องเทียบกันได้ |
| **PCA** | ต้องการให้ทุก Feature มี Scale เท่ากัน |

> ✅ **ไม่จำเป็นต้อง Scale สำหรับ:** Decision Tree, Random Forest (ไม่ขึ้นกับ Scale)

---

## 📊 ข้อมูลที่ใช้: Golf Dataset (Unscaled)

```python
import pandas as pd
import numpy as np

golf_dataset = pd.read_csv("golf_dataset_mini_unscaled.csv")
df = golf_dataset.copy()
df
```

**ตัวอย่างข้อมูล:**

| Date | Temperature | Humidity | Crowdedness | Estimated_Drive_Distance |
|------|-------------|----------|-------------|--------------------------|
| 06-16 | 19.7 | 77.0 | **13** | **198.5** |
| 06-25 | 21.0 | 82.0 | **19** | **283.5** |
| 07-01 | 25.9 | 83.0 | **16** | **435.5** |
| 07-04 | 22.4 | 84.0 | **11** | **541.0** |

> 🔍 สังเกตว่า Crowdedness (4-23) และ Drive Distance (150-582) มี Scale ต่างกันมาก!

---

## 🛠️ วิธีการ Scaling

```
Scaling Methods
│
├── ⚆ Min-Max Scaling     → ปรับให้อยู่ใน Range [0, 1]
├── ⚆ Standard Scaling    → ปรับให้ Mean=0, StdDev=1
└── ⚆ Robust Scaling      → ใช้ Median/IQR (ทนต่อ Outlier)
```

---

## ⚆ วิธีที่ 1: Min-Max Scaling

**สูตร:**
```
X_scaled = (X - X_min) / (X_max - X_min)
```

**ผลลัพธ์:** ค่าทุกตัวอยู่ใน **ช่วง [0, 1]**
- ค่าต่ำสุด → 0.0
- ค่าสูงสุด → 1.0

```python
from sklearn.preprocessing import MinMaxScaler

min_max_scaler = MinMaxScaler()
df['Crowdedness_MinMax'] = min_max_scaler.fit_transform(df[['Crowdedness']]).round(2)

df[['Crowdedness', 'Crowdedness_MinMax']]
```

**ผลลัพธ์:**

| Crowdedness | Crowdedness_MinMax |
|-------------|-------------------|
| 4 (น้อยสุด) | **0.00** |
| 13 | **0.47** |
| 23 (มากสุด) | **1.00** |

> ✅ **เหมาะกับ:** Neural Networks, Image Processing  
> ⚠️ **ข้อเสีย:** Outlier เพียงตัวเดียวสามารถบิดเบือน Scale ทั้งหมดได้

---

## ⚆ วิธีที่ 2: Standard Scaling (Z-Score)

**สูตร:**
```
X_scaled = (X - mean) / std_dev
```

**ผลลัพธ์:**
- **Mean (ค่าเฉลี่ย) = 0**
- **Standard Deviation = 1**
- ค่าจะอยู่แถวๆ -3 ถึง +3

```python
from sklearn.preprocessing import StandardScaler

std_scaler = StandardScaler()
df['Temperature_Standard'] = std_scaler.fit_transform(df[['Temperature']]).round(2)

df[['Temperature', 'Temperature_Standard']]
```

**ผลลัพธ์:**

| Temperature | Temperature_Standard | ความหมาย |
|-------------|---------------------|---------|
| 17.5 | **-1.34** | ต่ำกว่าค่าเฉลี่ย 1.34 SD |
| 21.0 | **0.03** | ใกล้ค่าเฉลี่ย |
| 25.9 | **1.95** | สูงกว่าค่าเฉลี่ย 1.95 SD |

> ✅ **เหมาะกับ:** Logistic Regression, SVM, Neural Networks  
> ✅ **ข้อดี:** ทนต่อ Outlier ได้ดีกว่า Min-Max

---

## ⚆ วิธีที่ 3: Robust Scaling

**สูตร:**
```
X_scaled = (X - median) / IQR
```

โดยที่ **IQR (Interquartile Range)** = Q3 - Q1

**ผลลัพธ์:**
- ใช้ Median และ IQR แทน Mean และ StdDev
- **ทนต่อ Outlier ได้ดีที่สุด**

```python
from sklearn.preprocessing import RobustScaler

robust_scaler = RobustScaler()
df['Distance_Robust'] = robust_scaler.fit_transform(df[['Estimated_Drive_Distance']]).round(2)

df[['Estimated_Drive_Distance', 'Distance_Robust']]
```

**ผลลัพธ์:**

| Drive_Distance | Distance_Robust |
|----------------|-----------------|
| 198.5 | **-0.93** |
| 303.5 (Median) | **0.00** |
| 435.5 | **0.67** |

> ✅ **เหมาะกับ:** ข้อมูลที่มี Outlier มาก

---

## 📊 เปรียบเทียบวิธี Scaling ทั้งหมด

| วิธี | สูตร | ช่วงผลลัพธ์ | ข้อดี | ข้อเสีย | เหมาะกับ |
|---|---|---|---|---|---|
| **Min-Max** | (X-min)/(max-min) | [0, 1] | เข้าใจง่าย | ไวต่อ Outlier | Image, NN |
| **Standard** | (X-mean)/std | แถว -3 ถึง +3 | ดีที่สุดทั่วไป | ยังได้รับผล Outlier | ส่วนใหญ่ |
| **Robust** | (X-median)/IQR | ไม่จำกัด | ทนต่อ Outlier | ตีความยาก | มี Outlier |

---

## 🎯 หลักการเลือกวิธี Scaling

```
มี Outlier ไหม?
│
├── ใช่ → Robust Scaling
│
└── ไม่มี Outlier
    │
    ├── ต้องการช่วง [0,1] → Min-Max Scaling
    │   (เช่น Neural Networks, Image Processing)
    │
    └── ไม่ต้องการช่วงตายตัว → Standard Scaling
        (เช่น Logistic Regression, SVM, PCA)
```

---

## 💡 สิ่งสำคัญที่ต้องจำ

> ⚠️ **Fit บน Training Set เท่านั้น!**  
> ต้อง `fit()` Scaler กับ Training Data เท่านั้น แล้วค่อย `transform()` ทั้ง Train และ Test

```python
from sklearn.preprocessing import StandardScaler
from sklearn.model_selection import train_test_split

X_train, X_test = train_test_split(df[['Temperature', 'Humidity']], test_size=0.2)

scaler = StandardScaler()

# ✅ ถูกต้อง: fit บน Train แล้ว transform ทั้งคู่
X_train_scaled = scaler.fit_transform(X_train)
X_test_scaled  = scaler.transform(X_test)   # ← ไม่ fit อีกครั้ง!

# ❌ ผิด: อย่า fit บน Test Set
# X_test_scaled = scaler.fit_transform(X_test)  ← Data Leakage!
```

> 💡 ถ้า `fit_transform()` บน Test Set → เกิด **Data Leakage** (โมเดลรู้ค่า min/max/mean ของ Test Set ล่วงหน้า)

---

> 🔗 **บทก่อนหน้า:** [Discretization ←](./02-4-data-preprocessing-discretization-TH.md)
