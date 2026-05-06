---
layout: post
title: "Kıyamete Hazır Olmak: 170 GB'lık Çevrimdışı Bilgi Arşivi"
date: 2026-05-06
---

Bugüne dek hep KendiEgemen kanalımda ve blog post'larımda offline LLM, güneş paneli, jeneratör gibi konulara değindim. Ama offline LLM'ler halüsinasyona uğruyor; o yüzden sağlam veri kaynaklarına da ihtiyaç var. Bu post'ta tam olarak bu konuya değineceğim.

Üç tane "ya olursa" senaryosu var:

1. **Savaş çıkar** — fiber kesilir, BTK kapatır, hücresel şebeke düşer.
2. **Doğal afet** — deprem, kasırga, elektrik kesintisi haftalarca sürer.
3. **Sadece kişisel** — uçaktasın, tekneyle açıldın, dağ köyünde kaldın, internetle aran iyi değil.

Üçünde de aynı şey lazım: **internet olmadan da çalışan bilgi**. Bu klasör (`/mnt/d/wikipedia`) tam olarak bunun için. Yaklaşık **170 GB** veri — bir dış diske sığar, bir SSD'ye fazlasıyla sığar, yedeklenebilir, taşınabilir.

İçinde ne var ve neden orada — sırayla.

---

## Genel envanter

| Kategori | Boyut | Ne için |
|---|---|---|
| İngilizce Wikipedia (3 format) | ~140 GB | Genel bilgi — her şeyin bir başlangıcı |
| Türkçe Wikipedia (3 format) | ~10 GB | Aynısının yerel dili |
| OpenStax kitapları (122 ders kitabı) | 11 GB | Üniversite seviyesi eğitim — matematik, fizik, biyoloji, anatomi, hukuk… |
| Tıbbi/saha arşivi (`offline-medical-survival/`) | 622 MB | Doktor yokken/sağlık sistemi çökmüşken |
| Kiwix Desktop + DuckDB araçları | 470 MB | Her şeyi açabilmek için |
| **Toplam** | **~170 GB** | |

Bir 256 GB veya 512 GB taşınabilir SSD bunu rahat kaldırır. Maliyet: birkaç bin lira. Ödülü: dünyayla bağlantın koptuğu zaman elinde insanlığın bilgisinin önemli bir kısmı.

---

## Wikipedia — neden üç farklı format?

Aynı Wikipedia, farklı amaçlar için farklı formatlarda:

### `wikipedia_en_all_maxi_2026-02.zim` (124 GB) ve `wikipedia_tr_all_maxi_2026-02.zim` (9 GB)

Kiwix'in ZIM formatı. **Görsellerle birlikte tüm Wikipedia**. Tarayıcıda gezilen Wikipedia deneyimini birebir alır — sadece offline. Şubat 2026 anlık görüntüsü.

Nasıl kullanılır: `kiwix-desktop_windows_x64_2.5.1/` içindeki `kiwix-desktop.exe`'yi çalıştır → Dosya → ZIM aç. Veya `kiwix-serve.exe` ile yerel ağda yayınla, telefondan da gir.

> **Doomsday önerisi:** Bu dosya en kritik tek parça. Her şeyden önce bunu bir ikinci diske yedekle.

### `enwiki-latest-pages-articles.xml.bz2` (24 GB) ve `trwiki-latest-pages-articles.xml.bz2` (983 MB)

Wikipedia'nın ham XML dump'ı. ZIM'den daha taze (Mayıs 2026), ama görsel yok ve ham wikitext halinde. Boru hattı kuranlar, kendi parser'ını yazanlar veya daha güncel içerik isteyenler için.

### `hf_wikipedia/20231101.{en,tr}/*.parquet` (12 GB)

HuggingFace'in temizlenmiş, parquet-formatlı Wikipedia'sı. 2023 Kasım anlık görüntüsü ama metin **temiz** — şablonlar açılmış, markup ayıklanmış. SQL'le aramak, pandas'la analiz etmek, makine öğrenimi için kullanmak için ideal.

`duckdb_cli-windows-amd64.zip` içindeki DuckDB CLI ile:

```sql
SELECT title FROM 'D:/wikipedia/hf_wikipedia/20231101.tr/*.parquet'
WHERE title ILIKE '%Atatürk%';
```

Üç format birbirini tamamlıyor: **ZIM = okuma**, **Parquet = arama/analiz**, **XML = en taze, kendi araçların**.

---

## OpenStax — bir üniversite kitaplığı

`openstax/` klasöründe **122 PDF** — OpenStax'in (Rice University) açık ders kitaplığının tamamı. Hepsi peer-reviewed, ücretsiz, kullanım hakkı serbest.

Kapsam: matematik (algebra, calculus 1-3, contemporary math), fizik, kimya, biyoloji (genel + AP + concepts), anatomi ve fizyoloji, astronomi, mikroekonomi, makro, iş etiği, iş hukuku, Amerikan hükümeti, sosyoloji, psikoloji, antropoloji, klinik hemşirelik becerileri, ek olarak İspanyolca calculus çevirileri…

Bir genç bu klasörle lise + lisans birinci-ikinci sınıf müfredatının çoğunu öz öğrenebilir. Elektrik biraz olduğu sürece — ki olmasa bile bir tablette PDF okumak için pil gerekir, bunu da güneş paneli halleder.

> **Doomsday önerisi:** Çocuğun varsa veya gelecekte olursa, bu klasör eğitim için en güçlü kaynak. Khan Academy / Kolibri eklemek de fena olmaz (`/mnt/d/wikipedia` dışında, ileri proje).

---

## `offline-medical-survival/` — savaş ya da kıyamet sonrası tıp

Bu klasör en küçüğü (622 MB) ama bir o kadar da kritik. Doktor yok, eczane yok, ambulans yok senaryolarında elindeki tek başvuru.

13 alt klasör var. Her biri farklı bir problem alanı:

| Klasör | İçerik | Ne zaman lazım |
|---|---|---|
| `hesperian/` (357 MB) | Hesperian Vakfı'nın *Where There Is No Doctor* 2025 ve *Where There Is No Dentist* 2020'inin tüm bölümleri | Doktorsuz bir köyde / kampta günlük tıp |
| `military/` (98 MB) | ABD Ordusu sahra el kitapları: Özel Kuvvetler Tıbbi El Kitabı (ST 31-91B), İlk Yardım (FM 4-25.11), Survival (FM 21-76 + 3-05.70), Su Kaynağı/Kuyu (FM 5-484), Taktik Telsiz (FM 24-18 + 6-02.53), Acil Savaş Cerrahisi 5. baskı | Saha hayatı, savaş tıbbı, su kaynağı, telsiz haberleşmesi |
| `who-iris/` (37 MB) | WHO'nun en önemli klinik kitapçıkları: Çocuklar için Hastane Bakımı, İlk Düzey Bakım, Cerrahi Bakım, Temel İlaçlar Listesi 2023, Anneler için Hastane Bakımı, Doğum Komplikasyonları, Antenatal Bakım, IMCI çocuk yönetimi, IMAI yetişkin yönetimi, COVID-19 klinik yönetimi | Hastane düzeyi tedavi, ilaç dozları, klinik karar verme |
| `who-icrc/` (22 MB) | WHO/ICRC Temel Acil Bakım PDF'i + bağlantılı sayfalar | Triaj, akut hasta yönetimi |
| `first-aid/` (22 MB) | IFRC Küresel İlk Yardım Kılavuzları + ICRC İlk Yardım kitapçığı + Kızılhaç sayfaları | Ev/saha ilk yardımı |
| `merck-msd/` (16 MB) | MSD Manuals referans sayfaları (apps daha iyi ama bu yedek) | Hastalık/ilaç hızlı referans |
| `msf/` (16 MB) | Sınır Tanımayan Doktorlar'ın 5 klinik rehberi (Klinik Rehber, Temel İlaçlar, Akıl Sağlığı, vb.) — sahra koşulları için altın standart | Düşük kaynaklı sahada her şey |
| `icrc-war-surgery/` (5.5 MB) | ICRC Savaş Cerrahisi Cilt 1 — sınırlı kaynaklı çatışma cerrahisi | Cerrahi müdahale gerektiğinde |
| `cdc/` (5 MB) | CDC acil malzeme listesi + Yellow Book seyahat tıbbı sayfaları | Hazırlık, tropik/seyahat hastalıkları |
| `drug-labels/` (8 MB) | DailyMed + FDA Orange Book sayfaları | İlaç etkileşimleri, dozaj |
| `openstax/` (39 MB) | OpenStax Anatomi & Fizyoloji (üst klasördeki ile aynı kitap) | Anatomi referansı |
| `maritime/` (2.2 MB) | WHO Uluslararası Gemi Tıp Rehberi | Gemide / izole bölgede tıbbi karar |
| `niosh/` (1.7 MB) | NIOSH Kimyasal Tehlikeler Cep Rehberi | Kimyasal kazalar, zehirlenmeler |

Bu arşiv `health.sh` (wget tabanlı) ve `health_fixup.py` (WHO IRIS DSpace 7 API'si için Python) tarafından kuruldu. İkisi de **idempotent** — tekrar çalıştırırsan sadece eksikleri çekiyor, mevcudu atlıyor.

**Boşluk:** `wilderness/` klasörü boş. Wilderness Medical Society rehberleri ve PMC/SagePub makalelerinin hepsi paywall arkasında. Kısmen `military/`'deki survival ve hesperian'daki kırsal tıp kapsıyor ama tam karşılığı değil.

---

## Araçlar

`/mnt/d/wikipedia` içindeki iki tane `.zip`:

- **kiwix-desktop_windows_x64_2.5.1.zip** — ZIM dosyalarını okumak için. Açıp `kiwix-desktop.exe`'yi çalıştır.
- **duckdb_cli-windows-amd64.zip** — Parquet dosyalarını okumak için. Aç, `duckdb.exe`'yi PATH'e ekle.

`README.md` (eski, bu post öncesi) bunların kullanımını detaylı anlatıyor. Buradaki post ise "neden" sorusunu cevaplıyor.

---

## Threat model — bu arşiv neye karşı?

| Senaryo | Bu arşiv yardım eder mi? | Notlar |
|---|---|---|
| Kısa internet kesintisi (saatler) | Aşırı işe yarar | Wikipedia/OpenStax tek başına yeter |
| Uzun süreli yerel kesinti (haftalar) | Çok | Tıbbi arşiv + Wikipedia hayat kurtarır |
| Bölgesel savaş | Evet, ciddi | Sahra tıbbı, su kaynağı, telsiz manualleri |
| Toplumsal çöküş (yıllar) | Sınırlı ama kritik | Eğitim ve tıp yeniden inşa için temel |
| Kişisel offline ihtiyaç (uçak, tekne, dağ) | Tabi | Hızlıca lazım olan her şey burada |

Kapsam dışı kalanlar:
- **Tarım & gıda** — FAO yayınları, Survivor Library, Plants For A Future yok.
- **Yerel haritalar** — OpenStreetMap dump'ı yok (eklenebilir, ~1 GB Türkiye için).
- **Hayvancılık/veterinerlik** — Merck Veterinary Manual yok.
- **Yasal kodlar** — TR Resmi Gazete arşivi yok.
- **Klasik literatür** — Project Gutenberg yok.

Hepsi eklenebilir; her biri ayrı bir hafta-sonu projesi.

---

## Yenileme ve bakım

Bilgi bayatlar:
- Wikipedia ZIM'leri Kiwix'ten ayda bir yeniden — `download.kiwix.org/zim/wikipedia/` kontrol et.
- `health.sh` ve `health_fixup.py`'ı yılda bir kez çalıştır — dosyaları `wget -c` zaten atlıyor, eksikleri tamamlıyor.
- WHO/ICRC/MSF rehberleri 2-5 yılda bir güncellenir.
- `_openstax_download.py` yeni kitapları ekler.
- İlaç bilgileri en hızlı bayatlayanlar — `drug-labels/` her yıl tazelenmeli.

Diskleri **iki yerde** tut. Bir tanesi ana çalışma diskin, bir tanesi başka bir fiziksel mekânda — yangın, hırsızlık, sel için.

---

## Son söz

Bu klasörü kurmak bir hafta sonu sürdü ama bir gün gerekirse on yıllık bilgiye karşılık geliyor. Kıyamete hazırlık paranoyak bir hobi değil; **sigorta**. Sigortayı yangın çıkmasını umut ederek almazsın — çıkmamasını umut ederek alırsın ama yine de alırsın.

Şu anda elindekiler: dünyanın en büyük ansiklopedisi (iki dilde), bir üniversitenin müfredatı, sahada hayat kurtaran tıp el kitapları, askerî saha kılavuzları, su, telsiz, navigasyon. Kabloyu çek, çalışmaya devam ediyor.

İyi haber: bunu yapan başkaları da var. Kiwix kütüphanesi binlerce ZIM sunuyor. Kötü haber: sen kendin yapmasan kimse senin için hazırlamayacak.

Bir de şu: bu dosyayı okumayı bitirdiğine göre, yedekle. Hemen.

Olası bir felaket senaryosunda LLM'inize bu dokümanlar üstünde RAG yaptırarak (LM Studio bunu natively destekliyor) kritik konularda factual bilgiye halüsinasyon riski olmaksızın ulaşabilirsiniz.
