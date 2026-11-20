# 🕳️ Missing Values (ค่าที่หายไป)

> **ไฟล์นี้เป็นคำแปลภาษาไทยของ Notebook: `02-2-data-preprocessing-missing-values.ipynb`**  
> Series 2: Data Preprocessing 🔍

---

## 📌 Missing Values คืออะไร?

**Missing Values** คือ **ค่าที่ขาดหายไป** ในข้อมูล — ช่องว่างในตาราง ที่ไม่มีข้อมูลบันทึกอยู่

ใน Python/Pandas แสดงด้วย **`NaN`** (Not a Number)

### สาเหตุของ Missing Values:
- 📝 ลืมกรอกข้อมูล / ไม่สะดวกตอบ
- ⚠️ ข้อผิดพลาดในการเก็บข้อมูล (Sensor Error)
- 🔗 การรวม Dataset จากหลายแหล่ง
- ❌ ข้อมูลบางคอลัมน์ไม่เกี่ยวข้องกับแถวนั้น

---

## ⚠️ ทำไม Missing Values ถึงเป็นปัญหา?

Missing Values สร้างปัญหาให้กับ Machine Learning เพราะ:

1. **โมเดลส่วนใหญ่ไม่รับค่า NaN** → รัน Code แล้ว Error
2. **ทำให้ผลการคำนวณผิดพลาด** → ค่าเฉลี่ย, ความสัมพันธ์ระหว่าง Features ผิดเพี้ยน
3. **Bias ในโมเดล** → ถ้าค่าที่หายไปไม่ได้หายแบบสุ่ม อาจทำให้โมเดลเรียนรู้ผิดทิศ

---

## 📊 ข้อมูลที่ใช้: Golf Dataset (มี Missing Values เยอะ)

```python
import pandas as pd

golf_dataset = pd.read_csv("golf_dataset_mini_lotsofmissingvalues.csv")
df = golf_dataset.copy()
df
```

**ตัวอย่างข้อมูล:**

| Date | Weekday | Holiday | Temp | Humidity | Wind | Outlook | Crowdedness |
|------|---------|---------|------|----------|------|---------|-------------|
| 08-01 | 0 | 0.0 | 25.1 | 99.0 | 0.0 | rainy | 0.14 |
| 08-02 | 1 | 0.0 | 26.4 | **NaN** | 0.0 | sunny | **NaN** |
| 08-03 | 2 | 0.0 | **NaN** | 96.0 | 0.0 | rainy | 0.21 |
| 08-05 | 4 | **NaN** | 24.7 | 98.0 | 0.0 | rainy | 0.20 |
| 08-11 | 3 | 0.0 | **NaN** | **NaN** | 0.0 | **NaN** | **NaN** |

> ⚠️ Row 10 (08-11) มี Missing Values ถึง 4 คอลัมน์!

---

## 🛠️ วิธีจัดการ Missing Values

มี **4 วิธีหลัก** ในการจัดการ Missing Values:

```
วิธีจัดการ Missing Values
│
├── ❯ Listwise Deletion    → ลบแถวที่มีค่าหาย
├── ❯ Mean/Median/Mode     → เติมด้วยค่ากลาง
├── ❯ Forward/Backward     → เติมด้วยค่าก่อนหน้า/ถัดไป
└── ❯ Model-based          → ใช้โมเดลทำนายค่าที่หาย
```

---

## ❯ วิธีที่ 1: Listwise Deletion (ลบแถวที่มีค่าหาย)

### เมื่อไหร่ควรใช้?
- แถวที่มี Missing Values มาก (ข้อมูลน้อยเกินไปที่จะเดา)
- จำนวนแถวที่ลบออกไม่มากจนเกินไป

```python
# ลบแถวที่มี Missing Values มากกว่าหรือเท่ากับ 3 คอลัมน์
df.dropna(thresh=df.shape[1] - 3 + 1, inplace=True)
df
```

**ผลลัพธ์:**
- Row 10 (08-11) ถูกลบออก เพราะมี 4 ค่าที่หาย
- Row 17 (08-18) ถูกลบออก เพราะมี 4 ค่าที่หาย
- เหลือ 18 แถว จากเดิม 20 แถว

> 💡 `thresh=N` หมายความว่า "เก็บแถวที่มีค่าที่ไม่ใช่ NaN อย่างน้อย N ค่า"

---

## ❯ วิธีที่ 2: Imputation Using Mean / Median / Mode

การ **Imputation** คือการ **เดาค่าที่หาย** แล้วเติมลงไป

| ประเภทข้อมูล | ใช้ค่า | เหมาะเมื่อ |
|---|---|---|
| ตัวเลขทั่วไป | **Mean (ค่าเฉลี่ย)** | ข้อมูลกระจายสมมาตร ไม่มี Outlier |
| ตัวเลขที่มี Outlier | **Median (มัธยฐาน)** | ข้อมูลเบ้ หรือมี Outlier |
| ข้อความ/หมวดหมู่ | **Mode (ฐานนิยม)** | ข้อมูล Categorical |

### Mean Imputation (เติมด้วยค่าเฉลี่ย)

```python
# เติมค่าที่หายของ Temp ด้วยค่าเฉลี่ย
df['Temp'].fillna(df['Temp'].mean(), inplace=True)
```

### Median Imputation (เติมด้วยค่ากลาง)

```python
# เติมค่าที่หายของ Humidity ด้วยค่า Median
df['Humidity'].fillna(df['Humidity'].median(), inplace=True)
```

### Mode Imputation (เติมด้วยค่าที่พบบ่อยที่สุด)

```python
# เติมค่าที่หายของ Outlook ด้วย Mode
df['Outlook'].fillna(df['Outlook'].mode()[0], inplace=True)
```

---

## ❯ วิธีที่ 3: Forward Fill / Backward Fill

ใช้กับ **ข้อมูล Time Series** — เติมค่าจากข้อมูลก่อนหน้า (ffill) หรือถัดไป (bfill)

```python
# Forward Fill: เติมด้วยค่าของแถวก่อนหน้า
df['Wind'].fillna(method='ffill', inplace=True)

# Backward Fill: เติมด้วยค่าของแถวถัดไป
df['Holiday'].fillna(method='bfill', inplace=True)
```

**ตัวอย่าง Forward Fill:**

| วัน | Wind |
|---|---|
| 08-09 | 0.0 |
| 08-10 | **NaN** → **0.0** (เติมจาก 08-09) |
| 08-11 | 0.0 |

> 💡 เหมาะกับข้อมูลที่เปลี่ยนแปลงช้า เช่น อุณหภูมิ, ความดันอากาศ

---

## ❯ วิธีที่ 4: Model-based Imputation (KNN)

ใช้ **Machine Learning** ทำนายค่าที่หาย โดยดูจากข้อมูลของแถวที่ใกล้เคียงที่สุด

```python
from sklearn.impute import KNNImputer

# ใช้ K=5 (ดูจาก 5 แถวที่ใกล้เคียงที่สุด)
knn_imputer = KNNImputer(n_neighbors=5)
df_imputed = knn_imputer.fit_transform(df[['Temp', 'Humidity', 'Wind']])
```

> 💡 แม่นยำกว่า Mean/Median แต่ใช้เวลาคำนวณมากกว่า

---

## 📊 เปรียบเทียบวิธีทั้งหมด

| วิธี | ข้อดี | ข้อเสีย | เหมาะกับ |
|---|---|---|---|
| **Listwise Deletion** | ง่าย ข้อมูลสะอาด | สูญเสียข้อมูล | Missing น้อย |
| **Mean Imputation** | ง่าย รวดเร็ว | ลด Variance | ข้อมูลสมมาตร |
| **Median Imputation** | ทนต่อ Outlier | ลด Variance | มี Outlier |
| **Mode Imputation** | ใช้กับ Categorical | อาจไม่แม่นยำ | ข้อความ/หมวดหมู่ |
| **Forward/Backward Fill** | เหมาะ Time Series | ใช้ได้เฉพาะงาน | ข้อมูลอนุกรมเวลา |
| **KNN Imputation** | แม่นยำที่สุด | ช้า, ต้องการ RAM | ข้อมูลซับซ้อน |

---

## 💡 แนวทางในการเลือกวิธี

```
ขั้นตอนการตัดสินใจ:

1. มี Missing มากแค่ไหน?
   ├── > 50% ของแถว → ลองลบคอลัมน์นั้นออก
   └── < 20% ของแถว → เลือก Imputation

2. ข้อมูลเป็นประเภทอะไร?
   ├── Categorical → Mode
   └── Numerical → Mean / Median / KNN

3. ข้อมูลมี Outlier ไหม?
   ├── มี Outlier → Median
   └── ไม่มี Outlier → Mean

4. เป็น Time Series ไหม?
   └── ใช่ → Forward/Backward Fill
```

---

> 🔗 **บทก่อนหน้า:** [Introduction ←](./02-1-data-preprocessing-introduction-TH.md)  
> 🔗 **บทถัดไป:** [Categorical Encoding →](./02-3-data-preprocessing-categorical-encoding-TH.md)
