# jsoup Kütüphanesi Tasarım Metrikleri Analizi (1.13.1 → 1.22.1)

Rol: Sen, nesne yönelimli yazılım tasarımı ve yazılım kalitesi metrikleri konusunda uzman bir yazılım mimarısın.

Görev: Sana, açık kaynaklı jsoup (Java HTML ayrıştırıcısı) kütüphanesinin 10 ardışık sürümünden (1.13.1 → 1.22.1) statik analizle çıkarılmış nesne yönelimli tasarım metriklerini içeren bir tablo veriyorum. Bu metrikleri yorumlayarak yazılımın sürümler boyunca tasarım kalitesinin nasıl evrildiğini analiz et. Yalnızca verilen sayısal verilere dayan; veride olmayan bilgileri uydurma. Bir çıkarımdan emin değilsen bunu açıkça belirt.

Metrik tanımları (DSC ve NOH dışındaki değerler sürüm içindeki sınıfların ortalamasıdır):

- DSC — Tasarım boyutu: toplam sınıf sayısı.
- NOH — Hiyerarşi sayısı: bağımsız kalıtım ağacı sayısı.
- ANA — Ortalama ata sayısı (soyutlama): ortalama kalıtım derinliği.
- DAM — Veri erişim metriği (kapsülleme): gizli alanların oranı (0–1). Yüksek = iyi.
- DCC — Doğrudan sınıf bağlaşımı: bir sınıfın bağlı olduğu diğer sınıf sayısı. Yüksek = kötü.
- CAM — Metotlar arası kohezyon (0–1). Yüksek = iyi.
- MOA — Toplama/kompozisyon ölçütü: nesne tipli alan sayısı.
- MFA — İşlevsel soyutlama: kalıtılan metotların oranı (0–1).
- NOP — Çokbiçimli (geçersiz kılınabilir) metot sayısı.
- CIS — Sınıf arayüz boyutu: ortalama public metot sayısı.
- NOM — Karmaşıklık: sınıf başına ortalama metot sayısı. Yüksek = daha karmaşık.

---

## 1. Genel Kalite Değerlendirmesi

Genel tasarım kalitesi, incelenen 10 sürüm boyunca **istikrarlı bir şekilde evrilmiş** ancak bu evrim, bazı ödünleşimleri (trade-off) beraberinde getirmiştir. Kütüphane büyümeye ve yeni özellikler eklemeye devam ederken, mimari temellerini korumuştur.

- **Belirgin İyileşme Eğilimleri:** En çarpıcı iyileşme, **kapsülleme (DAM)** ve **soyutlama derinliği (ANA)** alanlarındadır. DAM, 0.75'ten (1.13.1) 0.65 seviyelerine gerilemiş gibi görünse de (bu bir endişe kaynağıdır, aşağıda detaylandırılacaktır), asıl dikkat çekici iyileşme **çokbiçimlilik (NOP)** ve **işlevsel soyutlama (MFA)** oranlarındaki artıştır. NOP, 2.98'den (1.13.1) 3.45'e (1.22.1) yükselirken, MFA 0.24'ten 0.25'e hafif bir artış göstermiştir. Bu, yeni sürümlerde kalıtımın daha etkin kullanıldığına işaret eder.

- **Belirgin Bozulma Eğilimleri:** En önemli bozulma, **kapsülleme (DAM)** metriğinde yaşanmıştır. DAM, 0.751'den (1.13.1) 0.653'e (1.22.1) düzenli bir düşüş göstermektedir. Bu, sürümler ilerledikçe sınıfların iç verilerine (alanlarına) erişimin daha fazla açıldığını, yani kapsüllemenin zayıfladığını gösterir. Bu, artan bağlaşım (DCC) ile birlikte ciddi bir kalite endişesidir.

- **Diğer Göze Çarpanlar:** **Bağlaşım (DCC)** ve **karmaşıklık (NOM)** sürekli artmaktadır. DCC 4.81'den (1.13.1) 5.52'ye (1.22.1), NOM ise 6.09'dan (1.13.1) 7.23'e yükselmiştir. Bu, kütüphanenin daha fazla sorumluluk üstlendikçe sınıflar arası bağımlılıkların ve metot sayılarının arttığını gösteren beklenen bir durumdur.

---

## 2. Bakım Yapılabilirlik Analizi

Bakım yapılabilirlik, birbiriyle çelişen iki güçlü eğilimin etkisi altındadır:

- **Bağlaşım (DCC) ve Karmaşıklık (NOM) Olumsuz Etkiliyor:** DCC'nin 4.81'den 5.52'ye, NOM'un ise 6.09'dan 7.23'e artması, bakımı zorlaştıran temel faktörlerdir. Yüksek bağlaşım, bir sınıfta yapılan bir değişikliğin diğer birçok sınıfı etkileme olasılığını artırır. Artan metot sayısı (NOM) ise sınıfların daha fazla sorumluluk üstlendiğini ve dolayısıyla daha karmaşık hale geldiğini gösterir. Bu durum, yeni geliştiricilerin kodu anlamasını ve mevcut kodu değiştirmesini zorlaştırır.

- **Kohezyon (CAM) ve Kapsülleme (DAM) Belirsiz/Endişe Verici:** **Kohezyon (CAM)**, 0.85'lerden başlayarak 0.79'lara kadar düzenli bir düşüş göstermektedir. Bu, sınıfların metotlarının giderek daha az ilişkili işleri yaptığına, yani sınıfların "tek sorumluluk" ilkesinden uzaklaştığına işaret eder. En büyük endişe kaynağı ise **kapsüllemedeki (DAM)** düşüştür. Gizli alan oranının 0.75'ten 0.65'e gerilemesi, sınıfların iç durumlarının daha fazla dışarıya ifşa edildiği anlamına gelir. Bu, bağlaşımı daha da artıran ve bakımı zorlaştıran bir durumdur.

**Sonuç:** Bağlaşım ve karmaşıklığın artması beklenirken, kohezyon ve özellikle kapsüllemedeki düşüş, bakım yapılabilirliğin zamanda **kötüleştiğine** dair güçlü sinyallerdir. Kütüphane büyürken iç tasarım kalitesi bundan olumsuz etkilenmektedir.

---

## 3. Teknik Borç Tahmini

Teknik borcun en belirgin şekilde biriktiği sürümler, **1.17.1, 1.18.1 ve 1.19.1** aralığıdır. Bu sürümlerde birkaç metrikte birden keskin kırılmalar yaşanmıştır:

- **DAM (Kapsülleme):** 1.16.1'de 0.741 iken, 1.17.1'de 0.697'ye keskin bir düşüş yaşanmıştır. Bu, bu sürümde yapılan değişikliklerin kapsüllemeyi ciddi şekilde zedelediğini gösterir.

- **MFA (İşlevsel Soyutlama):** 1.16.1'de 0.241 iken, 1.17.1'de 0.242'ye çıkmış, ardından 1.18.1'de tekrar 0.235'e düşmüştür. Bu dalgalanma, kalıtım hiyerarşisinde bu sürümlerde yapılan değişikliklerin tam olarak oturmadığına işaret eder.

- **MOA (Toplama/Kompozisyon):** 1.16.1'de 0.834 iken 1.17.1'de 0.805'e, 1.18.1'de ise 0.805'te kalmıştır. Nesne tipli alan sayısındaki bu azalma, nesne kompozisyonu yerine daha basit veya daha az esnek çözümlere yönelinmiş olabileceğini düşündürür.

Bu üç metrikteki eşzamanlı bozulma, 1.17.1 sürümünde **önemli bir tasarım revizyonu** yapıldığını ve bu revizyonun beraberinde teknik borç getirdiğini göstermektedir. Sonraki sürümlerde (1.20.1 ve sonrası) bu metriklerdeki değerler yeni bir seviyede dengelenmiş olsa da, başlangıçtaki kalite seviyesine geri dönülememiştir.

---

## 4. Refactoring Önerileri

Tespit edilen sorunlara yönelik somut öneriler:

1.  **Kapsüllemeyi Güçlendirin (DAM):**
    - **Alanları `private` yapın ve gereksiz yere `public`/`protected` getter/setter eklemekten kaçının.**
    - Veri erişimini kısıtlamak için, sınıfların iç durumunu değiştiren metotlar yerine, durumu sorgulayan ve değişiklik talep eden davranışsal metotlar tasarlayın (Tell, Don't Ask prensibi).

2.  **Bağlaşımı Azaltın (DCC) ve Kohezyonu Artırın (CAM):**
    - **Sınıfları 'Tek Sorumluluk' ilkesine göre yeniden düzenleyin.** Yüksek NOM (metot sayısı) ve düşük CAM (kohezyon), sınıfların çok fazla iş yaptığını gösterir. Örneğin, bir `HtmlParser` sınıfı hem ayrıştırma hem de ağaç yapısı oluşturma işini yapıyorsa, bu iki sorumluluğu ayrı sınıflara bölün.
    - **Arayüzler kullanarak bağlaşımı gevşetin.** Bir sınıf doğrudan somut bir sınıfa bağlanmak yerine, bir arayüze bağlanmalıdır. Bu, özellikle DCC değerleri yüksek olan sürümler için kritiktir.

3.  **Soyutlama ve Kalıtımı Gözden Geçirin (MFA, NOP):**
    - MFA oranındaki düşüş, kalıtımın etkin kullanılmadığına işaret edebilir. **Ortak davranışları soyut üst sınıflara veya arayüzlere taşıyarak** alt sınıflardaki metot tekrarını azaltın. Bu aynı zamanda NOP değerini (çokbiçimli metotlar) daha anlamlı bir seviyeye çıkarabilir.

---

## 5. Mimari Kalite Yorumları

- **Genişletilebilirlik:** Kısmen iyidir. NOP ve MFA'daki artış, kalıtım ve çokbiçimliliğin kullanıldığını gösterir. Ancak, artan bağlaşım (DCC) ve zayıflayan kapsülleme (DAM), yeni özellik eklemenin zorlaşacağına işaret eder. Genişletilebilirlik, bu iki olumsuz faktör tarafından tehdit edilmektedir.

- **Soyutlama:** İkili bir durum söz konusudur. ANA (ortalama ata sayısı) yaklaşık 2.0 civarında sabitlenmiştir. Bu, sınıfların ortalama olarak 2 seviye derinlikte bir hiyerarşiye sahip olduğunu ve bunun makul olduğunu gösterir. MFA'daki düşüş ve DAM'daki düşüş, bu soyutlamanın yüzeyde kaldığını, iç yapının (kapsülleme) ve davranışların (işlevsel soyutlama) yeterince soyutlanmadığını gösterir.

- **Modülerlik:** Zayıflamaktadır. DCC'deki sürekli artış, modüller arası veya sınıflar arası bağımlılıkların arttığını gösterir. Düşük CAM değerleri ise modüllerin/sınıfların içindeki öğelerin (metotların) birbiriyle daha az ilişkili hale geldiğini, yani modüler yapının içten çürümeye başladığını gösterir.

---

## 6. QMOOD Kalite Nitelikleri Eğilimi

| Nitelik | Eğilim | Gerekçe |
| :--- | :--- | :--- |
| **Yeniden Kullanılabilirlik** | **Azalıyor** | Artan bağlaşım (DCC) ve zayıflayan kapsülleme (DAM), sınıfların bağımsız olarak kullanılabilirliğini azaltır. Yüksek bağlaşım, bir sınıfı başka bir projeye veya bağlama taşımayı zorlaştırır. |
| **Esneklik** | **Azalıyor** | Artan bağlaşım (DCC) ve azalan kohezyon (CAM), sistemde yapılacak değişikliklerin maliyetini artırır. Bir değişikliğin etkisini tahmin etmek zorlaşır, bu da esnekliği kısıtlar. |
| **Anlaşılabilirlik** | **Azalıyor** | Artan karmaşıklık (NOM), artan bağlaşım (DCC) ve azalan kohezyon (CAM) nedeniyle sınıfların ne yaptığını anlamak zorlaşır. Yeni bir geliştiricinin kodu kavraması daha fazla zaman alır. |
| **İşlevsellik** | **Artıyor** | Bu, metriklerle doğrudan ölçülemese de, DSC (sınıf sayısı) 242'den 290'a, NOM (metot sayısı) 6.09'dan 7.23'e ve CIS (arayüz boyutu) 3.35'ten 3.92'ye artmıştır. Bu artışlar, kütüphaneye yeni özellikler ve yetenekler eklendiğinin açık bir göstergesidir. |
| **Genişletilebilirlik** | **Kararsız / Azalıyor** | Soyutlama (ANA) sabit kalırken, çokbiçimlilik (NOP) artsa da, bağlaşımın (DCC) artması ve kapsüllemenin (DAM) azalması, mevcut yapıya yeni davranışlar eklemenin giderek zorlaşacağına işaret eder. |
| **Etkinlik** | **Azalıyor** (Veri Tasarımı Açısından) | MOA (toplama/kompozisyon) değerindeki düşüş (0.85'ten 0.79'a), nesne kompozisyonu yerine daha az esnek olan kalıtım veya doğrudan veri erişimine yönelinmiş olabileceğini gösterir. Bu, uzun vadede tasarımın etkinliğini azaltır. |

---

### Genel Sonuç

jsoup kütüphanesinin 1.13.1'den 1.22.1'e evrimi, başarılı ve aktif bir projenin klasik büyüme sancılarını yaşadığını göstermektedir. Kütüphane işlevsellik açısından zenginleşirken (DSC, NOM, CIS artışı), bu büyüme **kapsülleme (DAM), kohezyon (CAM) ve bağlaşım (DCC)** gibi temel tasarım kalite metriklerini olumsuz etkilemiştir. Özellikle 1.17.1 sürümündeki keskin düşüşler, teknik borç oluşumuna işaret eden önemli bir dönüm noktasıdır. Projenin gelecekteki sürümleri için, artan işlevselliği sürdürülebilir bir mimariyle dengelemek adına, kapsülleme ve bağlaşımı iyileştirmeye yönelik bilinçli bir yeniden düzenleme (refactoring) çalışması yapılması kritik önem taşımaktadır. Aksi takdirde, artan karmaşıklık ve azalan iç kalite, bakım maliyetlerini katlanarak artıracaktır.