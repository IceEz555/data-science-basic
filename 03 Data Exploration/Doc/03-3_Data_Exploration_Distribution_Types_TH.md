# 📊 03-3: ประเภทของการกระจายตัวของข้อมูล (Distribution Types)

> แปลและสรุปจาก: [03-3-data-exploration-distribution-types](https://www.kaggle.com/code/samybaladram/03-3-data-exploration-distribution-types)  
> รวมเนื้อหาจากรูปภาพ Infographic ในหน้า Kaggle  
> ชุดข้อมูล: Golf Play Dataset

---

## 🎯 ภาพรวม

> 📌 **จากรูปภาพ Infographic**:  
> การวิเคราะห์การกระจายตัว (Distribution Analysis) มีประโยชน์ 2 อย่างหลัก:
> 1. **ตรวจสอบข้อสมมติ (Check Assumptions)** ของการทดสอบทางสถิติ
> 2. **มองเห็นการกระจายตัว (See the Spread)** ของข้อมูล

---

## 📈 Histogram vs. Density Plot

> 📌 **จากรูปภาพ Infographic**:

| | **Histogram** | **Density Plot (KDE)** |
|--|--------------|------------------------|
| แสดงอะไร | จำนวนที่แน่นอน (Exact Counts) ใน "Bins" | เส้นโค้งเรียบ (Smooth Curve) |
| เหมาะสำหรับ | ดูจำนวนความถี่จริงๆ | เปรียบเทียบหลายกลุ่มในกราฟเดียว |
| ข้อดี | ตรงไปตรงมา | อ่านง่าย เปรียบเทียบง่าย |

```python
# Histogram
sns.histplot(df['Temperature'], bins=19, kde=False, edgecolor="white")

# Histogram + KDE ทับกัน
sns.histplot(df['Temperature'], bins=19, kde=True, edgecolor="white")
plt.show()
```

---

## 📐 วิธีเลือกจำนวน Bins (Bin Selection Methods)

> 📌 **จากรูปภาพ Infographic** — มีสูตรหลายแบบ:

| วิธี | สูตร | ลักษณะ |
|------|------|--------|
| **Square-root** | √n | ใช้ง่าย เหมาะทั่วไป |
| **Sturges'** | log₂(n) + 1 | เหมาะกับ Normal Distribution |
| **Rice** | 2 × n^(1/3) | เหมาะกับชุดข้อมูลขนาดใหญ่ |
| **Scott's** | 3.5σ / n^(1/3) | พิจารณา SD ด้วย |
| **Freedman-Diaconis** | 2 × IQR / n^(1/3) | **แข็งแกร่งที่สุดต่อ Outlier** |

> 💡 **คำแนะนำ**: Freedman-Diaconis เป็นวิธีที่ดีที่สุดเมื่อข้อมูลมี Outlier

---

## 🔔 1. Normal Distribution (การกระจายแบบปกติ)

> 📌 **จากรูปภาพ Infographic**:  
> ✅ **ตัวอย่าง**: ส่วนสูง, อุณหภูมิ (Temperature)

### ลักษณะสำคัญ

- รูปร่างสมมาตร (**Bell-shaped**) รอบค่าเฉลี่ย
- **Mean = Median = Mode**
- กฎ 68-95-99.7:

| ช่วง | % ของข้อมูล |
|------|-------------|
| μ ± 1σ | 68% |
| μ ± 2σ | 95% |
| μ ± 3σ | 99.7% |

```python
sns.histplot(df['Temperature'], bins=19, kde=True, edgecolor="white", color="#7B8699")
plt.show()
```

---

## 📈 2. Log-Normal Distribution

> 📌 **จากรูปภาพ Infographic**:  
> ✅ **ตัวอย่าง**: รายได้ (Income), ความเร็วลม (Wind Speed)

### ลักษณะสำคัญ

- **Right-Skewed** (หางยาวทางขวา)
- ค่าทั้งหมดเป็น **ค่าบวก**
- ถ้านำ Logarithm ของข้อมูลมาพล็อต จะได้ Normal Distribution
- พบบ่อยมากในข้อมูลธรรมชาติและธุรกิจ

```python
# ln(X) ~ Normal Distribution
import numpy as np
log_wind = np.log(df['WindSpeed'])
sns.histplot(log_wind, kde=True)
plt.show()
```

---

## ⬇️ 3. Exponential Distribution

> 📌 **จากรูปภาพ Infographic**:  
> ✅ **ตัวอย่าง**: เวลารอ (Waiting Times) เช่น เวลาระหว่างลูกค้า

### ลักษณะสำคัญ

- ค่าลดลงเมื่อตัวเลขสูงขึ้น (Values **decrease** as they get larger)
- **Right-Skewed** อย่างมาก
- **Memoryless Property**: โอกาสในอนาคตไม่ขึ้นกับเวลาที่รอมาแล้ว
- ใช้ในการคำนวณเวลาระหว่างเหตุการณ์สุ่ม

**สูตร:** P(X = x) = λ × e^(-λx)

---

## 🎯 4. Poisson Distribution

> 📌 **จากรูปภาพ Infographic**:  
> ✅ **ตัวอย่าง**: จำนวนผู้เล่นที่เข้ามา (Player Arrivals) ในช่วงเวลาคงที่

### ลักษณะสำคัญ

- นับ **จำนวนเหตุการณ์** ที่เกิดขึ้นใน **ช่วงเวลาคงที่**
- เป็น **Discrete Distribution** (ตัวเลขนับ)
- **Mean = Variance = λ** (Lambda)

### ตัวอย่างในชีวิตจริง
- จำนวนโทรศัพท์ที่รับต่อชั่วโมง
- จำนวนลูกค้าเข้าร้านต่อวัน
- จำนวนข้อผิดพลาดในโปรแกรม

---

## 🎲 5. Binomial Distribution

> 📌 **จากรูปภาพ Infographic**:  
> ✅ **ตัวอย่าง**: ผลลัพธ์แบบ Binary เช่น "Yes/No" หรือ "Rain/No Rain"

### ลักษณะสำคัญ

- มี **ผลลัพธ์แค่ 2 แบบ**: สำเร็จ/ไม่สำเร็จ (Bernoulli Trials)
- ทดลองซ้ำ n ครั้ง แต่ละครั้งอิสระกัน
- ความน่าจะเป็นของการสำเร็จ = p คงที่ทุกครั้ง

### ตัวอย่างในชีวิตจริง
- โอกาสฝนตกในแต่ละวัน (Rain/No Rain)
- ผลสอบ (Pass/Fail)
- การทอยเหรียญ (Head/Tail)

---

## 📊 6. Uniform Distribution (การกระจายแบบสม่ำเสมอ)

> 📌 **จากรูปภาพ Infographic**:  
> ✅ **ตัวอย่าง**: การบันทึกวันที่ (Date Entries) ในชุดข้อมูลนี้

### ลักษณะสำคัญ

- ทุกค่ามีโอกาสเกิดขึ้น **เท่ากัน**
- กราฟเป็น **แท่งสี่เหลี่ยม** (Rectangular Shape)
- เหมาะกับ Random Sampling

---

## 🔍 สรุปเปรียบเทียบการกระจายตัวทั้งหมด

| ประเภท | รูปร่าง | ตัวอย่าง (จาก Infographic) | ใช้เมื่อ |
|--------|---------|--------------------------|---------|
| **Normal** | Bell Curve | ส่วนสูง, Temperature | ข้อมูลสมมาตร |
| **Log-Normal** | เบ้ขวา | Income, Wind Speed | ค่าบวก เบ้ขวา |
| **Exponential** | เบ้ขวามาก | Waiting Times | เวลาระหว่างเหตุการณ์ |
| **Poisson** | เบ้ขวา | Player Arrivals | นับเหตุการณ์/ช่วงเวลา |
| **Binomial** | สมมาตร/เบ้ | Yes/No, Rain/No Rain | ผลลัพธ์ 2 แบบ |
| **Uniform** | แท่งสี่เหลี่ยม | Date Entries | ทุกค่าเป็นไปได้เท่ากัน |

---

## 🛠️ วิธีตรวจสอบประเภทการกระจายตัว

### 1. ดูจาก Histogram
```python
sns.histplot(data, kde=True)
plt.show()
```

### 2. Q-Q Plot (Quantile-Quantile Plot)
```python
import scipy.stats as stats

stats.probplot(data, dist="norm", plot=plt)
plt.show()
```
→ ถ้าจุดอยู่บนเส้นตรง = ข้อมูลมี Normal Distribution

### 3. Skewness & Kurtosis
```python
print(f"Skewness: {df['column'].skew()}")
print(f"Kurtosis: {df['column'].kurt()}")
```

| ค่า Skewness | ความหมาย |
|-------------|----------|
| ≈ 0 | Symmetric (Normal) |
| > 0 | Right-Skewed |
| < 0 | Left-Skewed |

---

## 📝 สรุปบทเรียน

1. **Histogram** แสดงจำนวนจริง, **KDE** ดูรูปร่างและเปรียบเทียบง่ายกว่า
2. **Freedman-Diaconis** คือวิธีเลือกจำนวน Bins ที่ดีที่สุดเมื่อมี Outlier
3. **Normal** → สมมาตร เหมาะกับโมเดลส่วนใหญ่
4. **Log-Normal** → เบ้ขวา ค่าบวก พบบ่อยในข้อมูลรายได้/ราคา
5. **Exponential** → เวลาระหว่างเหตุการณ์สุ่ม
6. **Poisson** → นับเหตุการณ์ในช่วงเวลาคงที่
7. **Binomial** → ผลลัพธ์แบบ Yes/No
8. **Uniform** → ทุกค่ามีโอกาสเท่ากัน

---

*แปลและสรุปจาก Kaggle Notebook โดย samybaladram รวมเนื้อหาจาก Infographic ในหน้าเว็บ*  
*ชุดข้อมูล: Golf Play Dataset (ข้อมูลจำลองเกี่ยวกับการตัดสินใจเล่นกอล์ฟ)*
