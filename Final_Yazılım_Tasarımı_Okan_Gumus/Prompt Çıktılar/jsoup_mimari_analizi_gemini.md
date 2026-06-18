Bir yazılım mimarı olarak, ilettiğin statik analiz metriklerini inceledim. Sisteme yüklenen dosyanın bana ulaşan kesitinde yalnızca **1.13.1 ile 1.16.1** arasındaki sürümlerin verileri tam olarak okunabilmektedir. Çıkarımlarımı, veride olmayan bilgileri uydurmama kuralına sadık kalarak, elimdeki bu somut ardışık verilerin sayısal eğilimleri üzerinden gerçekleştiriyorum.

İşte jsoup kütüphanesinin tasarım metriklerine dayalı nesne yönelimli mimari analizi:

## Genel Kalite Değerlendirmesi
Sürümler boyunca kütüphanenin büyümesine paralel olarak yapısal bir "şişme" gözlemlenmektedir. Tasarım boyutu (DSC) 242'den 253'e çıkarken, karmaşıklık (NOM) 6.09'dan 6.52'ye yükselmiştir. Bu büyüme ne yazık ki sağlıklı bir modülerlikten ziyade, kalite metriklerinde hafif bir bozulma eğilimi ile gelmektedir. Sınıflar arası bağlaşımın (DCC) 4.81'den 5.05'e çıkması ve kohezyonun (CAM) 0.85'ten 0.83'e düşmesi, yeni özelliklerin tasarımı yavaş yavaş daha "monolitik" ve iç içe geçmiş bir yapıya doğru ittiğini göstermektedir. 

## Bakım Yapılabilirlik Analizi
Bakım yapılabilirlik, incelenen sürümler boyunca giderek zorlaşan bir tablo çizmektedir:
* **Bağlaşım (DCC):** Sürekli bir artışla 4.81'den 5.05'e ulaşmıştır. Bir sınıfta yapılacak değişikliğin, sistemin diğer parçalarını bozma (ripple effect) riski artmaktadır.
* **Kohezyon (CAM):** 0.853'ten 0.834'e gerilemiştir. Bu durum, sınıfların "Tek Sorumluluk Prensibi"nden (SRP) uzaklaştığını ve birden fazla, birbiriyle alakasız işlevi üstlenmeye başladığını kanıtlar.
* **Karmaşıklık (NOM):** Sınıf başına düşen ortalama metot sayısı 6.09'dan 6.52'ye çıkmıştır. Daha fazla metot, kodun okunabilirliğini ve test edilebilirliğini doğrudan zorlaştırır.
* **Kapsülleme (DAM):** 0.751'den 0.741'e hafif bir düşüş yaşamıştır. Gizli veri oranının azalması, sınıfların iç durumlarını (state) dışarıya daha fazla sızdırdığı anlamına gelir.

## Teknik Borç Tahmini
Teknik borcun özellikle **1.15.1 ve 1.16.1** sürümlerinde ivmelenerek biriktiğine dair net belirtiler bulunmaktadır. Sınıf arayüz boyutunun (CIS) 3.35'ten 3.58'e ve karmaşıklığın (NOM) 6.52'ye ulaşması, yeni gereksinimlerin zarif mimari soyutlamalar yerine mevcut sınıflara "metot yığılarak" çözüldüğüne işaret eder. Kohezyondaki (CAM) istikrarlı düşüş ve bağlaşımdaki (DCC) artış, bu "şişman sınıf" (fat class) kökenli teknik borcun en büyük kanıtıdır.

## Refactoring Önerileri
Verilerdeki bozulma eğilimini tersine çevirmek için şu yeniden düzenleme (refactoring) adımları atılmalıdır:
* **Sınıfları Bölme (Extract Class):** NOM ve CIS metriklerindeki artış durdurulmalıdır. Kohezyonu (CAM) artırmak için, çok fazla sorumluluk üstlenen büyük sınıflar tespit edilip daha küçük, tek odaklı alt sınıflara bölünmelidir.
* **Arayüz Üzerinden İletişim (Dependency Inversion):** Artan DCC (bağlaşım) oranını düşürmek için, sınıfların birbirine somut tipler üzerinden doğrudan bağlanması yerine, arayüzler (interfaces) aracılığıyla haberleşmesi sağlanmalıdır.
* **Kapsüllemeyi Sıkılaştırma:** Düşüşte olan DAM değerini toparlamak adına, public veya protected alanlar `private` yapılmalı; verilere erişim, davranışsal metotlar veya kontrollü `getter/setter` fonksiyonları arkasına gizlenmelidir.

## Mimari Kalite Yorumları
* **Soyutlama (Abstraction):** Ortalama ata sayısı (ANA - 2.01) ve işlevsel soyutlama (MFA - 0.24) metrikleri sürümler boyunca neredeyse tamamen sabit kalmıştır. Bu durum, jsoup'un çekirdek kalıtım ağacının ve temel mimari soyutlamasının oldukça olgun, oturmuş ve sağlam olduğunu kanıtlar.
* **Genişletilebilirlik:** Çokbiçimli metot sayısının (NOP) 2.98'den 3.19'a çıkması, sistemin hala "Açık/Kapalı Prensibi"ne (OCP) uygun bir şekilde, kalıtım ve çokbiçimlilik (polymorphism) yoluyla genişletilmeye elverişli tutulduğunu gösterir.
* **Modülerlik:** Çekirdek soyutlama sağlam olsa da, artan dışa bağımlılık (DCC) ve azalan iç bütünlük (CAM) nedeniyle modülerlik zayıflamakta, mimari sınırlar belirsizleşmektedir.

## QMOOD Kalite Nitelikleri Eğilimi

| Nitelik | Eğilim | Gerekçe |
| :--- | :--- | :--- |
| **Yeniden Kullanılabilirlik** | Azalıyor | Sınıflar arası bağlaşımın (DCC) artması ve kohezyonun (CAM) düşmesi, sınıfların sistemden bağımsız olarak çekilip başka projelerde kullanılmasını güçleştirir. |
| **Esneklik** | Durağan | Çokbiçimliliğin (NOP) artması esnekliği desteklerken, kapsüllemenin (DAM) zayıflaması bu durumu dengeleyerek genel esnekliği durağan kılar. |
| **Anlaşılabilirlik** | Azalıyor | Sınıf başına metot sayısının (NOM) ve dışa bağımlılıkların (DCC) sürekli artması, geliştiricilerin kodu okumasını ve zihinsel modelini kurmasını zorlaştırır. |
| **İşlevsellik** | Artıyor | Toplam sınıf (DSC), public metot (CIS) ve kompozisyon nesnesi (MOA) sayısındaki istikrarlı artış, kütüphaneye sürekli yeni yetenekler eklendiğini gösterir. |
| **Genişletilebilirlik** | Artıyor | Hiyerarşi (NOH) ve soyutlama (ANA) sabit kalırken çokbiçimli metotların (NOP) artması, tasarımın yeni davranışlar eklemeye açık olduğunu doğrular. |
| **Etkinlik** | Azalıyor | Odak noktasını kaybeden (düşen CAM) ve giderek karmaşıklaşan (artan NOM) sınıflar, nesne yönelimli tasarımın genel çalışma etkinliğini ve zarafetini zedeler. |

Genel olarak jsoup kütüphanenizin çekirdek mimarisi ve hiyerarşik yapısı güçlü bir iskelete sahiptir. Ancak sürümler ilerledikçe eklenen yeni özellikler, var olan sınıfların şişmesine ve birbirine daha sıkı bağlanmasına neden olarak bir teknik borç döngüsü başlatmıştır. Sürdürülebilir bir kalite standartı için, yeni gereksinimlerin mevcut sınıfları büyütmek yerine, sisteme iyi izole edilmiş yeni birimler olarak entegre edilmesine özen gösterilmelidir.