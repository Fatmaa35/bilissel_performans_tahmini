🧠 Bilişsel Performans Skoru Tahmini

Bu depo, yaşam tarzı, uyku düzeni, stres metrikleri ve biyometrik verileri kullanarak bireylerin **Bilişsel Performans Skoru**'nu tahmin etmek için geliştirilmiş uçtan uca bir makine öğrenmesi boru hattını (pipeline) içermektedir.

 🚀 Proje Özeti
Model, ağaç tabanlı algoritmaların (LightGBM ve CatBoost) zayıf ve güçlü yanlarını dengeleyen, aykırı değerlere dirençli (robust) bir **Huber Stacking** mimarisi üzerine kurulmuştur. Genelleme yeteneğini artırmak için **5-Seed Averaging** ve **Yeo-Johnson** hedef değişken dönüşümü kullanılmıştır. Yüksek boyutlu verilerdeki karmaşık ilişkiler, Unsupervised Learning (Gözetimsiz Öğrenme) teknikleriyle haritalandırılmıştır.

🏗️ Model Mimarisi ve Pipeline Adımları

1. **Hedef Varyans Stabilizasyonu (Target Transformation):**
   - Ağaç tabanlı modellerin çarpıklığa (skewness) hassasiyetini azaltmak için hedef değişkene `PowerTransformer (Yeo-Johnson)` uygulandı.
   
2. **Latent Space (Gizli Profil) Keşfi:**
   - Ham veriler üzerinden doğrusal olmayan ilişkileri yakalamak adına, bireyler **Gaussian Mixture Models (GMM) / K-Means** kullanılarak 5 farklı psikolojik arka tipe ayrıldı ve bu dağılımlar modele güçlü bir sezgi olarak eklendi.

3. **Özellik Mühendisliği (Feature Engineering):**
   - Klinik yaklaşımlardan ilham alınarak yüksek sinyal yoğunluğuna sahip yeni özellikler türetildi:
     - `tukenmislik_indeksi`: (Stres Skoru × Günlük Çalışma Saati) / (Derin Uyku Yüzdesi + 1)
     - `uyku_verimliligi`: (Derin Uyku Yüzdesi + REM Yüzdesi) / (Uykuya Dalma Süresi + 1)
     - `otonom_stres_yuku`: Stres Skoru × Vücut Kitle İndeksi

4. **Cerrahi Özellik Budaması (XAI ile Pruning):**
   - LightGBM kullanılarak her bir özelliğin *Information Gain* (Bilgi Kazancı) hesaplandı. Modelin karar mekanizmasına katkısı olmayan ve ölü yük oluşturan alt %20'lik özellik dilimi veri setinden çıkarıldı.

5. **Robust Stacking & 5-Seed Averaging:**
   - **Level-0 Modelleri:** LightGBM (Huber objective) ve CatBoost (Huber loss).
   - Başlangıç rastgeleliğini sönümlemek için her model 5 farklı rastgele tohum (seed) ile 5-Fold CV kullanılarak eğitildi.
   - **Meta-Model (Level-1):** OOF (Out-of-Fold) tahminleri birleştirilirken, standart Ridge cezası yerine uç hatalara dirençli **Huber Regressor** kullanıldı.

 🛠️ Kullanılan Teknolojiler
- **Diller:** Python
- **Kütüphaneler:** Scikit-Learn, LightGBM, CatBoost, Pandas, NumPy, Matplotlib, Seaborn

 📂 Dosya Yapısı
- `bilissel-performans_tahmini.ipynb`: Veri ön işleme, özellik mühendisliği, model eğitimi ve tahminleme aşamalarını barındıran ana çalışma dosyası.
- `submission.csv`: Kaggle liderlik tablosu için üretilen nihai tahmin çıktısı (0-10 aralığına np.clip ile sabitlenmiş).

 ⚙️ Nasıl Çalıştırılır?
1. İlgili kütüphanelerin sisteminizde veya sanal ortamınızda yüklü olduğundan emin olun:
