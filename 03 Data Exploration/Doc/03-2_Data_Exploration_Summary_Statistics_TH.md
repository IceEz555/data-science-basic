# 📊 03-2: สถิติสรุป (Summary Statistics)

> แปลและสรุปจาก: [03-2-data-exploration-summary-statistics](https://www.kaggle.com/code/samybaladram/03-2-data-exploration-summary-statistics)  
> รวมเนื้อหาจากรูปภาพ Infographic และการตีความผลลัพธ์  
> ชุดข้อมูล: Golf Play Dataset (365 แถว × 10 คอลัมน์)

---

## 🎯 ภาพรวม

**สถิติสรุป (Summary Statistics)** คือตัวเลขที่ใช้อธิบายภาพรวมของข้อมูลด้วยตัวเลขเพียงไม่กี่ตัว

> 📌 **จากรูปภาพ — ค่ากลาง (Central Tendency Overview)**: ให้ตัวเลขตัวเดียวบอกว่าข้อมูล "ส่วนใหญ่" อยู่ตรงไหน ทำให้เปรียบเทียบง่าย

แบ่งออกเป็น 2 กลุ่มใหญ่:

1. **ค่ากลาง (Measures of Central Tendency)** — ข้อมูลส่วนใหญ่อยู่ตรงไหน?
2. **การกระจายตัว (Measures of Dispersion & Frequency)** — ข้อมูลกระจายตัวมากน้อยแค่ไหน?

---

## 📋 ชุดข้อมูล Golf Play Dataset

ข้อมูลบันทึกรายวัน ปี 2021 (1 มกราคม — 31 ธันวาคม) รวม **365 แถว × 10 คอลัมน์**

| คอลัมน์ | ประเภท | ตัวอย่างค่า |
|---------|--------|------------|
| `Date` | วันที่ | 2021-01-01 |
| `Outlook` | Categorical | rainy, sunny, overcast |
| `SunshineDuration` | Numerical | 1.7 ชั่วโมง |
| `Temperature` | Numerical | 27.2°C |
| `Humidity` | Numerical | 79% |
| `WindDirection` | Numerical | 62.0° |
| `WindSpeed` | Numerical | 1.3 m/s |
| `Pressure` | Numerical | 1010.3 hPa |
| `TotalPlayer` | Numerical | 28 คน |
| `TimePerRound` | Numerical | 206 นาที |

---

## ➊ ค่ากลาง (Measures of Central Tendency)

### 📌 Mean (ค่าเฉลี่ย)

**Mean** คือผลรวมของข้อมูลทั้งหมดหารด้วยจำนวนข้อมูล

> 📌 **จากรูปภาพ Infographic**:  
> ✅ **เหมาะกับ**: ข้อมูลสมมาตร เช่น อุณหภูมิ (Temperature)  
> ❌ **ไม่เหมาะกับ**: ข้อมูลที่มี Outlier เช่น ความเร็วลม (WindSpeed)

**โค้ด Python:**
```python
df[['Temperature', 'Humidity', 'WindDirection', 'WindSpeed']].mean()
```

### 📊 ผลลัพธ์จริง:
```
Temperature      27.08       ← อุณหภูมิเฉลี่ย
Humidity         73.23       ← ความชื้นเฉลี่ย
WindDirection   184.25       ← ทิศทางลมเฉลี่ย
WindSpeed         2.81       ← ความเร็วลมเฉลี่ย
```

### 🔍 การตีความ:
| คอลัมน์ | ค่า Mean | ความหมาย |
|---------|----------|----------|
| Temperature | 27.08°C | วันทั่วไปอุณหภูมิประมาณ 27°C (อากาศร้อน) |
| Humidity | 73.23% | ความชื้นค่อนข้างสูง (เขตร้อนชื้น) |
| WindDirection | 184.25° | ลมเฉลี่ยพัดมาจากทางใต้ (180°) |
| WindSpeed | 2.81 m/s | ลมเบา-ปานกลาง แต่อาจมี Outlier ดึง Mean ขึ้น |

---

### 📌 Median (มัธยฐาน)

**Median** คือค่ากลางเมื่อเรียงข้อมูลจากน้อยไปมาก

> 📌 **จากรูปภาพ Infographic**:  
> ✅ **เหมาะกับ**: ข้อมูลเบ้ (Skewed Data) หรือมี Outlier เช่น WindSpeed, TotalPlayer, TimePerRound  
> 💡 **เหตุผล**: ความเร็วลม (WindSpeed) มี Outlier สูงมากในบางวัน Median จึงสะท้อนค่า "ปกติ" ได้ดีกว่า

**โค้ด Python:**
```python
df[['SunshineDuration', 'WindSpeed', 'TotalPlayer', 'TimePerRound']].median()
```

### 📊 ผลลัพธ์จริง:
```
SunshineDuration    3.4        ← แสงแดดครึ่งหนึ่งของวันน้อยกว่า 3.4 ชั่วโมง
WindSpeed           1.9        ← ลมครึ่งหนึ่งของวันน้อยกว่า 1.9 m/s
TotalPlayer        61.0        ← วันทั่วไปมีผู้เล่น ~61 คน
TimePerRound      309.0        ← รอบหนึ่งใช้เวลา ~309 นาที
```

### 🔍 การตีความ:
- **WindSpeed**: Mean = 2.81, Median = 1.9 → **Mean > Median** หมายความว่ามีวันที่ลมแรงมากดึง Mean ขึ้น (Right-Skewed)
- **TotalPlayer**: Median = 61 คน → วันทั่วไปมีผู้เล่นประมาณ 60 กว่าคน
- **TimePerRound**: Median = 309 นาที (~5 ชั่วโมง) → รอบกอล์ฟปกติใช้เวลาประมาณ 5 ชั่วโมง

---

### 📌 Mode (ฐานนิยม)

**Mode** คือค่าที่ปรากฏบ่อยที่สุดในข้อมูล

> 📌 **จากรูปภาพ Infographic**:  
> ✅ **เหมาะกับ**: ข้อมูล Categorical เช่น Outlook (สภาพอากาศ)

**โค้ด Python:**
```python
df[['Outlook', 'TotalPlayer', 'Temperature', 'TimePerRound']].mode()
```

### 📊 ผลลัพธ์จริง:
```
Outlook       sunny      ← วันแดดออกพบบ่อยที่สุด
TotalPlayer      60      ← จำนวนผู้เล่นที่พบบ่อยที่สุดคือ 60 คน
Temperature    25.4      ← อุณหภูมิที่พบบ่อยที่สุดคือ 25.4°C
TimePerRound    290      ← รอบที่ใช้เวลา 290 นาทีพบบ่อยที่สุด
```

### 🔍 การตีความ:
- **Outlook = sunny**: วันที่แดดออกพบมากที่สุดในปี → ชุดข้อมูลนี้ถ่ายทำจากพื้นที่ที่มีแสงแดดมาก
- **TotalPlayer = 60**: จำนวนผู้เล่นที่พบบ่อยที่สุดคือ 60 คน → สนามกอล์ฟมักรับผู้เล่นประมาณ 60 คนต่อวัน

---

### เปรียบเทียบ Mean vs Median vs Mode

| สถิติ | เหมาะกับ | ทน Outlier | ตัวอย่างในชุดข้อมูล Golf |
|-------|---------|:----------:|--------------------------|
| **Mean** | ข้อมูลสมมาตร ไม่มี Outlier | ❌ | Temperature |
| **Median** | ข้อมูลเบ้ มี Outlier | ✅ | Wind Speed, TotalPlayer |
| **Mode** | ข้อมูล Categorical | ✅ | Outlook (sunny/overcast/rainy) |

---

## ➋ การวัดการกระจายตัวและความถี่ (Measures of Dispersion & Frequency)

### 📌 Range (พิสัย)

> 📌 **จากรูปภาพ Infographic**:  
> ✅ **ข้อดี**: วิธีวัดการกระจายตัวที่ง่ายที่สุด  
> ❌ **ข้อเสีย**: ไม่คำนึงถึงข้อมูลกลาง → ไวต่อ Outlier มาก

```python
temperature_range = df['Temperature'].max() - df['Temperature'].min()
print(temperature_range)
```

### 📊 ผลลัพธ์จริง:
```
16.0
```

### 🔍 การตีความ:
- อุณหภูมิในชุดข้อมูลนี้ผันผวน **16°C** (ความต่างระหว่างวันที่ร้อนสุดและเย็นสุด)
- ค่านี้บอกแค่ว่าอุณหภูมิกว้างแค่ไหน แต่ไม่บอกว่ากระจายตัวอย่างไร

---

### 📌 Standard Deviation (ส่วนเบี่ยงเบนมาตรฐาน)

> 📌 **จากรูปภาพ Infographic**:  
> ✅ **ใช้เมื่อ**: ต้องการรู้ว่าตัวเลขผันผวนจากค่าเฉลี่ยมากแค่ไหน

```python
df[['Temperature', 'Humidity', 'WindDirection', 'WindSpeed']].std()
```

### 📊 ผลลัพธ์จริง:
```
Temperature        3.07        ← SD ต่ำ: อุณหภูมิค่อนข้างสม่ำเสมอ
Humidity           9.11        ← SD ปานกลาง: ความชื้นผันผวนพอสมควร
WindDirection    114.36        ← SD สูงมาก: ทิศทางลมผันผวนมาก
WindSpeed          2.96        ← SD สูง: ลมผันผวน (บางวันลมแรงมาก)
```

### 🔍 การตีความ:
| คอลัมน์ | SD | ความหมาย |
|---------|-----|---------|
| Temperature | 3.07°C | อุณหภูมิส่วนใหญ่อยู่ในช่วง 27.08 ± 3.07°C (24-30°C) |
| Humidity | 9.11% | ความชื้นผันผวน ±9% จากค่าเฉลี่ย |
| WindDirection | 114.36° | ลมพัดมาจากทิศต่างๆ ไม่คงที่ |
| WindSpeed | 2.96 m/s | ลมผันผวนมาก (บางวันเกือบนิ่ง บางวันแรงมาก) |

> 💡 **สังเกต**: WindSpeed: Mean = 2.81 และ SD = 2.96 เท่ากับว่า SD > Mean ซึ่งบ่งชี้ว่าข้อมูลมี Outlier (บางวันลมแรงผิดปกติ)

---

### 📌 Quartile (ควอร์ไทล์) และ IQR

> 📌 **จากรูปภาพ Infographic**:  
> ✅ **ใช้เมื่อ**: ต้องการระบุ Outlier และดูช่วงที่ข้อมูล 50% ตรงกลางอยู่

```python
Q1 = df[['Temperature', 'Humidity', 'WindSpeed']].quantile(0.25)
Q2 = df[['Temperature', 'Humidity', 'WindSpeed']].quantile(0.5)
Q3 = df[['Temperature', 'Humidity', 'WindSpeed']].quantile(0.75)
pd.DataFrame({'Q1': Q1, 'Q2': Q2, 'Q3': Q3}).T
```

### 📊 ผลลัพธ์จริง:
```
      Temperature  Humidity  WindSpeed
Q1         24.9      66.0        0.8
Q2         26.6      72.0        1.9
Q3         29.4      79.0        3.7
```

### 🔍 การตีความ:

**Temperature (อุณหภูมิ):**
- Q1 = 24.9°C: วัน 25% ที่เย็นที่สุด มีอุณหภูมิต่ำกว่า 24.9°C
- Q2 = 26.6°C: ครึ่งหนึ่งของปี อุณหภูมิต่ำกว่า 26.6°C
- Q3 = 29.4°C: วัน 75% มีอุณหภูมิต่ำกว่า 29.4°C
- **IQR = 29.4 - 24.9 = 4.5°C** → อุณหภูมิ 50% กลางกระจายในช่วง 4.5°C

**Humidity (ความชื้น):**
- Q1 = 66%, Q2 = 72%, Q3 = 79%
- **IQR = 79 - 66 = 13%** → ความชื้น 50% กลางกระจายในช่วง 13%

**WindSpeed (ความเร็วลม):**
- Q1 = 0.8 m/s, Q2 = 1.9 m/s, Q3 = 3.7 m/s
- **IQR = 3.7 - 0.8 = 2.9 m/s**
- Lower Bound = 0.8 - 1.5×2.9 = **-3.55** (ไม่มีลมติดลบ ดังนั้น Min = 0)
- Upper Bound = 3.7 + 1.5×2.9 = **8.05 m/s** → ลมที่เร็วกว่า 8.05 m/s ถือเป็น **Outlier!**

---

### 📦 Boxplot

> 📌 **จากรูปภาพ Infographic**:  
> ✅ **ใช้เมื่อ**: ต้องการเห็น Median, Quartile และ Outlier ในกราฟเดียว

```python
fig, axes = plt.subplots(1, 3, figsize=(10, 6))
for i, col in enumerate(['Temperature', 'Humidity', 'WindSpeed']):
    axes[i].boxplot(df[col])
    axes[i].set_title(col)
plt.show()
```

> จากกราฟที่ได้: WindSpeed มี Outlier หลายจุดด้านบน (วันที่ลมแรงผิดปกติ)

---

### 📋 Frequency Table (ตารางความถี่)

> 📌 **จากรูปภาพ Infographic**:  
> ✅ **ใช้เมื่อ**: ต้องการนับจำนวนครั้งที่ค่าต่างๆ ปรากฏ

```python
frequency_table = df['Outlook'].value_counts().reset_index()
frequency_table.columns = ['Grade', 'Frequency']
frequency_table
```

### 📊 ผลลัพธ์จริง:
```
      Grade  Frequency
0     sunny        147
1  overcast        129
2     rainy         89
```

### 🔍 การตีความ:
| สภาพอากาศ | จำนวนวัน | สัดส่วน |
|-----------|---------|--------|
| sunny | 147 วัน | 40.3% |
| overcast | 129 วัน | 35.3% |
| rainy | 89 วัน | 24.4% |

- วันที่แดดออก (sunny) มากที่สุด คิดเป็น 40% ของปี
- วันฝนตก (rainy) น้อยที่สุด คิดเป็นเพียง 24% ของปี
- ข้อมูลนี้ถ่ายทำจากพื้นที่เขตร้อน (สอดคล้องกับอุณหภูมิเฉลี่ย 27°C)

---

## 🔍 ตารางเปรียบเทียบทุกมาตรการ

| มาตรการ | วัดอะไร | ทน Outlier? | เหมาะกับ |
|---------|---------|:-----------:|---------|
| **Mean** | ค่ากลาง | ❌ | ข้อมูลสมมาตร ไม่มี Outlier |
| **Median** | ค่ากลาง | ✅ | ข้อมูลเบ้ มี Outlier |
| **Mode** | ค่ากลาง | ✅ | ข้อมูล Categorical |
| **Range** | การกระจาย | ❌ | ต้องการภาพกว้างหยาบๆ |
| **SD** | การกระจาย | ❌ | ต้องการความผันผวน |
| **IQR** | การกระจาย | ✅ | ระบุ Outlier |
| **Boxplot** | ภาพรวม | ✅ | เปรียบเทียบหลายกลุ่ม |
| **Frequency Table** | ความถี่ | ✅ | ข้อมูล Categorical |

---

## 📝 สรุปบทเรียน (พร้อมสรุปจำข้อมูลจริง)

1. **Mean** → Temperature = 27.08°C, Humidity = 73.23%, WindSpeed = 2.81 m/s
2. **Median** → WindSpeed = 1.9 (< Mean แสดงว่า Right-Skewed มี Outlier วันลมแรง)
3. **Mode** → วันที่พบบ่อยสุดคือ sunny (147 วัน จาก 365 วัน)
4. **Range** → Temperature = 16°C (ช่วงความต่างตลอดปี)
5. **SD** → Temperature = 3.07 (ต่ำ/สม่ำเสมอ), WindSpeed = 2.96 (สูง/ผันผวน)
6. **Quartile** → WindSpeed: Q1=0.8, Q2=1.9, Q3=3.7 → Outlier ถ้า > 8.05 m/s

---

*แปลและสรุปจาก Kaggle Notebook โดย samybaladram รวมเนื้อหาจาก Infographic และการตีความผลลัพธ์จริง*  
*ชุดข้อมูล: Golf Play Dataset (ข้อมูลรายวัน ปี 2021 สภาพแวดล้อมของสนามกอล์ฟ)*
