# 1. Genel Kalite Değerlendirmesi

Veriler, jsoup'un 1.13.1 → 1.22.1 sürümleri arasında **işlevsel olarak büyüdüğünü**, ancak bazı tasarım kalite göstergelerinde kademeli bir bozulma yaşadığını göstermektedir.

Öne çıkan eğilimler:

- **Tasarım boyutu (DSC)** 242’den 290’a yükselmiştir (+%19,8). Bu, sistemin sürekli genişlediğini göstermektedir.
- **Hiyerarşi sayısı (NOH)** 2’den 4’e çıkmıştır. Yeni bağımsız kalıtım yapılarının eklendiği anlaşılmaktadır.
- **Bağlaşım (DCC)** 4.82’den 5.52’ye yükselmiştir (+%14,7). Bu artış modüller arası bağımlılıkların arttığını göstermektedir.
- **Kohezyon (CAM)** 0.853’ten 0.788’e düşmüştür. Sınıfların iç bütünlüğü zamanla zayıflamıştır.
- **Kapsülleme (DAM)** 0.752’den 0.654’e gerilemiştir. Özellikle 1.17.1 sonrasında belirgin bir düşüş görülmektedir.
- **Karmaşıklık (NOM)** 6.09’dan 7.24’e yükselmiştir (+%18,8).

Buna karşılık bazı olumlu göstergeler de vardır:

- **MFA** yaklaşık 0.24 seviyesinde kalmış ve son sürümlerde hafif yükselmiştir.
- **ANA** tüm sürümlerde yaklaşık 2 seviyesinde seyretmiştir; kalıtım derinliği kontrol altında tutulmuştur.
- **MOA** 0.90’dan 1.10 seviyesine çıkmıştır; nesne bileşimi kullanımında artış vardır.

Genel olarak sistem büyürken mimari yapının tamamen kontrolden çıkmadığı görülmektedir; ancak bakım maliyetini artırabilecek bağlaşım, karmaşıklık ve kapsülleme göstergelerinde olumsuz bir eğilim mevcuttur.

---

# 2. Bakım Yapılabilirlik Analizi

## Bağlaşım (DCC)

DCC değeri:

- 1.13.1: **4.82**
- 1.22.1: **5.52**

şeklinde sürekli artmıştır.

Bu durum sınıfların daha fazla sayıda diğer sınıfa bağımlı hale geldiğini göstermektedir. Yüksek bağlaşım değişikliklerin sisteme yayılma riskini artırır ve bakım yapılabilirliği olumsuz etkiler.

---

## Kohezyon (CAM)

CAM değeri:

- 1.13.1: **0.853**
- 1.22.1: **0.788**

olarak düşmüştür.

Özellikle:

- 1.16.1 → 1.17.1 arasında 0.835 → 0.806
- 1.18.1 → 1.22.1 arasında 0.806 → 0.788

şeklinde sürekli gerileme vardır.

Bu eğilim sınıfların tek bir sorumluluğa odaklanma derecesinin azaldığını düşündürmektedir.

---

## Karmaşıklık (NOM)

NOM:

- 6.09 → 7.24

seviyesine yükselmiştir.

Artış yaklaşık %19’dur.

Bu durum sınıfların daha fazla davranış içermeye başladığını ve bakım, test ve hata ayıklama maliyetlerinin yükseldiğini göstermektedir.

---

## Kapsülleme (DAM)

DAM:

- 0.752 → 0.654

olarak gerilemiştir.

En düşük değer:

- 1.20.1: **0.623**

olarak görülmektedir.

DAM’daki düşüş, özel (private) alan oranının azaldığını ve veri gizliliğinin zayıfladığını göstermektedir. Bu da sınıflar arası bağımlılıkları artırabilecek bir durumdur.

---

## Genel Bakım Yapılabilirlik Sonucu

Bakım yapılabilirlik açısından metriklerin çoğu olumsuz yönde hareket etmiştir:

| Metrik | Eğilim | Etki |
|----------|----------|----------|
| DCC | Artıyor | Kötü |
| CAM | Azalıyor | Kötü |
| NOM | Artıyor | Kötü |
| DAM | Azalıyor | Kötü |

Dolayısıyla bakım yapılabilirliğin sürümler boyunca kademeli olarak zorlaştığı söylenebilir.

---

# 3. Teknik Borç Tahmini

Verilere göre teknik borcun birikmeye başladığı dönem özellikle **1.17.1 sonrası** görünmektedir.

## 1.17.1

Bu sürümde:

- DAM: 0.741 → **0.698**
- CAM: 0.835 → **0.806**

şeklinde belirgin düşüşler vardır.

Bu ani değişim tasarım kalitesinde kırılma noktası olarak değerlendirilebilir.

---

## 1.19.1 – 1.20.1

Teknik borç göstergelerinin en yoğun olduğu dönemdir.

### 1.20.1'de:

- DCC = **5.44**
- DAM = **0.623** (en kötü değer)
- NOM = **7.18**
- CAM = **0.795**

Bu kombinasyon:

- artan bağımlılık,
- azalan kapsülleme,
- düşen kohezyon,
- yükselen karmaşıklık

şeklindeki klasik teknik borç belirtilerini göstermektedir.

---

## 1.21.1 ve 1.22.1

Bazı göstergelerde hafif toparlanma vardır:

- DAM: 0.623 → 0.645 → 0.654
- MFA: 0.238 → 0.250 → 0.248

Ancak:

- DCC yüksek kalmıştır (≈5.53)
- CAM düşmeye devam etmiştir (0.792 → 0.788)

Bu nedenle teknik borcun tamamen temizlendiğini söylemek mümkün değildir.

---

# 4. Refactoring Önerileri

## 1. Bağlaşımı Azaltma

DCC'nin 4.82’den 5.52’ye çıkması nedeniyle:

- Dependency Inversion Principle uygulanmalı,
- Arayüz tabanlı bağımlılıklar artırılmalı,
- Doğrudan sınıf referansları azaltılmalıdır.

---

## 2. Büyük Sınıfları Bölme

CAM düşerken NOM yükselmektedir.

Bu durum bazı sınıfların birden fazla sorumluluk üstlenmeye başlamış olabileceğini düşündürmektedir.

Öneriler:

- Extract Class
- Extract Method
- Single Responsibility Principle denetimi

---

## 3. Kapsüllemeyi Güçlendirme

DAM’daki düşüş nedeniyle:

- Public/protected alanlar gözden geçirilmeli,
- Gereksiz görünür üyeler private yapılmalı,
- Veri erişimi erişimci metotlar üzerinden sağlanmalıdır.

---

## 4. Kalıtım ve Kompozisyon Dengesi

MOA'nın artması olumlu bir işarettir.

Bu eğilim sürdürülerek:

- inheritance yerine composition tercih edilmeli,
- davranış paylaşımı delegasyon yoluyla yapılmalıdır.

---

## 5. Modül Sınırlarını Yeniden Değerlendirme

DCC artarken NOH da yükselmiştir.

Bu nedenle:

- paket bağımlılık analizi,
- modül ayrıştırma,
- katmanlar arası bağımlılıkların sadeleştirilmesi

faydalı olabilir.

---

# 5. Mimari Kalite Yorumları

## Genişletilebilirlik

Kısmen olumlu görünmektedir.

Göstergeler:

- DSC: 242 → 290
- NOH: 2 → 4
- MFA: 0.240 → 0.248

Sistem yeni özellikler eklenerek büyüyebilmiştir. Ancak artan bağlaşım uzun vadede genişletilebilirliği sınırlandırabilir.

---

## Soyutlama

Soyutlama seviyesi büyük ölçüde durağandır.

- ANA yaklaşık 2 seviyesinde kalmıştır.
- MFA da 0.24–0.25 aralığında seyretmiştir.

Bu durum kalıtım yapısının kontrollü şekilde korunduğunu göstermektedir.

---

## Modülerlik

Modülerlik açısından karışık bir tablo vardır.

Olumlu:

- MOA artmıştır (0.905 → 1.103).

Olumsuz:

- DCC artmıştır (4.82 → 5.52).

Dolayısıyla bileşim kullanımı artsa da modüller arası bağımlılıkların da arttığı görülmektedir. Modülerlikte net bir iyileşme gözlenmemektedir.

---

# 6. QMOOD Kalite Nitelikleri Eğilimi

| Nitelik | Eğilim | Gerekçe |
|----------|----------|----------|
| Yeniden Kullanılabilirlik | Azalıyor | DCC'nin yükselmesi ve CAM'in düşmesi sınıfların bağımsız kullanılabilirliğini azaltmaktadır. |
| Esneklik | Hafif Azalıyor | MOA ve MFA olumlu olsa da artan bağlaşım esnekliği sınırlandırmaktadır. |
| Anlaşılabilirlik | Azalıyor | NOM artarken CAM ve DAM düşmektedir; sınıfları anlamak giderek zorlaşmaktadır. |
| İşlevsellik | Artıyor | DSC, CIS ve NOP değerlerindeki artış sistemin daha fazla davranış ve yetenek sunduğunu göstermektedir. |
| Genişletilebilirlik | Hafif Artıyor | NOH ve MFA artışı yeni davranışların eklenebilmesini desteklemektedir. |
| Etkinlik | Durağan | Veriler performans ölçümü içermemektedir; yalnızca MOA ve DCC değişimleri etkinlikte net bir yön göstermemektedir. |

---

## Genel Sonuç

jsoup'un incelenen sürümleri boyunca sistem düzenli biçimde büyümüş ve işlevsel kapasitesi artmıştır. Bununla birlikte bağlaşımın yükselmesi, kohezyonun düşmesi, kapsüllemenin zayıflaması ve sınıf karmaşıklığının artması bakım yapılabilirlik açısından olumsuz sinyaller vermektedir. Özellikle 1.17.1 sonrasında teknik borç birikimine işaret eden metrik değişimleri görülmektedir. Buna rağmen soyutlama yapısının büyük ölçüde korunmuş olması ve kompozisyon kullanımındaki artış, mimarinin tamamen bozulmadığını ve kontrollü refactoring çalışmalarıyla kalite eğiliminin yeniden iyileştirilebileceğini göstermektedir.