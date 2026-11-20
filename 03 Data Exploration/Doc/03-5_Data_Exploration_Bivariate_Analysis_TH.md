
# ⛳️ 03-5 · 🔭 การสำรวจข้อมูล · การวิเคราะห์สองตัวแปร (Bivariate Analysis)

> แปลและอธิบายจาก: [Kaggle - 03-5 Data Exploration: Bivariate Analysis](https://www.kaggle.com/code/samybaladram/03-5-data-exploration-bivariate-analysis)  
> ชุดข้อมูล: **⛳️ Golf Play Dataset Extended** (365 แถว × 10 คอลัมน์)

---

## 📌 บทนำ

ในบทก่อนๆ เราได้ศึกษาตัวแปรแต่ละตัวแยกกัน (Univariate Analysis) แต่บทนี้เราจะก้าวไปอีกขั้น นั่นคือ **การวิเคราะห์สองตัวแปรพร้อมกัน (Bivariate Analysis)** ซึ่งช่วยให้เราเข้าใจ **ความสัมพันธ์** ระหว่างตัวแปรสองตัวว่ามีความเกี่ยวข้องกันอย่างไร

---

## 📊 ชุดข้อมูลและไลบรารี

```python
import numpy as np
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt

golf_dataset = pd.read_csv("./Data/golf_dataset_weather_tropical_1year.csv")
df = golf_dataset[['Date','Outlook'] + golf_dataset.select_dtypes(include=['number']).columns.tolist()].copy()
```

### ตัวอย่างข้อมูล (5 แถวแรก):

| Date | Outlook | SunshineDuration | Temperature | Humidity | WindDirection | WindSpeed | Pressure | TotalPlayer | TimePerRound |
|---|---|---|---|---|---|---|---|---|---|
| 2021-01-01 | rainy | 1.7 | 27.2 | 79 | 62.0 | 1.3 | 1010.3 | 28 | 206 |
| 2021-01-02 | sunny | 11.8 | 33.4 | 70 | 350.0 | 0.1 | 1013.2 | 74 | 453 |
| 2021-01-03 | sunny | 13.1 | 31.3 | 75 | 345.3 | 2.2 | 1005.5 | 63 | 290 |
| 2021-01-04 | overcast | 0.8 | 26.8 | 68 | 304.2 | 2.3 | 1009.6 | 56 | 298 |
| 2021-01-05 | sunny | 5.1 | 30.8 | 59 | 0.1 | 6.2 | 1011.3 | 70 | 213 |

*(365 แถว × 10 คอลัมน์)*

---

## 🔭 Bivariate Analysis คืออะไร?

**Bivariate Analysis** = การวิเคราะห์ความสัมพันธ์ระหว่างตัวแปร **2 ตัว** พร้อมกัน

### ประเภทของคู่ตัวแปร:

| ตัวแปรที่ 1 | ตัวแปรที่ 2 | เทคนิคที่ใช้ |
|---|---|---|
| Numerical (ตัวเลข) | Numerical (ตัวเลข) | Scatter Plot, Correlation |
| Categorical (หมวดหมู่) | Numerical (ตัวเลข) | Box Plot, Bar Plot |
| Categorical (หมวดหมู่) | Categorical (หมวดหมู่) | Cross-tabulation, Heatmap |

---

## 📈 ส่วนที่ 1: Numerical × Numerical (ตัวเลข × ตัวเลข)

### Scatter Plot (กราฟกระจาย)

**Scatter Plot** ใช้เห็นความสัมพันธ์ระหว่างตัวแปรตัวเลขสองตัว โดยแต่ละจุดในกราฟแทนข้อมูล 1 แถว

```python
sns.scatterplot(data=df, x='Temperature', y='TotalPlayer', color='#FF9502')
plt.show()
```

#### 🔍 การตีความผลลัพธ์ (Temperature vs TotalPlayer)

จากกราฟ Scatter Plot ระหว่าง **อุณหภูมิ (Temperature)** และ **จำนวนผู้เล่น (TotalPlayer)**:

- **แนวโน้มหลัก**: เมื่ออุณหภูมิสูงขึ้น มักพบว่ามีผู้เล่นมากขึ้น แสดงถึง **ความสัมพันธ์เชิงบวก (Positive Correlation)**
- **ความกระจาย**: จุดข้อมูลมีการกระจายค่อนข้างมาก แสดงว่าอุณหภูมิไม่ใช่ปัจจัยเดียวที่ควบคุมจำนวนผู้เล่น
- **ช่วงอุณหภูมิ**: ส่วนใหญ่อยู่ระหว่าง 22-35°C
- **ช่วงผู้เล่น**: อยู่ระหว่าง 0-80+ คน

---

### Correlation (ค่าสัมประสิทธิ์สหสัมพันธ์)

**Correlation** วัดความสัมพันธ์เชิงเส้นตรงระหว่างตัวแปร ค่าอยู่ในช่วง **-1 ถึง +1**:

| ค่า | ความหมาย |
|---|---|
| +1 | สัมพันธ์เชิงบวกสมบูรณ์ |
| 0 | ไม่มีความสัมพันธ์เชิงเส้น |
| -1 | สัมพันธ์เชิงลบสมบูรณ์ |

```python
# คำนวณ Correlation Matrix
corr_matrix = df.select_dtypes(include=['number']).corr()
print(corr_matrix)

# วาด Heatmap
sns.heatmap(corr_matrix, annot=True, cmap='coolwarm', center=0)
plt.show()
```

#### 🔍 การตีความผลลัพธ์ (Correlation Matrix)

จาก Heatmap ของ Correlation Matrix:

- **TotalPlayer กับ Temperature**: ค่า Correlation สูง (ประมาณ +0.6 ถึง +0.8) → อุณหภูมิที่สูงสัมพันธ์กับผู้เล่นมากขึ้น
- **TotalPlayer กับ SunshineDuration**: ค่า Correlation สูง → วันที่มีแดดมากมีผู้เล่นมากขึ้น
- **TotalPlayer กับ Humidity**: ค่า Correlation ต่ำหรือเป็นลบ → ความชื้นสูงทำให้มีผู้เล่นน้อยลง
- **TimePerRound กับ TotalPlayer**: ค่า Correlation สูงเชิงบวก → ยิ่งมีผู้เล่นมาก ใช้เวลาต่อรอบมากขึ้น

---

## 📦 ส่วนที่ 2: Categorical × Numerical (หมวดหมู่ × ตัวเลข)

### Box Plot (กราฟกล่อง)

**Box Plot** เปรียบเทียบการกระจายของตัวแปรตัวเลขในแต่ละกลุ่มของตัวแปรหมวดหมู่

```python
sns.boxplot(data=df, x='Outlook', y='TotalPlayer', 
            palette={'sunny': '#FFD700', 'overcast': '#808080', 'rainy': '#4169E1'})
plt.show()
```

#### 🔍 การตีความผลลัพธ์ (Outlook vs TotalPlayer)

จาก Box Plot ระหว่าง **สภาพอากาศ (Outlook)** และ **จำนวนผู้เล่น (TotalPlayer)**:

- **Sunny (อากาศแจ่มใส)**:
  - ค่ากลาง (Median) สูงที่สุด ประมาณ 60-70 คน
  - การกระจายกว้าง แสดงว่าวันแจ่มใสบางวันมีผู้เล่นน้อยมาก บางวันมากมาย
  
- **Overcast (มีเมฆมาก)**:
  - ค่ากลาง (Median) อยู่ระดับกลาง ประมาณ 45-55 คน
  - การกระจายปานกลาง
  
- **Rainy (ฝนตก)**:
  - ค่ากลาง (Median) ต่ำที่สุด ประมาณ 25-35 คน
  - มี Outlier บางจุด (วันฝนตกแต่มีคนมาเล่นมาก)

**ข้อสรุป**: สภาพอากาศมีผลชัดเจนต่อจำนวนผู้เล่นกอล์ฟ ❌ ฝนตก → ผู้เล่นน้อย ✅ แจ่มใส → ผู้เล่นมาก

---

### Bar Plot (กราฟแท่ง)

**Bar Plot** แสดงค่าเฉลี่ยของตัวแปรตัวเลขในแต่ละกลุ่ม

```python
sns.barplot(data=df, x='Outlook', y='TotalPlayer',
            palette={'sunny': '#FFD700', 'overcast': '#808080', 'rainy': '#4169E1'})
plt.show()
```

#### 🔍 การตีความผลลัพธ์ (Outlook vs TotalPlayer - Bar Plot)

- **Sunny**: ค่าเฉลี่ยผู้เล่นสูงที่สุด (ประมาณ 55-65 คน)
- **Overcast**: ค่าเฉลี่ยผู้เล่นปานกลาง (ประมาณ 40-50 คน)
- **Rainy**: ค่าเฉลี่ยผู้เล่นต่ำที่สุด (ประมาณ 25-35 คน)

เส้น Error Bar แสดง **ความเชื่อมั่น 95%** ของค่าเฉลี่ย

---

## 🗺️ ส่วนที่ 3: Categorical × Categorical (หมวดหมู่ × หมวดหมู่)

### Cross-tabulation (ตารางไขว้)

```python
# สมมติว่ามีตัวแปร Season (ฤดูกาล)
# ct = pd.crosstab(df['Outlook'], df['Season'])
# sns.heatmap(ct, annot=True, fmt='d')
```

---

## 📊 Scatter Plot Matrix (Pair Plot)

**Pair Plot** แสดง Scatter Plot ของทุกคู่ตัวแปรตัวเลขพร้อมกัน เหมาะสำหรับการสำรวจความสัมพันธ์หลายตัวแปรในคราวเดียว

```python
numerical_cols = df.select_dtypes(include=['number']).columns.tolist()
sns.pairplot(df[numerical_cols], corner=True, plot_kws={'alpha': 0.5})
plt.show()
```

#### 🔍 การตีความผลลัพธ์ (Pair Plot)

จาก Pair Plot ที่แสดงทุกคู่ตัวแปร:

- **แนวทแยง (Diagonal)**: แสดง Distribution ของแต่ละตัวแปร (เหมือน Histogram/KDE)
- **ส่วนล่างซ้าย**: แสดง Scatter Plot ของทุกคู่ตัวแปร

**ความสัมพันธ์ที่น่าสนใจ**:
- **Temperature ↑ → TotalPlayer ↑**: เห็นแนวโน้มเชิงบวกชัดเจน
- **SunshineDuration ↑ → TotalPlayer ↑**: วันที่มีแดดมาก ผู้เล่นมากขึ้น
- **TotalPlayer ↑ → TimePerRound ↑**: ยิ่งมีคนมาก ใช้เวลานานขึ้น
- **Humidity ↑ → TotalPlayer ↓**: ความชื้นสูงทำให้ผู้เล่นน้อยลง

---

## 🌡️ Heatmap

**Heatmap** แสดงความสัมพันธ์ด้วยสีสัน ช่วยให้เห็นรูปแบบได้ง่าย

```python
corr = df.select_dtypes(include=['number']).corr()
sns.heatmap(corr, 
            annot=True,        # แสดงตัวเลขในแต่ละช่อง
            cmap='coolwarm',   # สีแดง = บวก, สีน้ำเงิน = ลบ
            center=0,           # จุดกลางที่ 0
            fmt='.2f')          # แสดงทศนิยม 2 ตำแหน่ง
plt.show()
```

#### 🔍 การอ่านค่า Heatmap

| สี | ความหมาย |
|---|---|
| 🔴 แดงเข้ม | Correlation สูงเชิงบวก (+1) |
| ⚪ ขาว | ไม่มีความสัมพันธ์ (0) |
| 🔵 น้ำเงินเข้ม | Correlation สูงเชิงลบ (-1) |

---

## 🔬 การวิเคราะห์เชิงลึก: ทำไม Bivariate Analysis ถึงสำคัญ?

### 1. Feature Selection (การเลือกตัวแปร)
สำหรับ Machine Learning เราสามารถใช้ Correlation เพื่อ:
- เลือกตัวแปรที่มีความสัมพันธ์สูงกับ Target Variable
- ตัดตัวแปรที่ไม่เกี่ยวข้องออก
- ระบุ Multicollinearity (ตัวแปรอิสระที่สัมพันธ์กันเอง)

### 2. Business Insights (ข้อมูลเชิงธุรกิจ)
จากการวิเคราะห์พบว่า:
- **วันแจ่มใสและอุณหภูมิสูง** → เหมาะสำหรับจัดทัวร์นาเมนต์กอล์ฟ
- **วันฝนตก** → ควรมีกิจกรรมทดแทนหรือลดเจ้าหน้าที่
- **จำนวนผู้เล่นสูง** → ควรจัดสรรเวลาเพิ่มขึ้น

### 3. Data Cleaning
Correlation ช่วยระบุ:
- ตัวแปรที่อาจมีข้อมูลผิดปกติ (ถ้า Correlation ผิดจาก Domain Knowledge)
- ความสัมพันธ์ที่ไม่สมเหตุสมผล อาจเป็นสัญญาณว่ามีข้อมูลผิดพลาด

---

## 📋 สรุปบทเรียน

| แนวคิด | รายละเอียด |
|---|---|
| Bivariate Analysis | การวิเคราะห์ 2 ตัวแปรพร้อมกัน |
| Scatter Plot | แสดงความสัมพันธ์ระหว่างตัวเลข 2 ตัว |
| Correlation | วัดความสัมพันธ์เชิงเส้นตรง (-1 ถึง +1) |
| Box Plot | เปรียบเทียบการกระจายระหว่างกลุ่ม |
| Heatmap | แสดง Correlation Matrix ด้วยสีสัน |
| Pair Plot | Scatter Plot ทุกคู่ตัวแปรในภาพเดียว |

### ขั้นตอนการวิเคราะห์ที่แนะนำ:
1. **สำรวจเบื้องต้น** ด้วย Pair Plot (ดูภาพรวม)
2. **ดู Correlation Matrix** เพื่อวัดความสัมพันธ์เป็นตัวเลข
3. **วาด Scatter Plot** ของคู่ตัวแปรที่สนใจ
4. **ใช้ Box Plot** เปรียบเทียบระหว่างกลุ่ม (สำหรับ Categorical-Numerical)
5. **ระบุ Pattern** และ **ตีความผลในบริบทธุรกิจ**

> **ข้อควรจำ**: Correlation ≠ Causation (ความสัมพันธ์ไม่ได้หมายความว่าเป็นเหตุเป็นผลกัน) ต้องตีความอย่างระมัดระวัง!
