# 🛠 Data Science Pipeline คืออะไร?

> **ไฟล์นี้เป็นคำแปลภาษาไทยของ Notebook: `01-2-data-science-pipeline.ipynb`**  
> สำหรับใช้ประกอบการสอนนักเรียน ชุด Series 1: Data Science Pipeline 🛠  
> 📌 ตัวอย่างทั้งหมดใช้ **Golf Play Dataset** (ชุดข้อมูลการเล่นกอล์ฟ)

---

## 📋 ขั้นตอนในกระบวนการ Data Science มีอะไรบ้าง?

กระบวนการ Data Science แบ่งออกเป็น **7 ขั้นตอนหลัก**:

```
⓵ Define Problem        → กำหนดปัญหา
⓶ Collect & Store Data  → เก็บรวบรวมข้อมูล
⓷ Clean & Preprocess    → ทำความสะอาดและจัดเตรียมข้อมูล
⓸ Explore Data          → สำรวจและวิเคราะห์ข้อมูล (EDA)
⓹ Train Model           → สร้างและฝึกโมเดล
⓺ Evaluate & Interpret  → ประเมินและตีความผลลัพธ์
⓻ Deploy & Monitor      → นำไปใช้งานจริงและติดตามผล
```

---

## 🎯 ความสำคัญของ Data Science Pipeline

Pipeline เปรียบเสมือน **แผนที่นำทาง** สำหรับ Data Scientist — ทำให้:

- ✅ งานมีระเบียบและทำซ้ำได้ (Reproducible)
- ✅ ง่ายต่อการแก้ไขและปรับปรุง
- ✅ ทีมสามารถทำงานร่วมกันได้ง่ายขึ้น
- ✅ ลดโอกาสเกิดข้อผิดพลาด

---

## ⛳️ แนะนำ Dataset: Golf Play Dataset

ใน Notebook นี้จะใช้ **Golf Play Dataset** เป็นตัวอย่าง

Dataset นี้บันทึกข้อมูลสภาพอากาศและการตัดสินใจว่าจะออกไปเล่นกอล์ฟหรือไม่

| คอลัมน์ | ความหมาย | ประเภทข้อมูล |
|---|---|---|
| `Outlook` | สภาพท้องฟ้า (sunny/overcast/rain) | Categorical (ข้อความ) |
| `Temperature` | อุณหภูมิ (หน่วย°F) | Numerical (ตัวเลข) |
| `Humidity` | ความชื้น (%) | Numerical (ตัวเลข) |
| `Wind` | มีลมแรงหรือไม่ (0=ไม่มี, 1=มี) | Binary (0/1) |
| `Play` | เล่นกอล์ฟหรือไม่ (0=ไม่เล่น, 1=เล่น) | Binary (0/1) ← **Target** |

> 🎯 **เป้าหมาย:** สร้างโมเดลทำนายว่าควรออกไปเล่นกอล์ฟหรือไม่ จากสภาพอากาศ

---

## ⓵ กำหนดปัญหา (Define Problem)

### รู้จักบริบทของธุรกิจ (Know Business Context)
ก่อนเริ่มทำงาน ต้องเข้าใจ **ทำไม** ถึงต้องแก้ปัญหานี้ และ **ใคร** คือผู้ที่จะได้รับประโยชน์

### กำหนดเป้าหมายหลัก (Set Primary Goal)
ระบุให้ชัดเจนว่าต้องการ **ผลลัพธ์อะไร** เช่น:
- "ต้องการทำนายว่าลูกค้าจะซื้อสินค้าหรือไม่"
- "ต้องการค้นหากลุ่มลูกค้าที่มีพฤติกรรมคล้ายกัน"

### กำหนดเป้าหมายรอง (Set Secondary Goal)
เป้าหมายเพิ่มเติมที่อยากได้ แต่ไม่ใช่สิ่งที่สำคัญที่สุด

### เข้าใจข้อจำกัด (Understand Constraints)
รับรู้ข้อจำกัดต่างๆ เช่น:
- ข้อมูลที่มีอยู่จำกัด
- ระยะเวลาในการทำงาน
- ทรัพยากรและงบประมาณ
- ข้อกำหนดด้านความเป็นส่วนตัวของข้อมูล (Privacy)

### งานอื่นๆ ที่ต้องทำ (Other Tasks)
- กำหนดเมตริกสำหรับวัดความสำเร็จ
- สื่อสารกับผู้มีส่วนได้ส่วนเสีย (Stakeholders)
- วางแผนการนำเสนอผลลัพธ์

---

## ⓶ เก็บรวบรวมข้อมูล (Collect & Store Data)

### หาแหล่งข้อมูล (Find Data Sources)
ข้อมูลอาจมาจากหลายแหล่ง:
- 🗄️ ฐานข้อมูลของบริษัท (Internal Databases)
- 🌐 แหล่งข้อมูลสาธารณะ เช่น Kaggle, government open data
- 🔌 API ของบริการต่างๆ
- 📋 การสำรวจและสัมภาษณ์

### โหลดข้อมูล (Collect the Data)

```python
import pandas as pd

# โหลดข้อมูล CSV
path = 'golf_dataset_the_origin.csv'  
df = pd.read_csv(path)
```

**ตัวอย่างข้อมูลที่ได้:**

| Outlook | Temperature | Humidity | Wind | Play |
|---------|-------------|----------|------|------|
| sunny | 85.0 | 85.0 | 0 | 0 |
| sunny | 80.0 | 90.0 | 1 | 0 |
| overcast | 83.0 | 78.0 | 0 | 1 |
| rain | 70.0 | 96.0 | 0 | 1 |
| ... | ... | ... | ... | ... |

> 📌 Dataset มีทั้งหมด **14 แถว** และ **5 คอลัมน์**

### บันทึกข้อมูล (Store Data)
ในตัวอย่างนี้ข้อมูลถูกบันทึกในรูปแบบ CSV ซึ่งเป็นรูปแบบที่ใช้งานง่าย

---

## ⓷ ทำความสะอาดและจัดเตรียมข้อมูล (Clean & Preprocess Data)

### ตรวจสอบเบื้องต้น (Initial Check)

```python
# ดูข้อมูลทั่วไป: จำนวนแถว, ชนิดข้อมูล, ค่า null
print(df.info())

# ดูชนิดข้อมูลของแต่ละคอลัมน์
print(df.dtypes)

# ดูขนาดของ DataFrame (จำนวนแถว × คอลัมน์)
print(df.shape)  # ได้ผลลัพธ์: (14, 5)
```

**ผลลัพธ์ที่ได้:**
```
Info:
RangeIndex: 14 entries, 0 to 13
Data columns (total 5 columns):
 #   Column       Non-Null Count  Dtype  
---  ------       --------------  -----  
 0   Outlook      14 non-null     object    ← ข้อความ
 1   Temperature  14 non-null     float64   ← ตัวเลขทศนิยม
 2   Humidity     14 non-null     float64   ← ตัวเลขทศนิยม
 3   Wind         14 non-null     int64     ← จำนวนเต็ม
 4   Play         14 non-null     int64     ← จำนวนเต็ม
```

### ตรวจสอบค่าที่หายไป (Check Missing Values)

```python
# นับจำนวน missing values ในแต่ละคอลัมน์
missing_values = df.isna().sum()
print(missing_values)

# แสดงเฉพาะคอลัมน์ที่มี missing values
print(missing_values[missing_values > 0])
```

**ผลลัพธ์ที่ได้:**
```
Outlook        0   ✅ ไม่มีค่าหาย
Temperature    0   ✅ ไม่มีค่าหาย
Humidity       0   ✅ ไม่มีค่าหาย
Wind           0   ✅ ไม่มีค่าหาย
Play           0   ✅ ไม่มีค่าหาย
```

> 🎉 ข้อมูลชุดนี้สะอาด ไม่มี Missing Values!

### ตรวจสอบ Outliers (Check Outliers)

**วิธีที่ 1: Boxplot** — ดูภาพรวมการกระจายของข้อมูล
```python
import matplotlib.pyplot as plt

df.boxplot(figsize=(12, 6))
plt.show()
```

**วิธีที่ 2: Z-Score** — ตรวจหาจุดที่ห่างจากค่าเฉลี่ยมากผิดปกติ
```python
from scipy import stats
import numpy as np

z_scores = np.abs(stats.zscore(df.select_dtypes(include=[np.number])))

# ถ้า Z-Score > 1.6 ถือว่าเป็น Outlier
outliers = (z_scores > 1.6)
print(df[outliers.any(axis=1)])
```

> 💡 **Outlier คืออะไร?** ข้อมูลที่มีค่าผิดปกติอย่างมาก เช่น ข้อมูลที่บันทึกผิด หรือเหตุการณ์พิเศษ

### แปลงหมวดหมู่เป็นตัวเลข (Convert Categories)

Machine Learning ต้องการข้อมูลตัวเลข แต่คอลัมน์ `Outlook` เป็นข้อความ จึงต้องแปลงด้วย **One-Hot Encoding**:

```python
# One-Hot Encoding: แปลง 'Outlook' เป็นคอลัมน์ 0/1
dummy_vars = pd.get_dummies(df['Outlook'], prefix='Outlook').astype(int)
df_concat = pd.concat([dummy_vars, df], axis=1)
df_concat.drop('Outlook', axis=1, inplace=True)
print(df_concat)
```

**ผลลัพธ์ที่ได้:**

| Outlook_overcast | Outlook_rain | Outlook_sunny | Temperature | Humidity | Wind | Play |
|---|---|---|---|---|---|---|
| 0 | 0 | 1 | 85.0 | 85.0 | 0 | 0 |
| 1 | 0 | 0 | 83.0 | 78.0 | 0 | 1 |
| 0 | 1 | 0 | 70.0 | 96.0 | 0 | 1 |

> 💡 คอลัมน์ `Outlook` 1 คอลัมน์ → กลายเป็น 3 คอลัมน์ (overcast, rain, sunny)

### ทำให้ตัวเลขอยู่ในระดับเดียวกัน (Standardize Numbers)

```python
from sklearn.preprocessing import StandardScaler

scaler = StandardScaler()
scaled_values = scaler.fit_transform(df_concat[['Temperature', 'Humidity']]).round(2)
df_concat[['Temperature', 'Humidity']] = scaled_values
```

> 💡 **ทำไมต้อง Standardize?** เพราะ Temperature (64-85) และ Humidity (65-96) มีหน่วยและขนาดต่างกัน การทำ Standardization ทำให้โมเดลไม่เอนเอียงไปหาตัวเลขที่ใหญ่กว่า

### เลือก Features ที่จะใช้ (Select Features)

```python
# ในกรณีนี้ทุกคอลัมน์ใช้ได้หมด ไม่ต้องตัดทิ้ง
# All columns seems usable and clean!
```

---

## ⓸ สำรวจข้อมูล (Explore Data / EDA)

EDA (Exploratory Data Analysis) คือการ **สำรวจข้อมูลเพื่อทำความเข้าใจ** ก่อนสร้างโมเดล

### คำนวณสถิติเบื้องต้น (Compute Descriptive Statistics)

```python
df.describe()
```

**ผลลัพธ์ที่ได้:**

| | Temperature | Humidity | Wind | Play |
|---|---|---|---|---|
| **count** | 14 | 14 | 14 | 14 |
| **mean (ค่าเฉลี่ย)** | 73.57 | 80.29 | 0.43 | 0.64 |
| **std (ส่วนเบี่ยงเบนมาตรฐาน)** | 6.57 | 9.84 | 0.51 | 0.50 |
| **min (ค่าต่ำสุด)** | 64 | 65 | 0 | 0 |
| **max (ค่าสูงสุด)** | 85 | 96 | 1 | 1 |

> 📊 จากค่า mean ของ Play = 0.64 แสดงว่า **64% ของวัน** เล่นกอล์ฟ

### ดูการกระจายของข้อมูล (Examine Data Distributions)
- ดูว่าข้อมูลกระจายตัวอย่างไร (สมมาตรหรือเบ้)
- ใช้ Histogram, Boxplot, หรือ Density Plot

### หาความสัมพันธ์ระหว่าง Features (Find Relationships Between Features)
- ใช้ Correlation Matrix
- ใช้ Scatter Plot

### สำรวจข้อมูลหมวดหมู่ (Explore Categorical Data)
- นับความถี่ของแต่ละหมวดหมู่
- ดูการกระจายของ Target ในแต่ละหมวดหมู่

---

## ⓹ สร้างและฝึกโมเดล (Train Model)

### เลือก Algorithm (Choose the Algorithm)

ขึ้นอยู่กับประเภทของปัญหา:

| ประเภทปัญหา | ตัวอย่าง Algorithm |
|---|---|
| **Classification (จัดหมวดหมู่)** | Decision Tree, Logistic Regression, Random Forest |
| **Regression (พยากรณ์ตัวเลข)** | Linear Regression, Ridge, Lasso |
| **Clustering (จัดกลุ่ม)** | K-Means, DBSCAN |

> 💡 สำหรับ Golf Dataset: ปัญหาของเราคือ **Classification** (เล่นหรือไม่เล่น)

### ใช้ Feature Selection (Use Feature Selection Algorithms)
เลือกเฉพาะ Features ที่มีผลต่อการทำนาย เพื่อลด Noise และเพิ่มประสิทธิภาพ

### แบ่งข้อมูล Train/Test (Split the Data)
```
ข้อมูลทั้งหมด → แบ่งเป็น:
  ├── Training Set (80%) ← ใช้ฝึกโมเดล
  └── Test Set (20%)     ← ใช้ทดสอบโมเดล
```

> ⚠️ **สำคัญมาก!** ต้องแบ่ง Test Set ออกก่อนแล้วเก็บไว้ ห้ามให้โมเดล "เห็น" ข้อมูล Test จนกว่าจะถึงเวลาทดสอบจริง

### ฝึกโมเดล (Train the Algorithm)
นำ Training Set ให้โมเดลเรียนรู้รูปแบบของข้อมูล

---

## ⓺ ประเมินและตีความผลลัพธ์ (Evaluate & Interpret Model)

### เลือก Evaluation Metric (Select Evaluation Metric)

| Metric | ใช้เมื่อ |
|---|---|
| **Accuracy** | ข้อมูลสมดุล (จำนวนแต่ละ Class ใกล้เคียงกัน) |
| **Precision / Recall** | ข้อมูลไม่สมดุล หรือต้องระวังผลบวก/ลบปลอม |
| **F1 Score** | ค่าเฉลี่ยของ Precision และ Recall |
| **RMSE** | ใช้กับ Regression |

### ทดสอบกับข้อมูลที่ไม่เคยเห็น (Test Data)
นำ Test Set มาทดสอบโมเดล เพื่อวัดว่าโมเดลทำนายได้ดีแค่ไหนกับข้อมูลใหม่

### ประเมินผลโมเดล (Evaluate the Model)
- คำนวณ Metric ที่เลือกไว้
- เปรียบเทียบกับ Baseline (เช่น การทายสุ่ม)

### ตีความผลลัพธ์ (Interpret the Result)
- โมเดลดีหรือยัง?
- มี Feature ไหนที่สำคัญที่สุด?
- ผลลัพธ์สมเหตุสมผลกับบริบทธุรกิจหรือไม่?

### Cross-Validation
แบ่งข้อมูลหลายรอบเพื่อทดสอบความเสถียรของโมเดล (ไม่ให้ผลดีแค่บางครั้ง)

### ปรับแต่งโมเดล (Tune the Model)
ปรับ **Hyperparameters** ของโมเดลเพื่อให้ได้ประสิทธิภาพที่ดีขึ้น

### ทำซ้ำกระบวนการ (Repeat the Training Process)
Data Science เป็นกระบวนการ **วนซ้ำ (Iterative)** — ปรับแล้วทดสอบ แล้วปรับอีก

### เปรียบเทียบกับโมเดลอื่น (Compare with Other Models)
ลองหลายๆ Algorithm เพื่อหาตัวที่ดีที่สุดสำหรับปัญหาของเรา

---

## ⓻ นำไปใช้งานจริงและติดตามผล (Deploy & Monitor)

### เลือกวิธี Deploy (Choose Deployment Method)

| วิธี | เหมาะกับ |
|---|---|
| **REST API** | Web App, Mobile App |
| **Batch Processing** | ประมวลผลข้อมูลจำนวนมากเป็นชุด |
| **Embedded Model** | ใส่ไว้ในอุปกรณ์ IoT หรือ Edge Device |
| **Dashboard** | การรายงานผลแบบ Real-time |

### งานอื่นๆ ที่ต้องทำ (Other Tasks)
- **ติดตามประสิทธิภาพ** — ดูว่าโมเดลยังทำงานได้ดีไหมในข้อมูลจริง
- **Data Drift** — ตรวจจับว่าลักษณะข้อมูลเปลี่ยนแปลงหรือไม่
- **Re-training** — ฝึกโมเดลใหม่เมื่อข้อมูลเปลี่ยนไป
- **A/B Testing** — ทดสอบโมเดลใหม่กับโมเดลเก่า

---

## 🗺️ ภาพรวมทั้งหมดของ Data Science Pipeline

```
🎯 1. Define Problem
      ↓
📦 2. Collect & Store Data
      ↓
🧹 3. Clean & Preprocess Data
      ├── Initial Check (ตรวจสอบข้อมูล)
      ├── Handle Missing Values (จัดการค่าหาย)
      ├── Handle Outliers (จัดการ Outliers)
      ├── Convert Categories → Numbers (One-Hot Encoding)
      ├── Standardize Numbers (Scaling)
      └── Select Features (เลือก Features)
      ↓
🔍 4. Explore Data (EDA)
      ├── Descriptive Statistics
      ├── Data Distributions
      ├── Correlations
      └── Categorical Analysis
      ↓
🤖 5. Train Model
      ├── Choose Algorithm
      ├── Feature Selection
      ├── Split Train/Test
      └── Train the Model
      ↓
📈 6. Evaluate & Interpret
      ├── Select Metric
      ├── Test on Test Data
      ├── Evaluate Performance
      ├── Interpret Results
      ├── Cross-Validation
      ├── Tune Hyperparameters
      └── Compare Models
      ↓
🚀 7. Deploy & Monitor
      ├── Choose Deployment Method
      └── Monitor & Maintain
      
      (วนซ้ำเมื่อจำเป็น ↩️)
```

---

## 📝 สรุปแต่ละขั้นตอน

| ขั้นตอน | ทำอะไร | เครื่องมือที่ใช้บ่อย |
|---|---|---|
| **1. Define Problem** | กำหนดเป้าหมายและข้อจำกัด | การประชุม, เอกสาร |
| **2. Collect Data** | รวบรวมข้อมูลจากแหล่งต่างๆ | `pd.read_csv()`, SQL, API |
| **3. Clean Data** | จัดการ Missing, Outliers, Encoding | `pandas`, `sklearn` |
| **4. Explore Data** | ทำความเข้าใจรูปแบบข้อมูล | `matplotlib`, `seaborn` |
| **5. Train Model** | สร้างและฝึก ML Model | `sklearn`, `tensorflow` |
| **6. Evaluate** | วัดประสิทธิภาพ | `accuracy_score`, `confusion_matrix` |
| **7. Deploy** | นำไปใช้งานจริง | Flask, FastAPI, Docker |

---

## 💡 ข้อสังเกตสำคัญ

> ⚠️ **Pipeline ไม่ได้เป็นเส้นตรง!**  
> ในความเป็นจริง เราอาจต้องย้อนกลับไปทำขั้นตอนก่อนหน้าบ่อยครั้ง เช่น:
> - พบว่าข้อมูลไม่พอ → กลับไป Collect เพิ่ม
> - โมเดลไม่ดี → กลับไปทำ Feature Engineering ใหม่
> - ผลลัพธ์ไม่ตรงโจทย์ธุรกิจ → กลับไป Define Problem ใหม่

---

> 🔗 **บทก่อนหน้า:** [What is Data Science ←](./01-1-what-is-data-science-TH.md)
