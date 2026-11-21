# PreTest: ฝึกทำ Machine Learning แบบครบวงจร

ชุดฝึกก่อนสอบ แต่ละ notebook ทำครบทุกขั้น ตั้งแต่โหลดข้อมูล → ทำความสะอาด → EDA → เตรียมข้อมูล → เทรนโมเดล → วัดผล → สรุป
ท้าย section ของทุกโมเดลมีกล่อง **การวิเคราะห์ผล** อธิบายว่าตัวเลขแต่ละตัวหมายถึงอะไร

| ไฟล์ | ประเภท | Dataset | โมเดล | หัวข้อเด่น |
|---|---|---|---|---|
| [01_Classification_RainTomorrow_End-to-End](01_Classification_RainTomorrow_End-to-End.ipynb) | Binary Classification | Rain in Australia (142k แถว) | Logistic Regression, Decision Tree, Random Forest | ข้อมูลไม่สมดุล, Confusion Matrix, ROC-AUC, Threshold, GridSearchCV |
| [02_Regression_Insurance_End-to-End](02_Regression_Insurance_End-to-End.ipynb) | Regression | Medical Insurance (1.3k แถว) | Linear Regression, Decision Tree, Random Forest | MAE/RMSE/R², log transform, Residual plots, Feature Engineering |
| [03_Multiclass_Classification_Penguins](03_Multiclass_Classification_Penguins.ipynb) | Multiclass Classification | Palmer Penguins (342 แถว) | KNN, SVM, Gradient Boosting | Pipeline + ColumnTransformer, RandomizedSearchCV, Macro vs Weighted F1, Validation/Learning curve |
| [04_Clustering_Mall_Customers](04_Clustering_Mall_Customers.ipynb) | Unsupervised (Clustering) | Mall Customers (200 แถว) | K-Means, Hierarchical, DBSCAN (+ PCA) | Elbow, Silhouette, Davies-Bouldin, Dendrogram, Persona |
| [05_ML_Cheat_Sheet](05_ML_Cheat_Sheet.ipynb) | สรุปสำหรับข้อสอบ | ตัวอย่างใน sklearn | ทุกประเภท | Template โค้ด, ตารางเลือก Metric/โมเดล, ข้อผิดพลาดที่พบบ่อย |
| [Pandas_Cleaning_Lesson](Pandas_Cleaning_Lesson.ipynb) | Data Cleaning | — | — | Regex และการทำความสะอาดข้อมูลด้วย Pandas |

**ลำดับที่แนะนำ:** 01 → 02 → 03 → 04 แล้วใช้ 05 เป็นเอกสารประกอบตอนสอบ

**ไฟล์ `_LiveCode` (สำหรับเขียนโค้ดตามในคลาส):** 01–04 มีอีกชุดอยู่ในโฟลเดอร์ [LiveCode/](LiveCode/) ซึ่งมีคำอธิบายครบเหมือนเดิม แต่ช่องโค้ดทุกช่องเว้นว่างไว้ให้เขียนตาม ถ้าติดตรงไหนให้เปิดไฟล์ต้นฉบับ (ไม่มี `_LiveCode`) ดูเฉลย

**การเปิดบน Google Colab:** ถ้าไม่มีโฟลเดอร์ `Data/` notebook จะดาวน์โหลดข้อมูลจาก Kaggle ให้อัตโนมัติด้วย `kagglehub`

## ที่มา (ดัดแปลงจาก Kaggle)
- 01: [Logistic Regression Classifier Tutorial](https://www.kaggle.com/code/prashant111/logistic-regression-classifier-tutorial) — Prashant Banerjee
- 02: [Linear Regression Tutorial](https://www.kaggle.com/code/sudhirnl7/linear-regression-tutorial) — sudhirnl7
- 03: [penguin dataset : The new Iris](https://www.kaggle.com/code/parulpandey/penguin-dataset-the-new-iris) — Parul Pandey
- 04: [Customer Segmentation (K-Means) | Analysis](https://www.kaggle.com/code/kushal1996/customer-segmentation-k-means-analysis) — Kushal
