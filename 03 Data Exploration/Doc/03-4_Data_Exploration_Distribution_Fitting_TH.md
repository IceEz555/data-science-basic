
# ⛳️ 03-4 · 🔭 การสำรวจข้อมูล · การ Fitting การแจกแจง (Distribution Fitting)

> แปลและอธิบายจาก: [Kaggle - 03-4 Data Exploration: Distribution Fitting](https://www.kaggle.com/code/samybaladram/03-4-data-exploration-distribution-fitting)  
> ชุดข้อมูล: **⛳️ Golf Play Dataset Extended**

---

## 📌 บทนำ

ในบทก่อนหน้า เราได้เรียนรู้เรื่อง **ประเภทของการแจกแจง (Distribution Types)** ซึ่งช่วยให้เราระบุลักษณะรูปร่างของข้อมูลได้ บทนี้เราจะก้าวไปอีกขั้น นั่นคือ **การ Fitting การแจกแจง** (Distribution Fitting) ซึ่งหมายถึงการนำ **แบบจำลองทางคณิตศาสตร์** ไปพอดีกับข้อมูลที่มีอยู่จริง

**เป้าหมายหลัก:**
- เข้าใจว่าข้อมูลของเราอาจ "ตรงกับ" การแจกแจงรูปแบบใด
- ใช้ Python คำนวณค่าพารามิเตอร์ที่เหมาะสม (เช่น ค่าเฉลี่ย ส่วนเบี่ยงเบนมาตรฐาน)
- วาดกราฟเปรียบเทียบข้อมูลจริงกับการแจกแจงที่ Fit แล้ว

---

## 📦 ไลบรารีและชุดข้อมูล

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from scipy.stats import norm, lognorm, expon
```

### ชุดข้อมูล Golf Play Dataset
ชุดข้อมูลนี้บันทึกข้อมูลเกี่ยวกับ **การเล่นกอล์ฟในแต่ละวัน** ประกอบด้วยตัวแปรต่างๆ เช่น:
- `Outlook` – สภาพอากาศ (Sunny / Overcast / Rain)
- `Temperature` – อุณหภูมิ (°F)
- `Humidity` – ความชื้น (%)
- `Wind` – ลม (True/False)
- `WindSpeed` – ความเร็วลม (mph)
- `WindDirection` – ทิศทางลม (°)
- `TotalPlayer` – จำนวนผู้เล่นทั้งหมด
- `TimePerRound` – เวลาต่อรอบ (นาที)
- `Play` – เล่นหรือไม่ (Yes/No)

---

## 🔭 แนวคิด: Distribution Fitting คืออะไร?

**Distribution Fitting** = การหาว่าข้อมูลของเรา "เหมาะกับ" การแจกแจงทางสถิติรูปแบบใดมากที่สุด

### ขั้นตอนหลัก:
1. **สังเกตรูปร่างของข้อมูล** จาก Histogram หรือ KDE Plot
2. **เลือกการแจกแจงที่เหมาะสม** (Normal, Lognormal, Exponential ฯลฯ)
3. **Fit พารามิเตอร์** โดยใช้ `scipy.stats`
4. **วาดเส้นการแจกแจงทับบน Histogram** เพื่อดูว่า Fit ดีแค่ไหน

---

## 📊 การ Fitting การแจกแจงแบบต่างๆ

### 1. 🌡️ Temperature — Lognormal Distribution

```python
from scipy.stats import lognorm

sample_data = df['Temperature']

# วาด Histogram พร้อม KDE
sns.histplot(sample_data, bins=19, kde=True, edgecolor='white', stat="density", color='#FF9502')

# กำหนดช่วงของแกน x
xmin, xmax = plt.xlim()
x = np.linspace(xmin, xmax, 100)

# Fit Lognormal Distribution
shape, loc, scale = lognorm.fit(sample_data, floc=0)
p_lognorm = lognorm.pdf(x, shape, loc, scale)
plt.plot(x, p_lognorm, 'k--', linewidth=3, alpha=0.7)

plt.show()
```

#### 🔍 การตีความผลลัพธ์ (Temperature - Lognormal)

จากกราฟ Histogram ของ **Temperature** และเส้น Lognormal ที่ Fit:

- **รูปร่างกราฟ**: ข้อมูลอุณหภูมิของชุดข้อมูลกอล์ฟ มีลักษณะ **เบ้ขวาเล็กน้อย** (Right-skewed) คือมีค่าอุณหภูมิต่ำมีความถี่มากกว่า และมีค่าสูงกระจายออกไปทางขวา
- **เหตุใดถึงเลือก Lognormal?**: Lognormal Distribution เหมาะกับข้อมูลที่:
  - มีค่าเป็นบวกเสมอ (θm > 0)
  - มีการกระจายแบบเบ้ขวา
  - เกิดขึ้นตามธรรมชาติ เช่น อุณหภูมิ, ราคา, รายได้
- **เส้น Lognormal** (เส้นประสีดำ) พอดีกับข้อมูลได้ค่อนข้างดีในช่วงกลาง อธิบายได้ว่า **อุณหภูมิที่มักจะพบบ่อย** อยู่ในช่วงกลาง และมีโอกาสน้อยที่จะพบอุณหภูมิต่ำมากหรือสูงมากในวันที่มีการเล่นกอล์ฟ

---

### 2. 🌬️ WindDirection — Normal Distribution

```python
from scipy.stats import norm

sample_data = df['WindDirection']

# วาด Histogram พร้อม KDE
sns.histplot(sample_data, bins=19, kde=True, edgecolor='white', stat="density", color='#F87171')

# กำหนดช่วงของแกน x
xmin, xmax = plt.xlim()
x = np.linspace(xmin, xmax, 100)

# Fit Normal Distribution
mu, std = norm.fit(sample_data)
p_norm = norm.pdf(x, mu, std)
plt.plot(x, p_norm, 'k--', linewidth=3, alpha=0.7)

plt.show()
```

#### 🔍 การตีความผลลัพธ์ (WindDirection - Normal)

- **ลักษณะข้อมูล**: ทิศทางลม (WindDirection) ถูกวัดเป็นองศา มีการกระจายที่ค่อนข้าง **สมมาตร**
- **Normal Distribution** เหมาะกับข้อมูลที่:
  - มีการกระจายแบบสมมาตรรอบค่ากลาง
  - ข้อมูลส่วนใหญ่กระจุกอยู่ใกล้ค่าเฉลี่ย
  - ข้อมูลที่ห่างจากค่าเฉลี่ยมากมีความถี่น้อย
- **ค่า mu (μ)** = ทิศทางลมที่พบบ่อยที่สุด (ค่าเฉลี่ย) ซึ่งอยู่ประมาณกลางช่วง
- **ค่า std (σ)** = ความกว้างของการกระจาย ยิ่ง std มาก ข้อมูลยิ่งกระจายไกลจากค่าเฉลี่ย

---

### 3. 💨 WindSpeed — Exponential Distribution

```python
from scipy.stats import expon

sample_data = df['WindSpeed']

# วาด Histogram พร้อม KDE
sns.histplot(sample_data, bins=19, kde=True, edgecolor='white', stat="density", color='#34D399')

# กำหนดช่วงของแกน x
xmin, xmax = plt.xlim()
x = np.linspace(xmin, xmax, 100)

# Fit Exponential Distribution
loc, scale = expon.fit(sample_data, floc=0)
p_expon = expon.pdf(x, loc, scale)
plt.plot(x, p_expon, 'k--', linewidth=3, alpha=0.7)

plt.show()
```

#### 🔍 การตีความผลลัพธ์ (WindSpeed - Exponential)

- **ลักษณะข้อมูล**: ความเร็วลม (WindSpeed) มักจะ **เบ้ขวาอย่างมาก** — ส่วนใหญ่ของวันมีลมอ่อนหรือปานกลาง แต่บางวันมีลมแรงมาก
- **Exponential Distribution** เหมาะกับข้อมูลที่:
  - มีค่าเป็นบวกเสมอ
  - ลดลงอย่างรวดเร็ว (Decay ตามช่วงเวลา/ปริมาณ)
  - พบบ่อยในข้อมูลเวลา (เช่น เวลารอ) หรือปริมาณธรรมชาติ
- **ข้อสังเกต**: ความเร็วลมเป็น 0 บ่อยที่สุด (ไม่มีลม) และมีโอกาสลดลงเรื่อยๆ เมื่อความเร็วสูงขึ้น ซึ่งสอดคล้องกับ Exponential Distribution

---

## 📋 สรุป: เลือก Distribution อย่างไร?

| ตัวแปร | Distribution ที่เลือก | เหตุผล |
|---|---|---|
| Temperature | Lognormal | บวกเสมอ, เบ้ขวาเล็กน้อย |
| WindDirection | Normal | สมมาตร, กระจุกรอบค่าเฉลี่ย |
| WindSpeed | Exponential | บวกเสมอ, เบ้ขวาอย่างมาก, decay |

### หลักการทั่วไปในการเลือก Distribution:

1. **Normal** → ข้อมูลสมมาตร, ค่าเฉลี่ยกลาง
2. **Lognormal** → ข้อมูลบวกเสมอ, เบ้ขวาเล็กน้อย
3. **Exponential** → ข้อมูลบวกเสมอ, เบ้ขวามาก, ลดลงเร็ว
4. **Uniform** → ข้อมูลกระจายสม่ำเสมอในช่วงหนึ่ง
5. **Binomial/Bernoulli** → ข้อมูลที่มีแค่สองค่า (0/1, Yes/No)

---

## 🔬 การประเมินคุณภาพของ Fit

หลังจาก Fit Distribution แล้ว เราควรประเมินว่า Fit ดีแค่ไหน:

### วิธีการทั่วไป:
1. **การดูด้วยตา** → วาดเส้น Distribution ทับ Histogram แล้วดูว่าพอดีไหม
2. **Q-Q Plot (Quantile-Quantile Plot)** → เปรียบเทียบ Quantile ของข้อมูลกับ Quantile ของ Distribution ที่เลือก หากข้อมูลอยู่บนเส้นตรง = Fit ดี
3. **Kolmogorov-Smirnov Test (KS Test)** → ทดสอบทางสถิติว่าข้อมูลมาจาก Distribution ที่กำหนดหรือไม่

```python
from scipy.stats import kstest

# ทดสอบว่า WindSpeed Fit Exponential ได้ดีไหม
loc, scale = expon.fit(df['WindSpeed'], floc=0)
stat, p_value = kstest(df['WindSpeed'], 'expon', args=(loc, scale))
print(f"KS Statistic: {stat:.4f}, p-value: {p_value:.4f}")
# ถ้า p-value > 0.05 → ไม่มีหลักฐานเพียงพอที่จะปฏิเสธว่าข้อมูลมาจาก Exponential
```

---

## 💡 ทำไม Distribution Fitting ถึงสำคัญ?

1. **การทำนาย (Prediction)**: เมื่อรู้ Distribution ของข้อมูล เราสามารถประมาณความน่าจะเป็นของเหตุการณ์ต่างๆ ได้
2. **การจำลอง (Simulation)**: ใช้ Distribution ในการสร้างข้อมูลสมมติที่มีลักษณะคล้ายข้อมูลจริง
3. **การวิเคราะห์ความเสี่ยง (Risk Analysis)**: เข้าใจโอกาสที่จะเกิดค่าสุดโต่ง
4. **การสร้างโมเดล ML**: บางอัลกอริทึม (เช่น Naive Bayes, Linear Regression) มีสมมติฐานเกี่ยวกับ Distribution ของข้อมูล

---

## 📈 ตัวอย่างการใช้งาน: คำนวณความน่าจะเป็น

เมื่อ Fit Normal Distribution ให้ WindDirection ได้แล้ว เราสามารถตอบคำถามเช่น:

```python
mu, std = norm.fit(df['WindDirection'])

# ความน่าจะเป็นที่ WindDirection จะอยู่ระหว่าง 100 ถึง 200 องศา
prob = norm.cdf(200, mu, std) - norm.cdf(100, mu, std)
print(f"P(100 <= WindDirection <= 200) ≈ {prob:.4f} = {prob*100:.1f}%")
```

---

## 🎯 สรุปบทเรียน

| แนวคิด | รายละเอียด |
|---|---|
| Distribution Fitting | การหาแบบจำลองทางสถิติที่พอดีกับข้อมูล |
| `scipy.stats` | ไลบรารี Python สำหรับ Fit Distribution |
| `.fit()` method | คำนวณพารามิเตอร์ที่ Fit กับข้อมูลมากที่สุด |
| `.pdf()` method | คำนวณ Probability Density Function |
| KS Test | ทดสอบความดีของ Fit ทางสถิติ |

> **ข้อควรจำ**: ไม่มี Distribution ใดที่ "สมบูรณ์แบบ" สำหรับข้อมูลจริง เราเพียงหาการแจกแจงที่ "ดีพอ" (Good Enough) เพื่อใช้ในการวิเคราะห์และทำนายต่อไป
