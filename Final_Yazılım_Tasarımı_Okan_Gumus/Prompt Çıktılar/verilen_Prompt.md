# Kullanılan İstem (Prompt)

Bu istem, LLM destekli kalite değerlendirmesinde **dört modele de (ChatGPT, Gemini,
DeepSeek, Claude Sonnet 4.6) birebir aynı** biçimde verilmiştir. İçeriği, teknik raporun
**VI.A (Deney Düzeneği ve Kullanılan İstem)** bölümünde de belgelenmiştir.

---

**Rol:** Nesne yönelimli yazılım tasarımı ve yazılım kalitesi metrikleri konusunda uzman bir yazılım mimarısın.

**Görev:** jsoup'un 10 ardışık sürümünden (1.13.1 → 1.22.1) statik analizle çıkarılmış nesne yönelimli tasarım metriklerini yorumlayarak tasarım kalitesinin sürümler boyunca nasıl evrildiğini analiz et. Yalnızca verilen sayısal verilere dayan; veride olmayan bilgileri uydurma. Emin olmadığın çıkarımları açıkça belirt.

**Metrik tanımları** (DSC ve NOH dışındaki değerler sürüm içindeki sınıfların ortalamasıdır):

- **DSC** — Tasarım boyutu: toplam sınıf sayısı.
- **NOH** — Hiyerarşi sayısı: bağımsız kalıtım ağacı sayısı.
- **ANA** — Ortalama ata sayısı (soyutlama): ortalama kalıtım derinliği.
- **DAM** — Veri erişim metriği (kapsülleme): gizli alanların oranı (0–1). **Yüksek = iyi.**
- **DCC** — Doğrudan sınıf bağlaşımı: bir sınıfın bağlı olduğu diğer sınıf sayısı. **Yüksek = kötü.**
- **CAM** — Metotlar arası kohezyon (0–1). **Yüksek = iyi.**
- **MOA** — Toplama/kompozisyon ölçütü: nesne tipli alan sayısı.
- **MFA** — İşlevsel soyutlama: kalıtılan metotların oranı (0–1).
- **NOP** — Çokbiçimli (geçersiz kılınabilir) metot sayısı.
- **CIS** — Sınıf arayüz boyutu: ortalama public metot sayısı.
- **NOM** — Karmaşıklık: sınıf başına ortalama metot sayısı. **Yüksek = daha karmaşık.**

**Veri:** On bir tasarım özelliğinin sürümlere göre ham değerleri (raporda Tablo I):

```
version,DSC,NOH,ANA,DAM,DCC,CAM,MOA,MFA,NOP,CIS,NOM
1.13.1,242,2,2.012,0.752,4.818,0.853,0.905,0.240,2.983,3.360,6.091
1.14.1,246,2,2.000,0.744,4.976,0.852,0.919,0.239,3.073,3.467,6.280
1.15.1,250,2,2.012,0.750,5.012,0.842,0.976,0.241,3.080,3.468,6.348
1.16.1,253,2,2.016,0.741,5.051,0.835,0.992,0.241,3.198,3.581,6.522
1.17.1,261,2,2.023,0.698,5.046,0.806,1.077,0.243,3.395,3.728,6.793
1.18.1,265,2,1.989,0.712,5.155,0.806,1.113,0.236,3.438,3.815,6.891
1.19.1,270,3,1.981,0.669,5.289,0.799,1.144,0.235,3.448,3.848,6.985
1.20.1,275,3,1.978,0.623,5.436,0.795,1.091,0.238,3.476,3.942,7.178
1.21.1,285,3,2.032,0.645,5.533,0.792,1.095,0.250,3.460,3.919,7.186
1.22.1,290,4,2.017,0.654,5.524,0.788,1.103,0.248,3.448,3.928,7.238
```

**İstenen analizler** — her biri ayrı başlık altında ve veriden sayısal örneklerle:

1. **Genel Kalite Değerlendirmesi:** Sürümler boyunca genel tasarım kalitesi nasıl değişmiş? Belirgin iyileşme/bozulma eğilimleri hangileri?
2. **Bakım Yapılabilirlik Analizi:** Bağlaşım (DCC), kohezyon (CAM), karmaşıklık (NOM) ve kapsülleme (DAM) açısından bakım yapılabilirlik nasıl etkilenmiş?
3. **Teknik Borç Tahmini:** Hangi sürüm(ler)de teknik borç biriktiğine dair belirtiler var? Hangi metrikler buna işaret ediyor?
4. **Refactoring Önerileri:** Tespit ettiğin sorunlara yönelik somut yeniden düzenleme önerileri.
5. **Mimari Kalite Yorumları:** Genişletilebilirlik, soyutlama ve modülerlik açısından mimari kalite nasıl?
6. **QMOOD Kalite Nitelikleri Eğilimi:** Aşağıdaki altı nitelik için her birinin zaman içindeki eğilimini **(Artıyor / Azalıyor / Durağan)** ve tek cümlelik gerekçesini bir tabloda ver: Yeniden Kullanılabilirlik, Esneklik, Anlaşılabilirlik, İşlevsellik, Genişletilebilirlik, Etkinlik.

**Çıktı biçimi:** Yukarıdaki altı başlığı aynı sırayla kullan; altıncı başlığı tablo (Nitelik | Eğilim | Gerekçe) olarak ver; en sonda 3–4 cümlelik genel bir sonuç paragrafı ekle.

---

## Prompt Mühendisliği Süreci

İstem üç yinelemeyle son hâline getirilmiştir:

- **v1:** Yalnızca "bu metrikleri değerlendir" dendi → çıktılar yüzeysel kaldı, modeller bazı kısaltmaları (örn. yüksek DCC'yi olumlu) yanlış yorumladı.
- **v2:** Metrik tanımları ve "yüksek = iyi/kötü" yönleri eklendi → yorumlar tutarlılaştı.
- **v3 (son):** Altı analiz başlığı, "yalnızca veriye dayan, uydurma" kısıtı ve yapılandırılmış çıktı biçimi eklendi → modeller arası karşılaştırma mümkün oldu.

> Not: Karşılaştırmanın yanlılığını önlemek için modellere QMOOD kalite skorları **verilmemiş**, yalnızca ham metrikler sunulmuştur.