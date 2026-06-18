# jsoup Kütüphanesi Tasarım Kalitesi Analizi (v1.13.1 → v1.22.1)

> **Veri kaynağı:** 10 ardışık sürümden statik analizle çıkarılmış OO tasarım metrikleri.  
> **Yöntem:** QMOOD çerçevesi (Bansiya & Davis, 2002) ve metrik eğilim analizi.

---

## Ham Veri

| Sürüm  | DSC | NOH | ANA    | DAM    | DCC    | CAM    | MOA    | MFA    | NOP    | CIS    | NOM    |
|--------|-----|-----|--------|--------|--------|--------|--------|--------|--------|--------|--------|
| 1.13.1 | 242 | 2   | 2.012  | 0.752  | 4.818  | 0.853  | 0.905  | 0.240  | 2.983  | 3.360  | 6.091  |
| 1.14.1 | 246 | 2   | 2.000  | 0.744  | 4.976  | 0.852  | 0.919  | 0.239  | 3.073  | 3.467  | 6.280  |
| 1.15.1 | 250 | 2   | 2.012  | 0.750  | 5.012  | 0.842  | 0.976  | 0.241  | 3.080  | 3.468  | 6.348  |
| 1.16.1 | 253 | 2   | 2.016  | 0.741  | 5.051  | 0.835  | 0.992  | 0.241  | 3.198  | 3.581  | 6.522  |
| 1.17.1 | 261 | 2   | 2.023  | 0.698  | 5.046  | 0.806  | 1.077  | 0.243  | 3.395  | 3.728  | 6.793  |
| 1.18.1 | 265 | 2   | 1.989  | 0.712  | 5.155  | 0.806  | 1.113  | 0.236  | 3.438  | 3.815  | 6.891  |
| 1.19.1 | 270 | 3   | 1.981  | 0.669  | 5.289  | 0.799  | 1.144  | 0.235  | 3.448  | 3.848  | 6.985  |
| 1.20.1 | 275 | 3   | 1.978  | 0.623  | 5.436  | 0.795  | 1.091  | 0.238  | 3.476  | 3.942  | 7.178  |
| 1.21.1 | 285 | 3   | 2.032  | 0.645  | 5.533  | 0.792  | 1.095  | 0.250  | 3.460  | 3.919  | 7.186  |
| 1.22.1 | 290 | 4   | 2.017  | 0.654  | 5.524  | 0.788  | 1.103  | 0.248  | 3.448  | 3.928  | 7.238  |

---

## 1. Genel Kalite Değerlendirmesi

jsoup, incelenen 10 sürüm boyunca sürekli büyüyen (DSC: 242 → 290, **+19,8%**) ve olgunlaşan bir kütüphane görünümü çizer. Ancak büyüme, kalite metriklerinde çift yönlü bir etki bırakmıştır.

**Olumlu eğilimler:**

- **MOA** (0.905 → 1.103, +21,9%): Kompozisyon kullanımı belirgin biçimde artmış, kalıtım yerine "composition over inheritance" ilkesine yaklaşılmıştır.
- **NOP** (2.983 → 3.448, +15,6%) ve **CIS** (3.360 → 3.928, +16,9%): Polimorfizm ve arayüz zenginliği artarak genişletilebilirliğe katkı sağlamıştır.
- **NOH** (2 → 4): Hiyerarşi sayısı kontrollü artmıştır; kalıtım derinliği ise (ANA ≈ 2.0) tüm sürümlerde son derece kararlı kalmıştır.
- **MFA** (0.240 → 0.248): Hafif artışla kalıtılan kod payı korunmuştur.

**Olumsuz eğilimler:**

- **DAM** (0.752 → 0.654, **−13,1%**): Kapsülleme kalitesi tüm sürümlerde net biçimde gerilemiş, en düşük noktasına 1.20.1'de (0.623) ulaşmıştır.
- **DCC** (4.818 → 5.524, **+14,7%**): Her sürümde artan bağlaşım, modüller arası bağımlılıkların yönetilmesini giderek güçleştirmektedir.
- **CAM** (0.853 → 0.788, **−7,7%**): Metodlar arası kohezyon sürekli azalmış; sınıflar giderek daha az odaklı hale gelmiştir.
- **NOM** (6.091 → 7.238, **+18,8%**): Sınıf başına metot sayısı, kod tabanının büyümesinin ötesinde artmış; bu durum sınıfların sorumluluğunun genişlediğine işaret etmektedir.

**Özet tablo — başlangıç ve bitiş değerleri ile yön:**

| Metrik | 1.13.1 | 1.22.1 | Δ      | Yön  | Yorum            |
|--------|--------|--------|--------|------|-----------------|
| DSC    | 242    | 290    | +19,8% | →    | Kontrollü büyüme|
| DAM    | 0.752  | 0.654  | −13,1% | ↓↓   | Belirgin bozulma|
| DCC    | 4.818  | 5.524  | +14,7% | ↑↑   | Sürekli kötüleşme|
| CAM    | 0.853  | 0.788  | −7,7%  | ↓    | Kademeli bozulma|
| NOM    | 6.091  | 7.238  | +18,8% | ↑↑   | Karmaşıklık artışı|
| MOA    | 0.905  | 1.103  | +21,9% | ↑    | Olumlu gelişme  |
| NOP    | 2.983  | 3.448  | +15,6% | ↑    | Olumlu gelişme  |

Genel değerlendirme: kütüphane işlevsel olarak zenginleşmekte, ancak iç tasarım kalitesi (kapsülleme, kohezyon, bağlaşım) belirgin şekilde yıpranmaktadır. Büyüme yönetilmiş olmakla birlikte, eşlik eden refactoring yatırımı yetersiz kalmıştır.

---

## 2. Bakım Yapılabilirlik Analizi

Bakım yapılabilirliği doğrudan etkileyen dört metrik (DCC, CAM, NOM, DAM) tümü olumsuz yönde seyretmiştir.

### 2.1 Bağlaşım (DCC) — Kötüleşiyor

DCC, 1.13.1'deki 4.818 değerinden 1.22.1'de 5.524'e çıkmıştır. Artışın özellikle **1.18.1–1.20.1** döneminde hızlandığı görülmektedir:

- 1.17.1 → 1.18.1: +0.109
- 1.18.1 → 1.19.1: +0.134
- 1.19.1 → 1.20.1: +0.148 *(dönemin en yüksek tek-adım artışı)*

Bu ivmelenme, söz konusu dönemde yeni sınıfların mevcut yapıya güçlü bağlarla entegre edildiğini düşündürmektedir. Yüksek DCC, bir sınıfı değiştirmenin yan etki riskini artırır ve regresyon testlerinin kapsamını genişletir.

### 2.2 Kohezyon (CAM) — Sürekli Kötüleşiyor

CAM, 0.853'ten 0.788'e gerilemiştir (−7,7%). Azalma her sürümde düzenli devam etmiş, hiçbir sürümde toparlanma gözlemlenmemiştir. En sert düşüş **1.16.1 → 1.17.1** arasında yaşanmıştır (0.835 → 0.806, −0.029). Kohezyonun bu seyri, sınıfların tek bir sorumluluğa odaklanmak yerine farklı sorumlulukları aynı anda üstlendiğine işaret eder; bu durum doğrudan Single Responsibility Principle ihlaline karşılık gelir.

### 2.3 Karmaşıklık (NOM) — Sürekli Kötüleşiyor

Sınıf başına ortalama metot sayısı 6.091'den 7.238'e yükselmiştir (+18,8%). Kütüphanenin toplam sınıf sayısı %19,8 artarken NOM'un da benzer oranda artması, yeni sınıfların eskilerden daha büyük sorumluluk bloğuyla tanımlandığını göstermektedir. Bu durum birim test yazmayı, kodu okumayı ve hata ayıklamayı zorlaştırmaktadır.

### 2.4 Kapsülleme (DAM) — En Ciddi Gerileme

DAM, incelenen metrikler arasında en yüksek bozulma oranını sergileyen metriktir. 0.752'den 0.654'e düşmüş (−13,1%), en düşük değerine ise 1.20.1 sürümünde (0.623) ulaşmıştır. 1.21.1 ve 1.22.1'deki hafif toparlanma (0.645, 0.654) yönelimi tersine çevirmek için yeterli değildir. Azalan DAM, yeni eklenen alanların daha büyük bir bölümünün `public` ya da `package-private` olarak tanımlandığına işaret eder; bu, dışarıdan müdahaleye açık iç duruma yol açar ve kapsülleme prensibini aşındırır.

**Birleşik Bakım Riski:** DCC'nin artması ve CAM'ın azalması birlikte değerlendirildiğinde, sınıflar arası bağımlılıklar artarken sınıf içi tutarlılık azalmaktadır. Bu kombinasyon, "değişim etkisinin yayılması" riskini ikiye katlamaktadır.

---

## 3. Teknik Borç Tahmini

### 3.1 Birincil Borç Birikimi: 1.17.1 – 1.20.1 Dönemi

Bu dört sürümlük pencere, tüm olumsuz metriklerin eş zamanlı hızlandığı kritik dönemdir:

| Metrik | 1.16.1 | 1.20.1 | Değişim |
|--------|--------|--------|---------|
| DAM    | 0.741  | 0.623  | **−15,9%** |
| DCC    | 5.051  | 5.436  | +7,6%   |
| CAM    | 0.835  | 0.795  | −4,7%   |
| NOM    | 6.522  | 7.178  | +10,1%  |

DAM'ın bu dönemde neredeyse 0.12 birim düşmesi, sadece teknik borç değil, gelecekteki değişikliklerin maliyetini de artıran yapısal bir kırılmaya işaret etmektedir. **1.19.1 → 1.20.1 geçişi**, tek bir sürüm adımında en yüksek DCC artışını (+0.148) ve en düşük DAM değerini (0.623) üst üste getirdiği için bu dönemin en riskli noktasıdır.

MOA'nın 1.19.1'de zirveye ulaşması (1.144) ve ardından düşmesi (1.20.1: 1.091) de dikkat çekicidir: Bu, söz konusu dönemde hızla eklenen kompozisyon bağımlılıklarının bir kısmının sonraki sürümde geri alındığına işaret edebilir; ancak bu çıkarım kesinlik taşımamaktadır.

### 3.2 İkincil Borç Sinyalleri: 1.14.1

1.13.1 → 1.14.1 geçişinde DCC'nin tek adımda +0.158 artması (dönemin en yüksek tek adım artışı) ve DAM'ın 0.752'den 0.744'e gerilemesi, ilk büyük özellik dalgasının bir miktar hızlı ve borçlanarak gerçekleştiğine işaret etmektedir.

### 3.3 Kısmi İyileşme: 1.21.1 – 1.22.1

DAM 1.20.1 dibinden 1.22.1'e 0.031 toparlamış (0.623 → 0.654), DCC artışı duraksama göstermiştir (1.21.1 → 1.22.1: −0.009). Bu olumlu bir sinyaldir; ancak değerler hâlâ 1.13.1 başlangıç seviyelerinin gerisinde kalmaktadır.

---

## 4. Refactoring Önerileri

Aşağıdaki öneriler, tespit edilen ölçümsel sorunlara birebir karşılık gelmektedir.

### 4.1 DAM İçin: Alan Görünürlüklerini Kısıtla

Yeni eklenen `public` ve `package-private` alanları `private` olarak kısıtlayın; erişim gerekiyorsa getter/setter ya da immutable value object kullanın. Özellikle 1.17.1–1.20.1 arasında eklenen sınıflardaki alan görünürlükleri gözden geçirilmelidir.

### 4.2 DCC İçin: Bağımlılık Enjeksiyonu ve Arayüz Soyutlaması

Doğrudan sınıf referansları yerine arayüz (interface) bağımlılıkları tanımlayın. Bağlaşım artışının ivmelendiği 1.18.1–1.20.1 döneminde eklenen sınıflar için özellikle Dependency Injection Container ya da Factory kalıpları değerlendirilebilir. Bu yaklaşım DCC'yi aynı zamanda test edilebilirliği de artırarak düşürür.

### 4.3 CAM İçin: Tek Sorumluluk İlkesini Uygula (SRP Refactoring)

CAM'ı düşük olan sınıfları (dönem içinde oluşturulanlar dahil) tespit etmek için statik analiz araçları (Checkstyle, PMD, SonarQube) kullanın. Birden fazla kavramsal sorumluluğu olan sınıfları alt sınıflara ya da yardımcı (helper) sınıflara bölün. jsoup bağlamında bu, özellikle ayrıştırma (parsing) ile DOM manipülasyonunu aynı anda yürüten sınıflara uygulanabilir.

### 4.4 NOM İçin: Metot Dışa Çıkarma (Extract Method / Extract Class)

Sınıf başına metot sayısı 10'u geçen sınıflar refactoring için öncelikli adaylardır. "Extract Method" ve ardından gerekirse "Extract Class" desenleriyle sınıfları parçalayın. Bu, NOM'u düşürürken CAM'ı da dolaylı olarak iyileştirir.

### 4.5 Mimari Düzey: Katmanlı Bağımlılık Kuralı

DCC'yi sistematik olarak düşürmenin en etkili yolu, kütüphanenin iç modüllerini (core, parser, select, safety gibi) net katmanlar halinde tanımlamak ve bu katmanlar arası bağımlılıkları tek yönlü kılmaktır. Bu, hem DCC'yi hem de gelecekteki büyüme sürtünmesini azaltır.

---

## 5. Mimari Kalite Yorumları

### 5.1 Kalıtım Hiyerarşisi — Kontrollü ve Sağlıklı

ANA, tüm sürümlerde 1.978 – 2.032 aralığında kalmıştır; bu, kalıtım derinliğinin "2 ata" düzeyinde tutulduğunu göstermektedir. Derin kalıtım piramitleri oluşmamış, Liskov İkame Prensibi'nin uygulanmasını zorlaştıracak aşırı uzun hiyerarşilerden kaçınılmıştır. NOH'un 2'den 4'e çıkması, yeni bağımsız hiyerarşilerin oluşturulduğunu gösterir; bu, büyüyen bir kütüphane için beklenen ve genel olarak olumlu bir işarettir.

### 5.2 Kompozisyon Kullanımı — Olumlu Trend

MOA'nın 0.905'ten 1.103'e yükselmesi, mimarların kalıtım yerine kompozisyonu tercih ettiğini ortaya koymaktadır. Bu eğilim nesne yönelimli tasarım prensipleriyle uyumludur ve genişletilebilirliği artırır. Bununla birlikte 1.19.1'deki zirve (1.144) ve ardından gelen hafif geri çekilme bazı tasarım kararlarının gözden geçirildiğine işaret edebilir.

### 5.3 Fonksiyonel Soyutlama — Yetersiz Düzeyde Durağan

MFA (%24–25 bandında sabit kalmıştır) oldukça düşük bir düzeyde seyretmektedir. Sınıfların yalnızca dörtte biri kalıtılan metotlara dayanmakta; bu, kodun büyük bölümünün türetilmiş sınıflar yerine doğrudan her sınıfta yeniden yazıldığına işaret eder. Modülerlik açısından bu durum, ortak davranışın üst sınıfta toplanmadığı anlamına gelir.

### 5.4 Arayüz Büyümesi — Dikkat Gerektiriyor

CIS'in 3.360'tan 3.928'e çıkması (+16,9%), sınıf arayüzlerinin genişlediğini göstermektedir. Arayüz Ayrımı Prensibi (Interface Segregation) açısından bu gelişim, değişime duyarlı istemci kodunu artırabilir. Büyük public arayüzler, kütüphaneyi kullanan geliştiriciler için gereksiz bağımlılıklar oluşturabilir. Bu metriğin NOP ile birlikte (+15,6%) yorumlanması, daha fazla metodun geçersiz kılınabilir (overridable) hale geldiğini ve bunun esnek bir polimorfik tasarımı desteklediğini göstermektedir; dolayısıyla CIS artışı tek başına değil, NOM ile birlikte değerlendirilmelidir.

### 5.5 Genel Mimari Değerlendirme

Kütüphanenin genişletilebilirlik potansiyeli (MOA, NOP artışları) olumlu seyretmektedir; ancak artan bağlaşım (DCC) ve azalan kapsülleme (DAM), eklenen genişletilebilirlik mekanizmalarının yeterince izole edilemediğini düşündürmektedir. Başka bir deyişle kütüphane "dışarıya açılmakta" ama aynı zamanda "içeriden sızdırmaktadır."

---

## 6. QMOOD Kalite Nitelikleri Eğilimi

*Hesaplamalar Bansiya & Davis (2002) QMOOD ağırlıklandırma yaklaşımına göre yapılmıştır. Normalizasyon için metrik başına gözlemlenen aralıklar kullanılmıştır.*

| Nitelik              | Eğilim    | Gerekçe |
|----------------------|-----------|---------|
| **Yeniden Kullanılabilirlik** | ↑ Artıyor | DSC ve CIS'in artışı, DCC ve CAM'daki kayıpları sayısal olarak aşmakta; kütüphane daha fazla sınıf ve arayüz sunarak potansiyel yeniden kullanım alanını genişletmektedir (ham skor: 0.664 → 0.738). |
| **Esneklik**         | ↗ Hafif Artıyor (dalgalı) | DAM gerileme ve DCC artışı olumsuz etkilerken, MOA ve NOP'taki yükseliş bu etkiyi kısmen telafi etmektedir; 1.18.1 zirvesinden (0.671) sonra düşüş yaşanmış, 1.22.1'de kısmi toparlanma görülmüştür (0.646). |
| **Anlaşılabilirlik** | ↓↓ Belirgin Azalıyor | DCC, NOM ve DSC'nin tümü artarken DAM ve CAM azalmaktadır; bu metriklerin tamamı anlaşılabilirliği olumsuz etkilemekte ve skor sürekli kötüleşmektedir (−0.515 → −0.714). |
| **İşlevsellik**      | ↑↑ Belirgin Artıyor | DSC, CIS, NOP ve NOH'un sürekli artışı işlevsellik skorunu güçlü biçimde yukarı taşımaktadır; 1.19.1'deki NOH sıçraması bu artışı hızlandırmıştır (0.595 → 0.765). |
| **Genişletilebilirlik** | → Durağan (hafif dalgalı) | NOP ve MFA artışları olumlu katkı sağlarken DCC artışı bu katkıyı nötrlemektedir; sonuç olarak skor dar bir bantta seyretmektedir (0.513 → 0.529). |
| **Etkinlik**         | → Durağan (hafif artış) | MOA, MFA ve NOP'taki artışlar DAM'ın gerilemesiyle dengelenmekte; 1.20.1'deki belirgin dip (0.552) kısmen telafi edilmiş olsa da net kazanım sınırlı kalmaktadır (0.542 → 0.563). |

---

## Genel Sonuç

jsoup, 1.13.1'den 1.22.1'e uzanan süreçte işlevsel açıdan zenginleşmiş, hiyerarşi sayısı ve kompozisyon kullanımı artmış, polimorfik tasarım güçlenmiştir. Ancak bu büyüme, kapsülleme (DAM: −13,1%), kohezyon (CAM: −7,7%) ve bağlaşım (DCC: +14,7%) boyutlarında ödenen ciddi bir tasarım kalitesi bedeliyle gerçekleşmiştir. Teknik borcun en yoğun biriktiği dönem, tüm olumsuz metriklerin eş zamanlı ivmelendiği **1.17.1–1.20.1** arasıdır; bu dönemde özellikle DAM %15,9 gerilemiştir. 1.21.1 ve 1.22.1'de gözlemlenen hafif toparlanma umut verici olmakla birlikte, başlangıç kalite seviyelerinin oldukça gerisinde kalmaktadır. Kütüphanenin sürdürülebilir büyümesi için DCC'yi düşürmeye yönelik arayüz soyutlamalarının ve DAM'ı iyileştirmeye yönelik alan görünürlük kısıtlamalarının bir sonraki büyük sürüm döngüsünde önceliklendirilmesi önerilmektedir.
