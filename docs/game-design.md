<!-- Kaynak: docs/issues/tohum.md (epic bölümü). GitHub'daki epic issue ile aynı metin; karar değişirse önce burası, sonra epic güncellenir. -->

# Tohum — süresiz, seviye arası reklamsız, dürüst bahçe bulmacası

## Neden
Sahibi (2026-10-14): Fluffy Drop (UniRise, HK) 6 ayda 1M+ indirmeyi mekanikle değil dağıtım tarifiyle aldı: başlıkta "No Timer", Trivia kategorisinde #2, 60+ kitle, haftalık sürüm, 15 dil. Yorumlardaki şikâyetler ise birbirinin aynısı: her seviye sonrası interstitial, çalışmayan No Ads, harcanacak yeri olmayan coin, ana menüye dönüş yok, "no timer" vaadini tutmayan seviyeler. Yani kitle var, sözleşme bozuk. Tohum aynı kitleye aynı vaadi verir ve tutar: süre yok, seviye arası reklam yok, günde en fazla 3 interstitial ve yalnızca haritada, No Ads her şeyi kapatır, coin'in gerçek bir mağazası var, ana menü her ekranda.

Mekanik olarak fark iddiası yok; bu bilinçli. Kaydırmalı bulmaca (tohum bir yöne kayar, engele çarpana kadar durmaz) öğrenmesi 10 saniye, ustalaşması 200 seviye süren, senior ve çocuk için eşit okunabilir bir çekirdek. Fark, ekranın üstündeki dürüstlük şeridi ve onu doğrulayan her ekrandır.

Araştırma raporu ve tasarım taslakları: https://claude.ai/code/artifact/8ab0af47-8a63-4fac-8fd7-aad383f2fe81 (bölüm 5, diğer fikirler: Tohum) ve https://claude.ai/artifact/3VMA4d5qTn8GF89PDf5Uj9 (Tohum · Oyun artboard'u: dürüstlük şeridi "Süre yok · Bugün reklam 1/3 · Seviye arası reklam yok", sürüklenen tohum ve noktalı iz, üç renkli tarh biri çiçek açmış, coin fiyatlı boosterlar, "Bahçem 12/40").

Portföydeki rolü: "fast follower" dolgu oyunu; Sort benzeri demokratik pazarda (lider %18, bir düzine oyun 5–15 M USD) mekanik yarışına girmeden, kanıtlanmış bir kitlenin kırılan güvenini satın alır. Solo MVP 4 hafta (seviye editörü dahil); `MustyCozyCore` paketinin ikinci tüketicisi (interstitial kotası, IAP, büyük yazı modu, meta harita iskeleti ilk kez burada tam yük altında).

## Hedef kitle ve konumlandırma
- Birincil: 55+ (Fluffy Drop'un yorum yazan kitlesi) ve 7–12 yaş çocuklar (ebeveyn telefonu). İkincil: 25–45 "sakin bulmaca" oyuncusu (Sort, Water Sort, Nonogram).
- Mağaza adı: **Tohum: No Timer Garden Puzzle** (EN) / **Tohum: Süresiz Bahçe Bulmacası** (TR). Anahtar kelimeler: no timer, no ads between levels, relaxing garden puzzle, color match slide puzzle, slide puzzle, flower garden.
- Konumlandırma cümlesi: "Süre yok. Seviye arasında reklam yok. Tohumu kaydır, tarhı doldur, bahçen açsın."
- Rakiplerden farkı: dürüstlük şeridi ekranda görünür bir sözleşmedir (süre yok · bugün reklam N/3 · seviye arası reklam yok); No Ads gerçekten her interstitial'ı kapatır; coin'in harcanacak yeri (booster + Bahçem dekoru) vardır; büyük yazı modu varsayılan açık; ana menü düğmesi her ekranda.
- Kategori denemesi: Fluffy Drop'un #2 trivia taktiği; soft launch Trivia kategorisinde, karar kapı raporunda.

## Core loop ve kurallar (v1 kararları)
1. Oyuncu haritada (ana ekran) sıradaki seviye düğümüne dokunur. Harita = ana menü: Bahçem, Mağaza, Ayarlar düğmeleri ve dürüstlük şeridi buradadır.
2. Tahta: ızgara, seviye 1–8'de 6×8, varsayılan 7×9, seviye 121+ için 8×11'e kadar. Hücre boyutu 390 px referans genişlikte 7 sütunda 48 px (tahta genişliği ekranın %92'si); büyük yazı modu hücreyi değiştirmez, metni değiştirir.
3. Nesneler: tohum (renkli top; 2–5 renk, seviyeye göre), tarh (tek hücre, renkli, kapasite 2–6, varsayılan 4; dolu yuva sayısı hücrenin içinde noktalarla görünür), taş (sabit), kütük (itilebilir), sulama kanalı (yönlü ok; giren tohumu o yöne çevirir), yaban otu (3 çarpmada temizlenir).
4. Kontrol: tohuma dokun ve bir yöne sürükle (eşik 24 px, baskın eksen yön olur). Parmak basılıyken tohumun duracağı hücreye kadar noktalı iz görünür; bırakınca tohum kayar. Kayma hızı hücre başına 45 ms, en fazla 400 ms. Kayma sırasında girdi kilitli.
5. Kayma kuralı: tohum engele (tahta kenarı, taş, yaban otu, başka tohum, kütük, farklı renk tarh) çarpana kadar kayar. Aynı renk tarha girerse tarhın bir yuvasını doldurur ve tahtadan kalkar. Kütüğe çarparsa kütük aynı yönde 1 hücre itilir (ilerisi boş hücreyse) ve tohum kütüğün boşalttığı hücrede durur; ilerisi doluysa kütük duvardır. Kanala girerse yön değişir, kayma sürer; kanalın çıkışı doluysa tohum kanal üstünde durur. Yaban otuna çarpınca tohum önünde durur, ot 1 can kaybeder (3 → 0 → hücre boşalır).
6. Kaskad: bir tarh dolunca 4-komşu hücrelerdeki kütükler kırılır (hücre boşalır). v1'de kırılan kütük başka bir şey tetiklemez.
7. Seviye bitişi: tüm tarhlar dolunca. Tarhlar sırayla çiçek açar (her biri 180 ms, toplam ≤ 1,2 sn), "Bahçe açtı" kartı: yıldız (1–3), coin (1★ 30, 2★ 45, 3★ 60; tekrar oynanan seviyede 10), Devam düğmesi. Hamle sınırı yok, süre yok, enerji yok.
8. Yıldız: 3★ hamle ≤ optimal, 2★ ≤ optimal + 3, 1★ her bitiriş. Optimal, editör BFS çözücüsünden gelir ve `LevelData`'da saklanır.
9. Tohum sayısı: her seviyede tarh kapasiteleri toplamı + 0–2 fazla. Kaybetme yok; tek olumsuz durum "çıkmaz" (hiçbir tohum aynı renk tarha ulaşamıyor): sarı şerit "Çıkmaz" + Geri al (ücretsiz 1/seviye) + Yeniden başlat + İpucu (100 coin; mevcut durumdan BFS çözümünün ilk hamlesi).
10. Boosterlar (oyun ekranı, coin fiyatı yazılı): Kürek 150 (bir taş ya da yaban otu kaldır), Yağmur 300 (tüm tohum ve kütükler seçilen yöne 1 hücre kayar), Arı 500 ya da ödüllü video (seçilen tohumu aynı renk, dolmamış bir tarha taşır).
11. Coin ekonomisi: kazanç seviye bitişi (30–60), günlük hediye (50–150), 5'in katı seviyelerde Bahçem tarhı açılışında 100. Harcama: booster, ipucu, Bahçem dekoru (çit 200, bank 400, kuş yuvası 600, fener 800, çeşme 1.200, sera 2.000). IAP coin paketleri: 500 coin 0,99 USD, 1.800 coin 2,99 USD, 3.500 coin 4,99 USD, 8.000 coin 9,99 USD. Tüm sayılar `EconomyConfig` (Remote Config).
12. Meta: 200 seviye, bahçe temalı harita (4 bölge × 50 seviye). Her 5 seviyede bir Bahçem'de bir tarh kalıcı açılır (40 tarh); Bahçem oyuncunun kendi bahçesi: açılan tarhlar çiçekli, dekor coin ile yerleştirilir.
13. Reklam sözleşmesi (dürüstlük şeridi, haritada ve oyun ekranının üstünde): süre yok · seviye arası zorunlu reklam yok · günde en fazla 3 interstitial, yalnızca harita ekranında, seviye 10'dan önce hiç, aralarında en az 8 dk · ödüllü video yalnızca oyuncunun seçimiyle (Arı, coin ikiye katlama) · No Ads 4,99 USD tüm interstitial'ı kapatır, ödüllü videoyu kapatmaz. Oyun ekranında banner yok.
14. Erişilebilirlik varsayılanları: büyük yazı modu açık (TextMeshPro auto-size kademeleri; gövde 20 pt, düğme 22 pt, dokunma hedefi ≥ 56 px), tohum ve tarhlarda renk körü sembolleri (daire, kare, üçgen, yıldız, altıgen) açık, Reduce Motion sistem ayarından okunur, ana menü (ev) düğmesi her ekranın sol üstünde, Android geri tuşu bir geri gider.
15. Kalıcılık: seviye ilerlemesi, yıldızlar, coin, Bahçem, No Ads durumu Cloud Save (Core) + yerel JSON; çakışmada en yüksek seviye ve en yüksek coin kazanır.

## Kapsam dışı (v1)
- Hamle sınırı, süre, enerji/can sistemi, "lives" satışı.
- Gerçek zamanlı çok oyunculu, lig, turnuva; asenkron yarış SONRA.
- Banner reklam; seviye bitişinde, oyun ekranında ya da Bahçem'de interstitial.
- Harita dışı LiveOps (mevsimsel etkinlik, günlük bulmaca) SONRA.
- iOS çıkışı MVP'de yok (Android soft launch sonrası).
- 200'den fazla seviye; Bahçem'de 40'tan fazla tarh.

## Başarı ölçütleri (kapılar)
- Soft launch (TR, PH, CA; Android; 14 gün; kategori Trivia): D1 ≥ %35, D7 ≥ %15, seviye 20'ye ulaşan / yükleyen ≥ %45, No Ads dönüşümü (satın alan / D3'e ulaşan) ≥ %1,5, çökme oranı < %0,5.
- İkincil: tek seviyede 3+ yeniden başlat oranı < %8 (takılma), çıkmaz uyarısı/seviye < 0,4, ortalama oturum ≥ 6 dk, Bahçem'i açan oyuncu oranı ≥ %60.
- Kapı geçilirse: iOS build, Puzzle vs Trivia kategori kararı, 201–300 seviye paketi, Canlı.
- Kapı geçilmezse: iki hafta iterasyon (takılma seviyeleri yeniden dengelenir, tutorial 1–8 ve coin kazancı ayarlanır), yine geçilmezse rafa; seviye editörü ve `SlideResolver` portföyde yeniden kullanılır.

## Temalar ve story'ler (sıra)
**P1 · MVP**
- [ ] Tohum 1: Core paketini bağla ve proje iskeleti
- [ ] Tohum 2: Izgara tahtası ve kaydırma kuralı
- [ ] Tohum 3: Tarh doldurma, yıldızlar ve seviye bitişi
- [ ] Tohum 4: Engeller: taş, kütük, sulama kanalı, yaban otu
- [ ] Tohum 5: Kütük kaskadı: tarh dolunca komşu kütükler kırılır
- [ ] Tohum 6: Çıkmaz tespiti, geri al, ipucu ve yeniden başlat
- [ ] Tohum 7: His: kayma sesi, çarpma, çiçek açma animasyonu
- [ ] Tohum 8: Seviye veri formatı ve seviye editörü
- [ ] Tohum 9: 200 seviye içerik planı ve zorluk eğrisi
- [ ] Tohum 10: Tutorial seviyeleri 1–8
- [ ] Tohum 11: Prosedürel seviye yardımcısı: çözümden geriye karıştırma
- [ ] Tohum 12: Harita ekranı ve seviye ilerlemesi
- [ ] Tohum 13: Bahçem meta sahnesi ve dekor
- [ ] Tohum 14: Coin ekonomisi: kazanç, günlük hediye, mağaza ve coin paketleri
- [ ] Tohum 15: Boosterlar: Kürek, Yağmur, Arı
- [ ] Tohum 16: Interstitial kotası, dürüstlük şeridi ve No Ads
- [ ] Tohum 17: Erişilebilirlik: büyük yazı varsayılan, renk körü sembolleri, ana menü
- [ ] Tohum 18: Analitik olay şeması ve kapı raporu
- [ ] Tohum 19: Mağaza sayfası, ASO ve soft launch yapılandırması
**P2 · Soft launch**
- [ ] Tohum 20: Ödüllü video yerleşimleri: coin ikiye katlama ve ek geri al
**P3 · Canlı**
- [ ] Tohum 21: Türkçe bitki ve çiçek koleksiyonu kartları
**SONRA**
- [ ] Tohum 22: Mevsimsel bahçe etkinlikleri
- [ ] Tohum 23: 5 kişilik asenkron yarış (opsiyonel, kapatılabilir)
- [ ] Tohum 24: Günlük bulmaca

## Riskler ve açık sorular
- Efor toplamı: P1 story'leri ~44 iş günü, hedef 4 hafta. Karar: soft launch 120 seviyeyle başlar (1–80 elle, 81–120 prosedürel + elle düzeltme), 121–200 soft launch'ın 1. ve 2. haftasında iki sürümle gelir (Fluffy Drop'un haftalık sürüm taktiği). Bahçem dekor v1'de 6 tip.
- Çıkmaz tespiti kesin değil: tek-tohum erişilebilirlik BFS'i "hiçbir tohum ulaşamıyor" der ama bir tohum ulaşabildiği hâlde seviye çözümsüz olabilir. Kabul: Yeniden başlat her zaman bir dokunuş uzakta; tam durum uzayı BFS'i yalnızca editörde ve 50.000 durum sınırıyla çalışma zamanında (6. story).
- Trivia kategorisi: Play, kategori değişimini sıralamada sıfırlayabilir; soft launch boyunca kategori değişmez, karar kapı raporunda verilir.
- "No Timer" başlık ve vaat: hiçbir seviyede sayaç, hamle sınırı, enerji yok. Remote Config ile bile açılamaz (kod yolu yok).
- Senior kitle için sürükleme hassasiyeti: eşik 24 px ve "Ok modu" (tohuma dokun, 4 ok çıkar) 17. story'de; soft launch'ta `input_mode` dağılımına bakılır.
- Klon algısı: Fluffy Drop'tan görsel olarak ayrışma (tüylü hayvan değil, tohum ve tarh; düz renk, kalın kenarlık, pastel toprak zemini). Mekanik olarak "slide puzzle" jenerik; tescil riski yok.
- Çocuk kitle: COPPA/aile politikası; reklam SDK'sında çocuk yönelimli işaret (`setTagForChildDirectedTreatment`) ve karma kitle beyanı 19. story'de; kişisel veri yok.

## Done when
P1 story'leri kapalı, 120 seviye soft launch'ta ve 200 seviye 2. hafta sonunda canlıda, soft launch 14 gün tamamlanmış, kapı raporu epic'e yorum olarak eklenmiş ve karar (Canlı / iterasyon / raf) ile kategori kararı (Trivia / Puzzle) yazılmış.
