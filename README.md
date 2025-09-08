# Cezayir Orman Yangınları FWI Tahmini

Projede "**[Algerian Forest Fires Dataset](https://www.kaggle.com/datasets/nitinchoudhary012/algerian-forest-fires-dataset)**" veri setini kullanarak **Yangın Hava İndeksi (FWI - Fire Weather Index)** değeri tahmin edilmiştir. Proje kapsamında veri temizleme, ön işleme, keşifsel veri analizi (EDA) ve çeşitli regresyon modellerinin performans karşılaştırması yapılmıştır.

## Kullandığım Kütüphaneler
* **NumPy**
* **Pandas**
* **Matplotlib**
* **Seaborn**
* **Scikit-learn**

## Veri Seti Kolonları

  - **Temperature**: Sıcaklık (Celsius)
  - **RH**: Bağıl Nem (%)
  - **Ws**: Rüzgar Hızı (km/s)
  - **Rain**: Yağış Miktarı (mm)
  - **FFMC**: Fine Fuel Moisture Code (İnce Yakıt Nem Kodu)
  - **DMC**: Duff Moisture Code (Orta Yakıt Nem Kodu)
  - **DC**: Drought Code (Kuraklık Kodu)
  - **ISI**: Initial Spread Index (İlk Yayılım İndeksi)
  - **BUI**: Buildup Index (Bünye Nem İndeksi)
  - **FWI**: Fire Weather Index (Yangın Hava İndeksi) -> **Hedef Değişken**
  - **Classes**: Yangın var (1) veya yok (0) durumu
  - **Region**: Bölge (Bejaia: 0, Sidi-Bel Abbes: 1)

## Proje Adımları

Proje aşağıdaki adımlardan oluşmaktadır:

1.  **Veri Yükleme ve Keşif:** Veri seti 'read_csv()' ile yüklendi ve 'info()', 'describe()', 'isnull().sum()' gibi fonksiyonlarla veri seti incelendi.

2.  **Veri Temizleme ve Ön İşleme:**

      * Veri setinde bulunan iki bölge için de 'NaN' değerler içeren gereksiz satırlar temizlendi.
      * İki bölgeyi ayırt etmek için 'Region' adında yeni bir özellik (feature) oluşturuldu.
      * Sütun isimlerindeki gereksiz boşluklar kaldırıldı.
      * Tüm sütunların veri tipleri 'object' olduğu için 'astype()' kullanılarak uygun sayısal ('int', 'float' gibi) tiplere dönüştürüldü.
      * 'Classes' sütunundaki metin verileri ("not fire", "fire") sayısal (0, 1) değerlere dönüştürüldü.

3.  **Keşifsel Veri Analizi (EDA):**

      * Özellikler arasındaki korelasyonu incelemek için bir ısı haritası ('heatmap') oluşturuldu. Bazı özellikler arasında yüksek korelasyon olduğu gözlemlendi.

4.  **Özellik Seçimi ve Model Hazırlığı:**

      * 'day', 'month', 'year' sütunları, FWI tahmini için doğrudan anlamlı olmadıkları için modelden çıkarıldı.
      * Çoklu doğrusallığı (multicollinearity) önlemek amacıyla birbiriyle **0.85'ten daha yüksek korelasyona sahip** olan özelliklerden 'BUI' ve 'DC' sütunları çıkarıldı.
      * Veri seti hedef değişken ('FWI') ve bağımsız değişkenler ('X') olarak ayrıldı.
      * 'train_test_split' ile veri seti eğitim (%75) ve test (%25) olarak bölündü.
      * Model performansını artırmak ve regularizasyon tekniklerini daha etkili uygulayabilmek için veriler 'StandardScaler' ile ölçeklendirildi.

5.  **Modelleme ve Değerlendirme:**

      * FWI değerini tahmin etmek için aşağıdaki regresyon modelleri eğitildi ve test edildi:
          - Lineer Regresyon
          - Lasso Regresyon
          - Ridge Regresyon
          - ElasticNet Regresyon
      * Ayrıca, en iyi hiperparametreleri (alpha) bulmak için `LassoCV`, `RidgeCV` ve `ElasticNetCV` modelleri kullanıldı.
      * Modellerin performansı **Ortalama Mutlak Hata (MAE)**, **Ortalama Kare Hata (MSE)** ve **R² Skoru** metrikleri ile değerlendirildi.

## Sonuçlar

Eğitilen modellerin test verisi üzerindeki performans metrikleri aşağıdaki gibidir:

| Model | MAE | MSE | R² Skoru |
| :--- | :--- | :--- | :--- |
| Lineer Regresyon | 0.70 | 0.88 | 0.9838 |
| Lasso Regresyon | 1.22 | 2.60 | 0.9521 |
| Ridge Regresyon | 0.71 | 0.89 | 0.9836 |
| ElasticNet | 1.75 | 5.57 | 0.8975 |
| **LassoCV** | **0.72** | **0.90** | **0.9834** |
| **RidgeCV** | **0.71** | **0.89** | **0.9836** |
| **ElasticNetCV**| **0.74** | **0.92** | **0.9829** |

**Değerlendirme:**

  - Tüm modeller, özellikle Lineer Regresyon ve RidgeCV, **%98'in üzerinde R² skoru** ile oldukça yüksek bir başarı göstermiştir. Bu, FWI'nin veri setindeki diğer özelliklerle güçlü bir doğrusal ilişkiye sahip olduğunu göstermektedir.
  - Lasso Regresyonu ve ElasticNet'in basit versiyonları biraz daha yüksek hata oranları verse de, Çapraz Doğrulama (CV) ile optimize edilmiş versiyonları Lineer ve Ridge Regresyon'a yakın sonuçlar vermiştir.
