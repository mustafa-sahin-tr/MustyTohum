<!-- game: Tohum -->
<!-- repo: MustyTohum -->
# EPIC: Tohum — süresiz, seviye arası reklamsız, dürüst bahçe bulmacası
labels: epic, enhancement

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

## STORY: Tohum 1: Core paketini bağla ve proje iskeleti
labels: tema:backend, P1, efor:M
milestone: MVP

**Tohum** epic'inin **1. story'si** (epic #EPIC). Tema: Altyapı.

**Öncelik:** P1 · **Efor:** M · **Milestone:** MVP · **Dokunduğu yer:** Unity projesi, `MustyCozyCore` UPM bağımlılığı, sahne düzeni, UGS kurulumu, `EconomyConfig`

### Kullanıcı hikâyesi
Bir geliştirici olarak Tohum projesinin ortak paketi (reklam mediation ve interstitial kotası, IAP, UGS, yerelleştirme, büyük yazı modu, ayarlar, meta harita iskeleti) ilk günden kullanmasını isterim, böylece 4 haftanın tamamını ızgara, editör ve seviyelere harcayayım.

### Kabul kriterleri
- [ ] **Senaryo: Temiz kurulum** · Verilen boş bir Unity 2022 LTS projesi (URP 2D) · Eğer `MustyCozyCore` paketi git URL ile eklenirse · O zaman proje derlenir ve `Bootstrap` sahnesi açılınca UGS oturumu (anonim Player Accounts) kurulur, Cloud Save okunur.
- [ ] **Senaryo: Sahne akışı** · Verilen `Bootstrap → Map → Play → Garden` sahneleri ve `LevelComplete` overlay'i · Eğer uygulama açılırsa · O zaman 2 sn içinde Map görünür; geri tuşu Map'te uygulamadan çıkmayı sorar, Play'de Map'e döner, Garden'da Map'e döner.
- [ ] **Senaryo: Ana menü düğmesi** · Verilen herhangi bir sahne · Eğer sol üstteki ev düğmesine basılırsa · O zaman Map'e dönülür; Play'de devam eden seviye kaydedilir (Fluffy Drop'un "ana menüye dönüş yok" hatası burada kapanır).
- [ ] **Senaryo: Yerelleştirme** · Verilen TR ve EN tabloları · Eğer cihaz dili TR ise · O zaman tüm metinler TR, aksi halde EN; dil Ayarlar'dan değiştirilebilir; 13 dil tablosu boş anahtarla EN'e düşer.
- [ ] **Senaryo: Build** · Verilen Android build ayarları · Eğer `release` build alınırsa · O zaman paket adı `com.mtafasahin.tohum`, min SDK 24, 64-bit, boyut < 70 MB.
- [ ] **Senaryo: Remote Config** · Verilen UGS Remote Config `EconomyConfig` · Eğer `booster_shovel_cost` 150'den 120'ye çekilirse · O zaman yeni oturumda oyun ekranındaki Kürek fiyatı 120 görünür.
- [ ] **Senaryo: Büyük yazı varsayılan** · Verilen ilk açılış · Eğer Ayarlar okunursa · O zaman `big_text = true` varsayılanıyla gelir ve Map'teki düğme metinleri 22 pt'dir.

### Teknik görevler (Unity/C#)
- [ ] Repo iskeleti: `Assets/_Game/{Scripts,Prefabs,Art,Audio,Levels,Data}`, `Assets/_Game/Scripts/{Board,Levels,Meta,Economy,UI,Editor}` assembly'leri, `.gitignore`, `docs/game-design.md` (bu epic'in kuralları), `docs/analytics.md`, `docs/levels.md`.
- [ ] `MustyCozyCore` bağımlılığı (UPM git URL, sürüm etiketi ≥ Daily Drop'un kullandığı sürüm); bu oyunun ihtiyacı olup pakette eksikse paket story'si açılır: `AdQuota` (günlük interstitial sayacı), `BigTextMode` (TMP auto-size kademe seti), `MapScaffold` (düğüm listesi + kilit), `DailyGift`, `ShopScreen` iskeleti.
- [ ] Sahne yöneticisi: `SceneFlow` (async yükleme, 200 ms geçiş perdesi), `AppState` (ScriptableObject tabanlı oturum durumu: aktif seviye, coin önbelleği).
- [ ] `HomeButton` prefab'ı (Core `BackButtonRouter` ile aynı yönlendirme): her sahnenin sol üstünde, 56×56 px dokunma alanı.
- [ ] UGS: Player Accounts anonim giriş, Cloud Save, Analytics, Remote Config projesi ve ortamları (`dev`, `prod`); Leaderboards v1'de kapalı.
- [ ] `EconomyConfig` Remote Config şeması: `level_coin_1star/2star/3star`, `replay_coin`, `hint_cost`, `booster_shovel_cost`, `booster_rain_cost`, `booster_bee_cost`, `bee_rewarded_enabled`, `interstitial_daily_cap`, `interstitial_min_level`, `interstitial_min_gap_min`, `daily_gift_table`, `decor_prices`, `coin_pack_skus`.
- [ ] CI: GitHub Actions ile Android debug build (GameCI); artefakt olarak APK; Edit Mode testleri CI'da koşar (ızgara ve çözücü testleri 2., 4., 8. story'lerde eklenir).
- [ ] Kod stili ve analiz: `.editorconfig`, Roslyn analizörleri, Assembly Definition dosyaları (Game, Game.Editor, Game.Tests).

### Analitik
- `app_open` {first_open: bool, locale: string, build: int, big_text: bool} — her açılışta
- `session_start` {max_level: int, coins: int} — Map görünür olduğunda

### Notlar
- Karar: Reklam mediation AppLovin MAX (Daily Drop ile aynı); interstitial yerleşimi tek: `map_return`. Ödüllü yerleşimler: `bee`, `coin_double` (P2), `extra_undo` (P2).
- `AdQuota` Core'a bu oyunla girer; Daily Drop interstitial kullanmadığı için paket tarafı yeni iştir (efor bu story'de M, pakette ayrıca S).
- Çocuk kitle: MAX SDK `setIsAgeRestrictedUser` ve Play aile politikası beyanı 19. story'de; burada yalnızca SDK bayrağı için `AudienceConfig` alanı açılır.

## STORY: Tohum 2: Izgara tahtası ve kaydırma kuralı
labels: tema:cekirdek, P1, efor:M
milestone: MVP

**Tohum** epic'inin **2. story'si** (epic #EPIC). Tema: Çekirdek oynanış.

**Öncelik:** P1 · **Efor:** M · **Milestone:** MVP · **Dokunduğu yer:** `GridBoard`, `CellType`, `Seed`, `SlideResolver`, `BoardView`, `DragInput`, `Play` sahnesi

### Kullanıcı hikâyesi
Bir oyuncu olarak bir tohumu bir yöne sürüklediğimde nereye kadar kayacağını parmağımı kaldırmadan görmek ve bırakınca tam oraya kaymasını isterim, böylece her hamle önceden okunabilir olsun ve yanlışlıkla hamle yapmayayım.

### Kabul kriterleri
- [ ] **Senaryo: Tahta ölçüsü** · Verilen 390×844 referans ekran ve 7×9 tahta · Eğer Play açılırsa · O zaman tahta genişliği ekranın %92'si, hücre 48 px, hücreler arası boşluk 4 px, tahta dikeyde ortalanır; 6×8 ve 8×11 tahtalarda hücre boyutu genişliğe göre hesaplanır (en az 36 px).
- [ ] **Senaryo: Sürükleme ve yön** · Verilen bir tohum · Eğer parmak tohuma basıp 24 px'den fazla hareket ederse · O zaman baskın eksen yön olur (dx > dy ise yatay), yön parmak basılıyken değişebilir (eşik yeniden aşılınca).
- [ ] **Senaryo: Noktalı iz** · Verilen yön belirlenmiş sürükleme · Eğer parmak basılıysa · O zaman tohumdan duracağı hücreye kadar noktalı iz çizilir ve hedef hücre %20 vurgulanır; kayma 0 hücreyse iz yok ve tohum hafifçe titrer.
- [ ] **Senaryo: Kayma** · Verilen bırakılan parmak ve hedef hücre · Eğer hamle geçerliyse · O zaman tohum hücre başına 45 ms (en fazla 400 ms) hızla kayar, kayma boyunca girdi kilitli, bitince `MoveCommitted` olayı yayılır ve hamle sayacı +1.
- [ ] **Senaryo: Duvarlar** · Verilen tahta kenarı, taş ya da başka tohum · Eğer kayan tohum bunlara ulaşırsa · O zaman bir önceki hücrede durur; hiçbir zaman üst üste binmez.
- [ ] **Senaryo: İptal** · Verilen sürüklenen tohum · Eğer parmak eşiğin altına dönerse ya da başka bir parmak dokunursa · O zaman hamle yapılmaz, iz kaybolur, tohum yerinde kalır.
- [ ] **Senaryo: UI önceliği** · Verilen HUD düğmesi üstünde başlayan dokunma · Eğer sürüklenirse · O zaman tahta girdi almaz.
- [ ] **Senaryo: Determinizm** · Verilen aynı tahta ve aynı hamle · Eğer `SlideResolver` iki kez çalıştırılırsa · O zaman aynı hedef hücre döner (float yok, yalnızca int koordinat).

### Teknik görevler (Unity/C#)
- [ ] `CellType` enum {Empty, Stone, Log, Channel, Weed, Bed}; `GridBoard` (width, height, `Cell[,]`, `Dictionary<int, Seed>` tohumlar, `Bed[]`, `Log[]`); `Cell` struct {type, bedId, channelDir, weedHp}.
- [ ] `Seed` {id, color: SeedColor (0–5), pos: Vector2Int}; `Direction` enum {Up, Right, Down, Left} ve `Vector2Int` dönüşümü.
- [ ] `SlideResolver.Resolve(GridBoard, seedId, Direction) → SlideResult` {path: List<Vector2Int>, stopPos, outcome: Stopped | EnteredBed | Blocked, pushedLogId?, weedHitPos?}; bu story'de yalnızca Empty/Stone/Seed/kenar; Log, Channel, Weed, Bed davranışı 3. ve 4. story'de aynı sınıfa eklenir. Saf C#, Unity bağımlılığı yok (Game.Board assembly).
- [ ] `BoardView`: hücre prefab'ları (`SpriteRenderer`, 9-slice), tohum prefab'ı (renk + sembol katmanı, 17. story semboli doldurur), hücre ↔ dünya koordinat dönüşümü, `SafeArea` ile HUD kayması.
- [ ] `DragInput`: Unity Input System, `Pointer` aksiyonları; durum makinesi {Idle, Holding, Directed, Sliding}; `EventSystem.IsPointerOverGameObject` ile UI önceliği; çoklu dokunmada iptal.
- [ ] `SlidePreview`: `LineRenderer` noktalı materyal (Reduce Motion'da düz ince çizgi), hedef hücre vurgusu.
- [ ] `SlideAnimator`: `MoveCommitted` öncesi tween (kendi `Tween` yardımcı sınıfı; DOTween bağımlılığı yok), hücre başına süre `slide_ms_per_cell` (Remote Config).
- [ ] `MoveHistory`: her hamlede `BoardSnapshot` (tohum konumları, tarh dolulukları, kütük konumları, ot canları) yığını; 6. story geri al için kullanır.
- [ ] Edit Mode testleri (NUnit): kenar, taş, tohum engeli; 0 hücre kayma; 4 yön; 8×11 tahta sınırları.

### Analitik
- `move` {level: int, move_n: int, dir: string, cells: int, outcome: string} — her hamlede (yalnızca ilk 30 seviyede tam; sonrasında her 5. hamle, hacim için)

### Notlar
- Karar: tek sürükleme girdisi, "dokun-sonra-dokun" yok; senior için "Ok modu" 17. story'de ayar olarak gelir, aynı `SlideResolver`'ı kullanır.
- Hücre boyutu, eşik ve kayma hızı Play modunda ayarlanır; Remote Config'ten gelir.
- Tasarım taslağı: Tohum · Oyun artboard'u — sürüklenen tohum ve noktalı iz.

## STORY: Tohum 3: Tarh doldurma, yıldızlar ve seviye bitişi
labels: tema:cekirdek, P1, efor:M
milestone: MVP

**Tohum** epic'inin **3. story'si** (epic #EPIC). Tema: Çekirdek oynanış.

**Öncelik:** P1 · **Efor:** M · **Milestone:** MVP · **Dokunduğu yer:** `Bed`, `SlideResolver` (tarh dalı), `LevelRunner`, `StarRule`, `LevelComplete` overlay

### Kullanıcı hikâyesi
Bir oyuncu olarak aynı renk tohumu tarha kaydırınca tarhın dolduğunu, yanlış rengin tarha giremediğini ve tüm tarhlar dolunca seviyenin bittiğini net görmek isterim, böylece hedefim her an belli olsun.

### Kabul kriterleri
- [ ] **Senaryo: Aynı renk girer** · Verilen kapasitesi 4, 2 dolu mavi tarh · Eğer mavi tohum tarha doğru kayarsa · O zaman tohum tarh hücresine girer, tahtadan kalkar, tarhın 3. noktası dolar ve hamle `EnteredBed` sonucu verir.
- [ ] **Senaryo: Yanlış renk duvardır** · Verilen mavi tarh · Eğer kırmızı tohum tarha doğru kayarsa · O zaman tohum tarhtan bir önceki hücrede durur ve tarh 120 ms "hayır" sallanması yapar (Reduce Motion'da yalnızca kenarlık kızarır).
- [ ] **Senaryo: Dolu tarh duvardır** · Verilen 4/4 dolu mavi tarh · Eğer mavi tohum tarha doğru kayarsa · O zaman tohum tarhın önünde durur; tarh kapasitesi aşılmaz.
- [ ] **Senaryo: Tarh görünümü** · Verilen kapasite 4 tarh · Eğer tahta çizilirse · O zaman tarh hücresi renkli çerçeve + içinde 4 yuva noktası (dolu/boş), büyük yazı modunda noktalar yerine "2/4" metni.
- [ ] **Senaryo: Seviye bitişi** · Verilen son tarhın son yuvası · Eğer dolarsa · O zaman girdi kilitlenir, tarhlar sırayla çiçek açar (7. story animasyonu; burada 180 ms aralıkla durum değişimi) ve 1,2 sn sonra `LevelComplete` overlay'i açılır.
- [ ] **Senaryo: Yıldız** · Verilen `LevelData.optimalMoves = 9` · Eğer oyuncu 9 hamlede bitirirse 3★, 12'de 2★, 13 ve üstünde 1★ · O zaman overlay'de yıldızlar ve "9 hamle · en iyi 9" satırı görünür.
- [ ] **Senaryo: Overlay içeriği** · Verilen bitmiş seviye · Eğer overlay açılırsa · O zaman yıldızlar, kazanılan coin (14. story; burada yalnızca gösterim), "Devam" (birincil), "Tekrar oyna" (ikincil), ev düğmesi; reklam yok, geri sayım yok.
- [ ] **Senaryo: Fazla tohum** · Verilen tüm tarhlar dolu, tahtada 2 tohum kaldı · Eğer seviye biterse · O zaman kalan tohumlar sorun değildir, overlay açılır.

### Teknik görevler (Unity/C#)
- [ ] `Bed` {id, color, capacity, filled, pos}; `GridBoard.Beds`; `SlideResolver`'a Bed dalı: aynı renk ve `filled < capacity` ise `EnteredBed`, aksi hâlde duvar.
- [ ] `LevelRunner`: `LevelData` yükler, `GridBoard` kurar, `MoveCommitted` dinler, `AllBedsFull` kontrolü, `LevelState` enum {Loading, Playing, Completing, Completed}; hamle sayacı.
- [ ] `StarRule.Compute(moves, optimalMoves) → int` (3: ≤ optimal; 2: ≤ optimal + 3; 1: diğer); `star_window` Remote Config.
- [ ] `BedView`: çerçeve, yuva noktaları, "n/k" metni (büyük yazı modu), sallanma tween'i, `Bloomed` durumu.
- [ ] `LevelCompleteOverlay` (uGUI + TMP): yıldız, coin, Devam, Tekrar oyna, ev; `LevelResult` {level, moves, stars, hintsUsed, boostersUsed, undoUsed, durationS}.
- [ ] `ProgressService`: `levelStars[level]` (en yüksek), `maxLevel`; Cloud Save (Core) + yerel JSON; Tekrar oyna sonucu yıldızı yalnızca yükseltir.
- [ ] Edit Mode testleri: aynı renk girer, yanlış renk durur, dolu tarh durur, `StarRule` sınırları, `AllBedsFull`.

### Analitik
- `level_start` {level: int, attempt: int, board: string ("7x9"), seeds: int, beds: int} — seviye yüklendiğinde
- `level_complete` {level: int, moves: int, optimal: int, stars: int, duration_s: int, undo_used: int, hints: int, boosters: int, restarts: int} — overlay açıldığında

### Notlar
- Karar: tarh kapasitesi hücrede görünür; gizli bilgi yok (senior ve çocuk için okunabilirlik).
- Overlay'de reklam olmaması epic'in sözleşmesidir; 16. story'deki `AdQuota` bu overlay'e hiç bağlanmaz (kod yolu yok).
- Yıldız penceresi Play modunda ayarlanır; Remote Config.

## STORY: Tohum 4: Engeller: taş, kütük, sulama kanalı, yaban otu
labels: tema:cekirdek, P1, efor:L
milestone: MVP

**Tohum** epic'inin **4. story'si** (epic #EPIC). Tema: Çekirdek oynanış.

**Öncelik:** P1 · **Efor:** L · **Milestone:** MVP · **Dokunduğu yer:** `SlideResolver` (Log, Channel, Weed dalları), `Log`, `ChannelCell`, `WeedCell`, `BoardView` engel prefab'ları

### Kullanıcı hikâyesi
Bir oyuncu olarak seviyeler ilerledikçe taş, itilebilir kütük, yönlendiren sulama kanalı ve üç çarpmada temizlenen yaban otu gibi yeni engellerin kuralını tek bakışta anlamak isterim, böylece 200 seviye boyunca öğrenecek yeni şey olsun ama hiçbiri sürpriz olmasın.

### Kabul kriterleri
- [ ] **Senaryo: Taş** · Verilen taş hücresi · Eğer tohum ya da kütük ona doğru kayarsa · O zaman bir önceki hücrede durur; taş hiçbir şeyle hareket etmez (Kürek hariç, 15. story).
- [ ] **Senaryo: Kütük itme** · Verilen kütük ve kütüğün ilerisinde boş hücre · Eğer tohum kütüğe çarparsa · O zaman kütük aynı yönde tam 1 hücre itilir, tohum kütüğün boşalttığı hücrede durur; kütük ve tohum aynı tween süresinde hareket eder.
- [ ] **Senaryo: Kütük duvar** · Verilen kütük ve ilerisinde taş, kenar, tohum, tarh, başka kütük ya da yaban otu · Eğer tohum kütüğe çarparsa · O zaman kütük itilmez, tohum kütüğün önünde durur.
- [ ] **Senaryo: Kütük tarha girmez** · Verilen kütüğün ilerisinde tarh · Eğer tohum kütüğü iterse · O zaman kütük itilmez (tarh kütük için duvardır).
- [ ] **Senaryo: Kanal yönlendirir** · Verilen sağa bakan kanal · Eğer tohum yukarıdan kanala girerse · O zaman tohum kanalın içinden geçip sağa doğru kaymaya devam eder; kanal hücresi geçilirken 1 hücre sayılır; kanalın çıkışı doluysa tohum kanal üstünde durur.
- [ ] **Senaryo: Kanal zinciri** · Verilen ardışık iki kanal (sağ, sonra aşağı) · Eğer tohum ilkine girerse · O zaman ikisini de izler; 20 yönlendirmeden sonra döngü koruması devreye girer ve tohum son kanalda durur.
- [ ] **Senaryo: Yaban otu** · Verilen 3 canlı yaban otu · Eğer tohum ona çarparsa · O zaman tohum otun önünde durur, ot 2 cana düşer ve görünümü incelir; 0 canda hücre boşalır (aynı hamlede tohum ilerlemez).
- [ ] **Senaryo: Tanıtım kartı** · Verilen bir engel tipinin ilk göründüğü seviye · Eğer seviye açılırsa · O zaman girdiyi kilitlemeyen tek satırlık kart ("Kütük: itebilirsin") 3 sn görünür ya da dokununca kapanır; bir kez.

### Teknik görevler (Unity/C#)
- [ ] `Log` {id, pos}; `GridBoard.Logs`; `SlideResolver` Log dalı: ilerisi `IsFree(pos + dir)` (Empty ve tohumsuz ve kütüksüz) ise `pushedLogId` ile sonuç; tween iki nesneyi birlikte oynatır.
- [ ] `ChannelCell` {dir}; `SlideResolver` Channel dalı: path'e kanal hücresi eklenir, `currentDir = channel.dir`, döngü sayacı ≤ 20; kanala giren tohum kanalda duramazsa (çıkış dolu) `stopPos = channel.pos`.
- [ ] `WeedCell` {hp: 3}; Weed dalı: `weedHitPos` ile sonuç; `LevelRunner` hp düşürür, 0'da `CellType.Empty`.
- [ ] Engel prefab'ları (`BoardView`): taş (gri, kalın kenarlık), kütük (kahverengi, yatay çizgili), kanal (açık mavi, büyük ok; büyük yazı modunda ok %120), yaban otu (3 kademe sprite: gür, seyrek, tek yaprak).
- [ ] `ObstacleIntroCard`: `LevelData.introduces` alanından okur (8. story), `TutorialFlags` ile bir kez gösterir; Core `HintBubble`.
- [ ] `MoveHistory` snapshot'ına kütük konumları ve ot canları eklenir (6. story geri al tam döner).
- [ ] Edit Mode testleri: kütük itme/durma 6 durum, kanal tek/zincir/döngü/çıkış dolu, ot 3→0, kütük-tarh, kütük-kütük.
- [ ] `docs/game-design.md`: engel kural tablosu (her engel × her çarpan: tohum, kütük).

### Analitik
- `obstacle_intro` {type: string, level: int} — tanıtım kartı gösterildiğinde
- `move` parametrelerine ekle: {pushed_log: bool, weed_hit: bool, channel: int}

### Notlar
- Karar: kütük yalnızca tohumla itilir, kütük kütüğü itmez (zincir itme yok): durum uzayı küçük kalır, BFS çözücü (8. story) hızlı kalır.
- Karar: kanal tohumu yönlendirir, kütüğü yönlendirmez (kütük kanala giremez, kanal kütük için duvardır).
- Yaban otu "3 geçiş" = 3 çarpma; tohum otun içinden geçmez. Açık soru: ot temizlenince aynı hamlede devam etmeli mi? v1: hayır (okunabilirlik).
- Engel görünümleri renk körü sembolleriyle değil biçimle ayrışır; renk yalnızca tohum ve tarhlarda anlam taşır.

## STORY: Tohum 5: Kütük kaskadı: tarh dolunca komşu kütükler kırılır
labels: tema:cekirdek, P1, efor:S
milestone: MVP

**Tohum** epic'inin **5. story'si** (epic #EPIC). Tema: Çekirdek oynanış.

**Öncelik:** P1 · **Efor:** S · **Milestone:** MVP · **Dokunduğu yer:** `CascadeRule`, `LevelRunner`, `BoardView` kırılma efekti

### Kullanıcı hikâyesi
Bir oyuncu olarak bir tarhı doldurduğumda yanındaki kütüklerin kırılıp yol açtığını görmek isterim, böylece tarh sırası bir strateji hâline gelsin ve dolum anı küçük bir ödül gibi hissettirsin.

### Kabul kriterleri
- [ ] **Senaryo: Komşu kütükler kırılır** · Verilen 4-komşusunda 2 kütük olan tarh · Eğer tarhın son yuvası dolarsa · O zaman 2 kütük de kırılır (hücreler Empty), kırılma dolumdan 150 ms sonra başlar, kütük başına 80 ms kayma.
- [ ] **Senaryo: Çapraz kırılmaz** · Verilen yalnızca çapraz komşuda kütük · Eğer tarh dolarsa · O zaman kütük kalır.
- [ ] **Senaryo: Yalnızca kütük** · Verilen komşuda taş, yaban otu, tohum · Eğer tarh dolarsa · O zaman bunlar etkilenmez.
- [ ] **Senaryo: Zincir yok** · Verilen kırılan kütüğün yanında başka tarh · Eğer kırılma olursa · O zaman başka hiçbir şey tetiklenmez (v1'de tek adım kaskad).
- [ ] **Senaryo: Geri al** · Verilen kaskad sonrası geri al · Eğer basılırsa · O zaman kütükler geri gelir ve tarh dolumu geri alınır (tek hamle olarak).
- [ ] **Senaryo: Editör ve çözücü** · Verilen kaskadlı seviye · Eğer BFS çözücü çalışırsa · O zaman kaskad kural olarak uygulanır ve `optimalMoves` buna göre hesaplanır.

### Teknik görevler (Unity/C#)
- [ ] `CascadeRule.Apply(GridBoard, bedId) → List<int> brokenLogIds` (4-komşu); `LevelRunner` `BedFilled` olayında çağırır, `MoveCommitted`'den önce snapshot'a dahil eder.
- [ ] `BoardView`: kütük kırılma efekti (2 parça sprite 80 ms dağılır, Reduce Motion'da fade).
- [ ] `SlideResolver` değil `LevelRunner` düzeyinde: kural hamle çözümlemesinin sonucudur, path'i etkilemez.
- [ ] `LevelSolver` (8. story) ile paylaşılan `BoardRules.ApplyMove(state, move) → state` fonksiyonuna kaskad eklenir; çözücü ve oyun aynı fonksiyonu kullanır.
- [ ] Edit Mode testleri: 4-komşu, çapraz, karışık komşu, geri al.

### Analitik
- `cascade` {level: int, logs_broken: int} — her kaskadda

### Notlar
- Karar: tek adım kaskad; kırılan kütük tarha "bitişik olmayan" hiçbir şeyi etkilemez. Çok adımlı kaskad (kırılan kütüğün ardındaki tohum kayması vb.) SONRA, okunabilirlik için bilinçli olarak dışarıda.
- Kaskad seviye tasarımında "sıra bulmacası" aracıdır: önce şu tarhı doldur ki yol açılsın (9. story zorluk eğrisi, bölge 2'den itibaren).

## STORY: Tohum 6: Çıkmaz tespiti, geri al, ipucu ve yeniden başlat
labels: tema:cekirdek, P1, efor:M
milestone: MVP

**Tohum** epic'inin **6. story'si** (epic #EPIC). Tema: Çekirdek oynanış.

**Öncelik:** P1 · **Efor:** M · **Milestone:** MVP · **Dokunduğu yer:** `DeadlockDetector`, `ReachabilityBfs`, `UndoService`, `HintService`, `DeadlockBanner`, Play HUD alt çubuğu

### Kullanıcı hikâyesi
Bir oyuncu olarak kendimi kilitlediğimde oyunun bunu bana sessizce söylemesini ve bir dokunuşla geri alıp düzeltebilmemi isterim, böylece "kaybetme" diye bir şey olmasın, yalnızca "bir daha dene" olsun.

### Kabul kriterleri
- [ ] **Senaryo: Erişilebilirlik BFS** · Verilen her hamle sonrası tahta · Eğer `ReachabilityBfs` çalışırsa · O zaman her tohum için "bir hamle dizisiyle aynı renk, dolmamış bir tarha ulaşabilir mi" cevabı ≤ 16 ms'de (7×9, 8 tohum) gelir; hesaplama ana iş parçacığında tek karede biter ya da `Task` ile bir sonraki karede sonuçlanır.
- [ ] **Senaryo: Çıkmaz uyarısı** · Verilen hiçbir tohumun ulaşamadığı durum · Eğer tespit edilirse · O zaman üstte sarı şerit "Çıkmaz: hiçbir tohum tarha ulaşamıyor" + "Geri al" (birincil) + "Yeniden başlat" görünür; girdi kilitlenmez, oyuncu istediği gibi devam edebilir.
- [ ] **Senaryo: Kısmi çıkmaz** · Verilen bazı tohumlar ulaşabiliyor ama dolmamış bir tarhın rengi için hiç ulaşan tohum yok · Eğer tespit edilirse · O zaman aynı şerit "Mavi tarha ulaşan tohum kalmadı" metniyle görünür.
- [ ] **Senaryo: Geri al** · Verilen en az 1 hamle · Eğer Geri al'a basılırsa · O zaman son hamle (kütük itme, ot canı, tarh dolumu, kaskad dahil) tam geri döner, hamle sayacı −1; seviye başına 1 ücretsiz, sonrası 50 coin ya da ödüllü video (20. story; MVP'de yalnızca coin).
- [ ] **Senaryo: İpucu** · Verilen çıkmaz olmayan durum · Eğer İpucu (100 coin) alınırsa · O zaman mevcut durumdan tam BFS çözümünün ilk hamlesi 1,5 sn boyunca ok + tohum vurgusu ile gösterilir; çözüm ≤ 50.000 durumda bulunamazsa ipucu "şu an verilemiyor" der ve coin alınmaz.
- [ ] **Senaryo: Yeniden başlat** · Verilen herhangi bir an · Eğer Yeniden başlat'a basılırsa · O zaman onay sorulmadan (çıkmaz şeridinden) ya da tek onayla (HUD'dan) seviye baştan yüklenir, `restarts` +1, coin alınmaz.
- [ ] **Senaryo: Çıkmaz çözülür** · Verilen çıkmaz şeridi açık · Eğer geri al sonrası durum tekrar erişilebilirse · O zaman şerit 200 ms'de kapanır.
- [ ] **Senaryo: Vaat** · Verilen çıkmaz · Eğer oyuncu hiçbir şey yapmazsa · O zaman hiçbir sayaç, ceza, can kaybı yoktur; şerit sonsuza kadar bekler.

### Teknik görevler (Unity/C#)
- [ ] `ReachabilityBfs.CanReach(GridBoard, seedId) → bool`: tek tohum, diğer nesneler sabit kabul (yaklaşık ama ucuz); `DeadlockDetector.Evaluate(board) → DeadlockReport` {isDeadlock, unreachableColors}; `LevelRunner` her `MoveCommitted` sonrası çağırır.
- [ ] `LevelSolver.Solve(BoardState, maxStates = 50_000) → List<Move>?` (8. story ile paylaşılan; durum anahtarı: tohum konumları + kütük konumları + tarh dolulukları + ot canları, `ulong` hash); `HintService` bunu kullanır.
- [ ] `UndoService`: `MoveHistory` yığını, `freeUndoPerLevel = 1`, `undo_cost` (Remote Config), coin düşümü `EconomyService` (14. story) üzerinden.
- [ ] `DeadlockBanner` prefab'ı: sarı şerit, iki düğme, büyük yazı modu metni; Reduce Motion'da kayma yok.
- [ ] HUD alt çubuğu: Geri al (kalan ücretsiz sayısı rozeti), İpucu (fiyat), Yeniden başlat; boosterlar 15. story'de aynı çubuğa eklenir.
- [ ] `HintView`: ok + tohum vurgusu, 1,5 sn; `hint_cost` Remote Config.
- [ ] Edit Mode testleri: çıkmaz var/yok 6 tahta, kısmi çıkmaz, geri al tam dönüş (kaskad dahil), ipucu çözüm doğruluğu.

### Analitik
- `deadlock` {level: int, move_n: int, partial: bool} — şerit göründüğünde
- `undo` {level: int, free: bool, cost: int} — her geri almada
- `hint` {level: int, move_n: int, found: bool} — ipucu alındığında
- `level_restart` {level: int, move_n: int, from_deadlock: bool} — yeniden başlatmada

### Notlar
- Risk (epic): tek-tohum erişilebilirlik "çıkmaz yok" dese de seviye çözümsüz olabilir (iki tohum birbirini kilitler). Kabul: yeniden başlat her zaman bir dokunuş; soft launch'ta `level_restart` ve `deadlock` oranları takılma seviyelerini gösterir.
- Tam durum uzayı BFS'i çalışma zamanında yalnızca ipucu için ve 50.000 durum sınırıyla; 8×11 tahtada 10 tohumla aşılabilir, o durumda ipucu "verilemiyor" der (ölçüm: `hint.found`).
- Geri al ücretsiz 1/seviye kararı: senior kitle için "ceza" hissi yaratmadan coin batağı (sink) olarak çalışır; soft launch verisiyle 2'ye çıkabilir (Remote Config).

## STORY: Tohum 7: His: kayma sesi, çarpma, çiçek açma animasyonu
labels: tema:cekirdek, P1, efor:M
milestone: MVP

**Tohum** epic'inin **7. story'si** (epic #EPIC). Tema: Çekirdek oynanış.

**Öncelik:** P1 · **Efor:** M · **Milestone:** MVP · **Dokunduğu yer:** `SlideFx`, `BloomFx`, `AudioDirector`, `Haptics` (Core), `LevelCompleteOverlay` giriş animasyonu

### Kullanıcı hikâyesi
Bir oyuncu olarak tohumun toprakta kaymasını duymak, tarha "oturduğunu" hissetmek ve seviye bitince tarhların sırayla çiçek açmasını izlemek isterim, böylece sakin ama tatmin edici bir ritim olsun (senior kitle için sakin, çocuk için ödüllü).

### Kabul kriterleri
- [ ] **Senaryo: Kayma sesi** · Verilen kayan tohum · Eğer kayma başlarsa · O zaman hücre sayısıyla orantılı uzunlukta "toprakta kayma" sesi (1–8 hücre için 4 klip, en yakını seçilir) çalar ve tohumun arkasında 3–5 toz parçacığı kalır (Reduce Motion'da parçacık yok).
- [ ] **Senaryo: Çarpma** · Verilen tohumun durması · Eğer engel taş/kenar ise "tok", kütük ise "ahşap", yaban otu ise "hışırtı", tohum ise "tık" · O zaman ilgili klip ve Light haptik; tohum 1,0 → 1,08 → 1,0 ölçek (100 ms).
- [ ] **Senaryo: Tarha giriş** · Verilen tohum tarha girer · Eğer yuva dolarsa · O zaman "toprağa gömülme" sesi, tarh 1,0 → 1,12 → 1,0 (160 ms), yuva noktası parlar, Medium haptik; tarh tamamen dolunca ek "tamam" tınısı (yarım ton yukarı).
- [ ] **Senaryo: Çiçek açma** · Verilen seviye bitişi · Eğer tarhlar sırayla açarsa · O zaman her tarh 180 ms'de tomurcuk → çiçek sprite'ına geçer, 6–10 taç yaprağı parçacığı, kısa arp tınısı; sıra soldan sağa, yukarıdan aşağı; toplam ≤ 1,2 sn.
- [ ] **Senaryo: Kaskad** · Verilen kütük kırılması · Eğer olursa · O zaman "çatırtı" sesi ve 2 parça dağılma (5. story), Light haptik.
- [ ] **Senaryo: Sessiz ve Reduce Motion** · Verilen telefon sessizde ve Reduce Motion açık · Eğer oynanırsa · O zaman tüm durum değişimleri yine görünür (renk, anında geçiş), haptik korunur, hiçbir bilgi yalnızca sese bağlı değildir.
- [ ] **Senaryo: Ambient** · Verilen Play · Eğer açıksa · O zaman düşük sesli bahçe ambiyansı (kuş, rüzgâr) döngüde; Ayarlar'dan Müzik anahtarı kapatır.

### Teknik görevler (Unity/C#)
- [ ] `SlideFx`: toz `ParticleSystem` (havuzlu), çarpma ölçek tween'i, `SlideResult.outcome`'a göre klip seçimi.
- [ ] `BloomFx`: tarh sprite durumları {Empty, Partial, Full, Bloomed}, taç yaprağı `ParticleSystem`, sıra zamanlayıcısı; `LevelRunner.Completing` durumunda oynar.
- [ ] `AudioDirector` (Core `AudioManager` üstüne): klip tablosu SO (kayma ×4, çarpma ×4, gömülme, tamam, çiçek, çatırtı, UI tık), `pitch` küçük rastgelelik (±3 %), ambient döngü.
- [ ] Ses varlıkları: özgün ya da CC0 (freesound), `docs/audio-credits.md`.
- [ ] `Haptics` (Core) çağrıları: Light çarpma, Medium tarh, Heavy 3★ bitiş.
- [ ] `LevelCompleteOverlay` giriş: yıldızlar 120 ms arayla belirir, coin sayacı 400 ms sayar; Reduce Motion'da anında.
- [ ] Efekt yoğunluğu ilkesi: yoğunluk artsın, süre uzamasın (hiçbir efekt oyuncunun bir sonraki hamlesini 400 ms'den fazla bekletmez; çiçek açma yalnızca seviye sonunda).

### Analitik
- Yok (his ayarları için `level_complete.duration_s` ve `move` yeterli).

### Notlar
- Ses kimliği: doğal, "toprak ve bahçe" ailesi; Fluffy Drop'un 80'ler arcade tonundan bilinçli ayrışma; sakin ama sessiz değil.
- Tüm ölçüler Play modunda ayarlanır.
- Tasarım taslağı: Tohum · Oyun artboard'unda üç tarhtan biri açmış hâlde — Bloomed sprite referansı.

## STORY: Tohum 8: Seviye veri formatı ve seviye editörü
labels: tema:cekirdek, P1, efor:L
milestone: MVP

**Tohum** epic'inin **8. story'si** (epic #EPIC). Tema: Seviye içeriği.

**Öncelik:** P1 · **Efor:** L · **Milestone:** MVP · **Dokunduğu yer:** `LevelData` JSON, `LevelCatalog`, `LevelEditorWindow` (Unity EditorWindow), `LevelSolver`, `Assets/_Game/Levels/`

### Kullanıcı hikâyesi
Bir geliştirici (ve tek seviye tasarımcısı) olarak bir seviyeyi hücre boyayarak 2 dakikada kurmak, aynı pencereden test oynamak ve "çözülebilir mi, kaç hamlede" sorusunun cevabını anında görmek isterim, böylece 200 seviye 4 haftaya sığsın ve hiçbir seviye çözümsüz çıkmasın.

### Kabul kriterleri
- [ ] **Senaryo: Veri formatı** · Verilen `Levels/level_042.json` · Eğer okunursa · O zaman {id, width, height, grid: string[] (satır başına karakter: `.` boş, `#` taş, `L` kütük, `^>v<` kanal, `w` ot), seeds: [{color, x, y}], beds: [{color, x, y, capacity}], optimalMoves, solution: [{seed, dir}], introduces: string[], region, bigText: bool?} alanları vardır ve `LevelData` sınıfına deterministik olarak yüklenir.
- [ ] **Senaryo: Katalog** · Verilen 200 dosya · Eğer `LevelCatalog` yüklenirse · O zaman `level_001..200` sırayla, eksik dosya build'i kırar (Edit Mode testi), `Resources` yerine Addressables ya da `StreamingAssets` ile ≤ 400 KB toplam.
- [ ] **Senaryo: Hücre boyama** · Verilen `LevelEditorWindow` · Eğer bir araç (boş, taş, kütük, kanal ×4, ot, tohum ×6 renk, tarh ×6 renk) seçilip hücreye tıklanırsa · O zaman hücre boyanır; sağ tık siler; tarh için kapasite alanı; sürükleyerek çoklu boyama.
- [ ] **Senaryo: Doğrulama** · Verilen düzenlenen tahta · Eğer "Doğrula" basılırsa · O zaman `LevelSolver` BFS çalışır ve ≤ 2 sn'de "Çözülebilir · 9 hamle" ya da "Çözümsüz" ya da "Durum sınırı aşıldı (200.000)" döner; çözüm ve `optimalMoves` dosyaya yazılır; tohum sayısı tarh kapasite toplamından az ise kırmızı uyarı, 2'den fazla fazlaysa sarı uyarı.
- [ ] **Senaryo: Test oynat** · Verilen editör · Eğer "Oynat" basılırsa · O zaman Play sahnesi bu seviyeyle Play Mode'da açılır; çıkınca editör aynı seviyede kalır.
- [ ] **Senaryo: Çözümü izle** · Verilen çözülmüş seviye · Eğer "Çözümü oynat" basılırsa · O zaman editör tahtasında hamleler 500 ms arayla gösterilir.
- [ ] **Senaryo: Toplu doğrulama** · Verilen `Levels/` klasörü · Eğer menüden "Tümünü doğrula" seçilirse · O zaman 200 seviye için rapor (`docs/levels-report.md`: id, boyut, tohum, tarh, optimal, durum sayısı, engel tipleri) üretilir; çözümsüz ya da sınır aşan seviye listelenir ve CI'da test olarak koşar.
- [ ] **Senaryo: Oyun içi tutarlılık** · Verilen bir seviye · Eğer çözüm dizisi `BoardRules.ApplyMove` ile oynatılırsa · O zaman tüm tarhlar dolar (çözücü ve oyun aynı kural fonksiyonunu kullanır; Edit Mode testi 200 seviyede koşar).

### Teknik görevler (Unity/C#)
- [ ] `LevelData` (serileştirilebilir sınıf) + `LevelData.FromJson/ToJson` (Newtonsoft ya da `JsonUtility` + özel grid parse); şema sürümü alanı `v: 1`.
- [ ] `LevelCatalog`: Addressables grubu `levels`, `LoadAsync(int id)`, önbellek; eksik/bozuk dosya testi.
- [ ] `BoardState` (immutable struct: tohumlar, kütükler, tarh dolulukları, ot canları; `ulong` hash) ve `BoardRules.ApplyMove(state, move) → state` (2.–5. story kurallarını `SlideResolver` + `CascadeRule` üzerinden çağırır); `LevelSolver.Solve(BoardState, maxStates)` BFS, en kısa yol, `HashSet<ulong>` ziyaret.
- [ ] `LevelEditorWindow` (`EditorWindow`, IMGUI ya da UI Toolkit): araç paleti, ızgara çizimi (`Handles`/`GUI.DrawTexture`), boyut değişimi (6×8 … 8×11), kapasite alanı, `introduces` ve `region` alanları, Kaydet/Yükle/Yeni, Doğrula, Oynat, Çözümü oynat, önceki/sonraki seviye.
- [ ] `EditorPlayBridge`: `EditorPrefs` ile seçili seviye id'si, Play sahnesine `LevelRunner` başlangıç parametresi.
- [ ] `LevelBatchValidator` (menü + Edit Mode testi): tüm seviyeler, rapor markdown.
- [ ] `docs/levels.md`: format, araçlar, zorluk kuralları (9. story doldurur).

### Analitik
- Yok (editör aracı). Oyun içi `level_start.board` ve `level_complete.optimal` bu verinin tüketicisidir.

### Notlar
- Karar: BFS en kısa çözüm; durum sınırı editörde 200.000, oyunda (ipucu) 50.000. 8×11 ve 10 tohumda sınır aşılırsa seviye basitleştirilir (içerik kuralı: her seviye editörde çözülebilir olmalı; aksi hâlde yayımlanmaz).
- Risk: bu story 4 haftanın en kritik yatırımı; editör bitmeden seviye üretimi başlamaz. Hafta 2 sonunda bitmesi şart (epic riskleri).
- Prosedürel yardımcı (11. story) bu pencereye düğme olarak eklenir.

## STORY: Tohum 9: 200 seviye içerik planı ve zorluk eğrisi
labels: tema:cekirdek, P1, efor:L
milestone: MVP

**Tohum** epic'inin **9. story'si** (epic #EPIC). Tema: Seviye içeriği.

**Öncelik:** P1 · **Efor:** L · **Milestone:** MVP · **Dokunduğu yer:** `Assets/_Game/Levels/level_001..200.json`, `docs/levels.md`, `docs/levels-report.md`, `LevelCatalog`

### Kullanıcı hikâyesi
Bir oyuncu olarak her 5–10 seviyede yeni bir şey öğrenmek ama hiçbir seviyede duvara toslamamak isterim; geliştirici olarak 200 seviyenin zorluğunu ölçülebilir bir eğriyle planlamak isterim, böylece soft launch'ta takılma seviyeleri veriyle bulunup düzeltilebilsin.

### Kabul kriterleri
- [ ] **Senaryo: Bölgeler** · Verilen harita · Eğer seviyeler sınıflanırsa · O zaman 4 bölge × 50 seviye: Çayır (1–50: 6×8 → 7×9, 2–3 renk, taş; kütük 12'den, kaskad 25'ten), Bağ (51–100: 7×9, 3–4 renk, kanal 55'ten), Orman (101–150: 7×9 → 8×11, 4–5 renk, yaban otu 105'ten), Sera (151–200: 8×11, 5–6 renk, tüm engeller).
- [ ] **Senaryo: Zorluk metriği** · Verilen her seviye · Eğer `docs/levels-report.md` üretilirse · O zaman zorluk puanı `D = optimalMoves + 0,5 × (seeds − bedCapacitySum) + 0,3 × logs + 0,4 × channels + 0,6 × weeds + colors` hesaplanır ve bölge içinde testere dişi eğri izler (3 artan, 1 "nefes" seviyesi; her 10. seviye bölge ortalamasının altında).
- [ ] **Senaryo: Optimal aralığı** · Verilen bölge · Eğer seviyeler incelenirse · O zaman optimal hamle Çayır 3–8, Bağ 6–12, Orman 8–16, Sera 10–22; hiçbir seviye 25'i geçmez.
- [ ] **Senaryo: Fazla tohum** · Verilen her seviye · Eğer sayılırsa · O zaman tohum sayısı = tarh kapasite toplamı + {0, 1, 2}; fazla tohumlu seviye oranı bölge başına ≤ %30.
- [ ] **Senaryo: Yeni öğe tanıtımı** · Verilen bir engelin ilk seviyesi · Eğer açılırsa · O zaman o seviye yalnızca o engeli ve 2 rengi içerir, optimal ≤ 5 ve `introduces` alanı doludur (4. story kartı).
- [ ] **Senaryo: Soft launch paketi** · Verilen sürüm 1.0 · Eğer build alınırsa · O zaman 1–120 seviye dahil, 121–200 için harita "Yakında" düğümleri; 1.1 (hafta 1) 121–160, 1.2 (hafta 2) 161–200.
- [ ] **Senaryo: Tam doğrulama** · Verilen 200 seviye · Eğer `LevelBatchValidator` koşarsa · O zaman 0 çözümsüz, 0 sınır aşımı, her seviyede `solution` dolu.

### Teknik görevler (Unity/C#)
- [ ] `docs/levels.md`: bölge tablosu, engel takvimi, zorluk formülü, testere dişi kuralı, tasarım ilkeleri (her seviyede tek "aha", 3★ çözümü sezgisel olmayan ama görünür, sahte yol en fazla 1).
- [ ] 1–80 elle (editör, ~15 dk/seviye → ~20 saat), 81–200 prosedürel yardımcı (11. story) + elle düzeltme (~5 dk/seviye → ~10 saat).
- [ ] `LevelBatchValidator` raporuna zorluk puanı ve bölge ortalaması; eğri grafiği için CSV (`docs/levels.csv`).
- [ ] `LevelCatalog.AvailableCount` Remote Config `max_level_available` (120 → 160 → 200) ile sürüm bağımsız açılır; harita "Yakında" düğümleri (12. story).
- [ ] Her 10. seviye "nefes" işareti: `LevelData.tags: ["breather"]`; her 5. seviye Bahçem tarhı açar (13. story bu listeyi okur).
- [ ] Oyun testi: 10 kişi (3 senior, 3 çocuk, 4 yetişkin) ilk 30 seviyeyi oynar; takılma (> 3 yeniden başlat) seviyeleri yeniden dengelenir; sonuçlar `docs/playtest-1.md`.

### Analitik
- `level_complete` ve `level_restart` zaten var; kapı raporunda seviye başına huni: start → complete oranı, ortalama restart, ortalama süre (18. story `level_funnel.sql`).

### Notlar
- Zorluk formülü başlangıç; playtest ve soft launch verisiyle katsayılar ayarlanır (`docs/levels.md`'de sürümlenir).
- Karar: kolay bir oyun olmak hedef; Sera bölgesinde bile hedef tamamlama oranı ≥ %85/seviye. Zorluk "kaybetme"den değil "3★ bulma"dan gelir.
- Risk (epic): efor; 1.0'da 120 seviye kararı buradan.

## STORY: Tohum 10: Tutorial seviyeleri 1–8
labels: tema:cekirdek, P1, efor:M
milestone: MVP

**Tohum** epic'inin **10. story'si** (epic #EPIC). Tema: Seviye içeriği.

**Öncelik:** P1 · **Efor:** M · **Milestone:** MVP · **Dokunduğu yer:** `level_001..008.json`, `TutorialDirector`, `HintBubble` (Core), `TutorialFlags`

### Kullanıcı hikâyesi
Bir yeni oyuncu (70 yaşında ya da 8 yaşında) olarak kuralları okumadan, oynarken ve kilitlenmeden öğrenmek isterim, böylece 8. seviyede kendimi "biliyorum" hissedeyim ve 20. seviyeye ulaşma oranı kapıyı geçsin.

### Kabul kriterleri
- [ ] **Senaryo: Seviye 1** · Verilen 6×8 tahta, 1 tohum, 1 tarh (kapasite 1), tek yön · Eğer açılırsa · O zaman tohumdan tarha noktalı iz ve sürükleyen el animasyonu görünür; el, oyuncu doğru yönde sürüklemeye başlayınca kaybolur; başka yöne sürüklerse tohum kayar, el tekrar gelir (girdi kilitlenmez).
- [ ] **Senaryo: Seviye 2–3** · Verilen 2 tohum, kapasite 2 tarh (2), 2 tohum 2 renk (3) · Eğer oynanırsa · O zaman "aynı renk tarha girer" balonu ilk yanlış renk çarpmasında bir kez çıkar.
- [ ] **Senaryo: Seviye 4–5** · Verilen taş (4) ve iki yönlü kayma gerektiren düzen (5) · Eğer oynanırsa · O zaman "taş durdurur" kartı (4. story) ve 5'te ipucu yok, optimal 3.
- [ ] **Senaryo: Seviye 6** · Verilen bilerek çıkmaza girilebilen düzen · Eğer oyuncu çıkmaza girerse · O zaman çıkmaz şeridi ilk kez görünür ve "Geri al ücretsiz" balonu eşlik eder; girmezse seviye normal biter.
- [ ] **Senaryo: Seviye 7–8** · Verilen fazla tohumlu seviye (7: +1 tohum) ve 3 renk, 2 kapasite 3 tarh (8) · Eğer bitirilirse · O zaman 8. seviye overlay'inde "Bahçeni kurmaya hazırsın" kartı ve Bahçem'in ilk tarhı (5. seviyede açılmıştı) hatırlatılır.
- [ ] **Senaryo: Hiçbir kilit yok** · Verilen tutorial seviyeleri · Eğer oyuncu yönergeyi yok sayarsa · O zaman her şey çalışır; balonlar 4 sn sonra ya da dokununca kapanır.
- [ ] **Senaryo: Tekrar yok** · Verilen ipuçları görüldü · Eğer seviye tekrar oynanırsa · O zaman balon çıkmaz; Ayarlar'da "İpuçlarını sıfırla".

### Teknik görevler (Unity/C#)
- [ ] `level_001..008.json` elle (editörde) + `tutorial` alanı: [{trigger: LevelStart | WrongColorHit | Deadlock | FirstBedFull, textKey, anchor: seedId | bedId | hud}].
- [ ] `TutorialDirector`: `LevelRunner` olaylarını dinler, `TutorialFlags` (Cloud Save) ile bir kez gösterir; el animasyonu prefab'ı (Core `HintBubble` + `PointerHand`).
- [ ] Sürükleme yönü ipucu: seviye 1'de `SlidePreview` otomatik açık (parmak basmadan).
- [ ] TR/EN metinler, büyük yazı modu uyumu (balon ≤ 2 satır, 22 pt).
- [ ] Playtest 1 (9. story) ilk 8 seviyeye odaklanır: 3 senior, 3 çocuk gözlemi.

### Analitik
- `ftue_step` {level: int, trigger: string} — her balon gösteriminde
- `ftue_complete` {duration_s: int, restarts: int} — 8. seviye bitince

### Notlar
- İlke: zorunlu, girdiyi kilitleyen eğitim yok (MustyBlockBlast ve Daily Drop ile aynı).
- Kapı metriği bağı: seviye 20'ye ulaşma ≥ %45; tutorial huni `ftue_step` → `ftue_complete` → `level_complete(20)`.

## STORY: Tohum 11: Prosedürel seviye yardımcısı: çözümden geriye karıştırma
labels: tema:cekirdek, P1, efor:M
milestone: MVP

**Tohum** epic'inin **11. story'si** (epic #EPIC). Tema: Seviye içeriği.

**Öncelik:** P1 · **Efor:** M · **Milestone:** MVP · **Dokunduğu yer:** `LevelGenerator`, `LevelEditorWindow` "Üret" paneli, `LevelSolver`

### Kullanıcı hikâyesi
Bir geliştirici olarak editöre "7×9, 4 renk, 2 kütük, 1 kanal, hedef optimal 10" deyip 10 saniyede çözülebilir bir aday seviye almak ve onu elle rötuşlamak isterim, böylece 81–200 arası seviyeler 4 haftaya sığsın.

### Kabul kriterleri
- [ ] **Senaryo: Parametreler** · Verilen Üret paneli · Eğer {width, height, colors, beds (sayı ve kapasite aralığı), stones, logs, channels, weeds, extraSeeds (0–2), targetOptimal, seed} girilirse · O zaman en fazla 10 sn'de bir aday üretilir ya da "üretilemedi, parametreleri gevşet" döner.
- [ ] **Senaryo: Geriye karıştırma** · Verilen çözülmüş son durum (tüm tarhlar dolu) · Eğer üretici çalışırsa · O zaman tohumlar tarhlardan "geri çıkarılır" ve ters hamlelerle (bir hücreden geldiği yöne, bir engele dayanana kadar) `targetOptimal` adım geri kaydırılır; her ters hamle ileri yönde geçerli olmalıdır (ileri `SlideResolver` ile doğrulanır).
- [ ] **Senaryo: Doğrulama** · Verilen aday · Eğer `LevelSolver` çalışırsa · O zaman çözülebilir ve `optimalMoves` `targetOptimal ± 2` aralığında; değilse 20 denemeye kadar yeniden üretilir.
- [ ] **Senaryo: Çeşitlilik** · Verilen aynı parametreler, farklı seed · Eğer 10 aday üretilirse · O zaman en az 8'i farklı tahtadır (grid + tohum konumu hash).
- [ ] **Senaryo: Sıkıcı aday filtresi** · Verilen aday · Eğer bir tohum tek hamlede tarha giriyorsa ve bu tohum oranı > %50 ise · O zaman aday reddedilir (yeniden üret).
- [ ] **Senaryo: Editöre aktarım** · Verilen kabul edilen aday · Eğer "Editöre al" basılırsa · O zaman tahta editörde açılır, `solution` ve `optimalMoves` dolu, geliştirici elle düzenleyip "Doğrula" ile yeniden kontrol eder.

### Teknik görevler (Unity/C#)
- [ ] `LevelGenerator.Generate(GenParams, int seed) → LevelData?`: engel yerleşimi (rastgele, tarh komşuluğu kısıtları), son durum kurulumu, ters hamle üretimi (`ReverseSlide`: hedef hücreden geriye doğru bir engele dayanana kadar; ileri doğrulama), fazla tohum ekleme.
- [ ] `LevelSolver` ile doğrulama ve `targetOptimal ± 2` filtresi; `TrivialRatio` filtresi.
- [ ] Editör paneli: parametre alanları, Üret, 10 aday küçük önizleme, Editöre al; `EditorPrefs` ile son parametreler.
- [ ] Kaskad ve kanal için ters hamle özel durumları: kaskadla kırılan kütük geri yerleştirilir; kanal ters yönde izlenir; karmaşıksa aday reddedilir (basit yol: kanal ve kaskadlı seviyeler elle).
- [ ] Edit Mode testleri: 50 üretim deterministik (aynı seed aynı seviye), hepsi çözülebilir.

### Analitik
- Yok (editör aracı).

### Notlar
- Karar: üretici seviye tasarımcısının yerine geçmez; her aday elle rötuşlanır ve 9. story eğrisine oturtulur.
- Açık soru: kanal ve kaskad için ters hamle karmaşıklığı; v1'de bu iki öğe elle eklenir, üretici engel olarak yalnızca taş/kütük/ot kullanır.

## STORY: Tohum 12: Harita ekranı ve seviye ilerlemesi
labels: tema:meta, P1, efor:M
milestone: MVP

**Tohum** epic'inin **12. story'si** (epic #EPIC). Tema: Meta ve tutma.

**Öncelik:** P1 · **Efor:** M · **Milestone:** MVP · **Dokunduğu yer:** `MapScreen`, `MapScaffold` (Core), `LevelNode`, `ProgressService`, dürüstlük şeridi yuvası

### Kullanıcı hikâyesi
Bir oyuncu olarak uygulamayı açınca kaldığım seviyeyi, kaç yıldızım olduğunu ve bahçemin kaç tarhının açıldığını tek bakışta görmek ve tek dokunuşla oynamaya başlamak isterim, böylece harita hem ana menü hem ilerleme hissi olsun.

### Kabul kriterleri
- [ ] **Senaryo: Harita düzeni** · Verilen Map · Eğer açılırsa · O zaman dikey kaydırılan bahçe yolu, 4 bölge arka planı (Çayır, Bağ, Orman, Sera), her seviye bir düğüm (numara + 0–3 yıldız), sıradaki seviye büyük ve "Oyna" etiketli, kilitli düğümler soluk; kaydırma sıradaki seviyeye odaklı açılır.
- [ ] **Senaryo: Üst çubuk** · Verilen Map · Eğer açılırsa · O zaman üstte dürüstlük şeridi (16. story doldurur; burada yuva), coin sayacı (+ düğmesi Mağaza'ya), Ayarlar; altta "Bahçem 12/40" düğmesi ve Mağaza.
- [ ] **Senaryo: Seviye seçimi** · Verilen tamamlanmış seviye düğümü · Eğer dokunulursa · O zaman "Tekrar oyna · 2★ · en iyi 11 hamle" kartı ve Oyna; kilitli düğüme dokununca hafif sallanma, metin yok.
- [ ] **Senaryo: Bölge geçişi** · Verilen 50. seviye bitti · Eğer Map'e dönülürse · O zaman "Bağ bölgesi açıldı" kartı (bir kez), harita yeni bölgeye kayar.
- [ ] **Senaryo: Yakında** · Verilen `max_level_available = 120` · Eğer 121. düğüm görünürse · O zaman "Yakında" etiketi, dokununca "Yeni seviyeler bu hafta" metni; sürüm güncellemesi ya da Remote Config değişimiyle açılır.
- [ ] **Senaryo: Devam eden seviye** · Verilen yarım bırakılmış seviye (ev düğmesiyle çıkılmış) · Eğer Map açılırsa · O zaman sıradaki düğüm "Devam et · 6. hamle" gösterir ve Play kaldığı tahtayla açılır.
- [ ] **Senaryo: Performans** · Verilen 200 düğüm · Eğer kaydırılırsa · O zaman 60 fps (orta sınıf Android), düğümler havuzlu/sanal liste.

### Teknik görevler (Unity/C#)
- [ ] `MapScreen` (uGUI `ScrollRect`, dikey), `LevelNode` prefab'ı (havuzlu), bölge arka planları (4 sprite, parallax yok), yol çizgisi.
- [ ] Core `MapScaffold` bağlama: düğüm listesi, kilit kuralı (`maxLevel + 1`), odaklama; oyuna özgü görsel.
- [ ] `ProgressService` (3. story) okuma; `InProgressLevel` snapshot (2. story `MoveHistory` son durumu; `OnApplicationPause` ve ev düğmesinde kaydedilir).
- [ ] Bölge geçiş kartı ve `RegionFlags` (Cloud Save).
- [ ] "Yakında" düğümü: `LevelCatalog.AvailableCount` ve Remote Config `max_level_available`.
- [ ] Üst çubuk prefab'ı: `HonestyStrip` yuvası (16. story), `CoinCounter` (14. story), Ayarlar düğmesi; alt çubuk: Bahçem (13), Mağaza (14).

### Analitik
- `map_view` {max_level: int, stars_total: int, garden_beds: int} — Map her açıldığında
- `level_select` {level: int, replay: bool} — düğüme dokununca

### Notlar
- Harita = ana menü kararı: ayrı "Home" ekranı yok; ev düğmesi her yerden buraya getirir (Fluffy Drop şikâyeti).
- Interstitial yalnızca bu ekranda ve yalnızca Play'den dönüşte (16. story); harita açılışında reklam yok.

## STORY: Tohum 13: Bahçem meta sahnesi ve dekor
labels: tema:meta, P1, efor:L
milestone: MVP

**Tohum** epic'inin **13. story'si** (epic #EPIC). Tema: Meta ve tutma.

**Öncelik:** P1 · **Efor:** L · **Milestone:** MVP · **Dokunduğu yer:** `Garden` sahnesi, `GardenState`, `GardenBed`, `DecorItem`, `DecorShop`, `EconomyService`

### Kullanıcı hikâyesi
Bir oyuncu olarak her 5 seviyede kendi bahçemde yeni bir tarhın açıldığını, coin'lerimle çit, bank, çeşme koyup bahçemi büyüttüğümü görmek isterim, böylece seviyeler bir listeden ibaret olmasın ve coin'in gerçek bir anlamı olsun.

### Kabul kriterleri
- [ ] **Senaryo: Tarh açılışı** · Verilen 5'in katı seviye tamamlandı · Eğer `LevelComplete` overlay'inde Devam'a basılırsa · O zaman Garden sahnesi açılır, yeni tarh 1,5 sn'lik açma animasyonuyla çiçeklenir (bölgeye göre çiçek türü), +100 coin, "Bahçem 13/40" güncellenir; sonra Map'e dönülür (reklam yok).
- [ ] **Senaryo: Bahçe düzeni** · Verilen Garden · Eğer açılırsa · O zaman 8×5 tarh ızgarası (40), açılanlar çiçekli, kapalılar toprak + "Seviye 65" etiketi; yakınlaştırma yok, tek ekran; büyük yazı modunda etiketler 20 pt.
- [ ] **Senaryo: Dekor mağazası** · Verilen Garden'daki "Dekor" düğmesi · Eğer açılırsa · O zaman 6 tip (çit 200, bank 400, kuş yuvası 600, fener 800, çeşme 1.200, sera 2.000 coin), her biri önizleme ve "Satın al"; yetersiz coin'de düğme soluk ve "+ coin" Mağaza'ya götürür.
- [ ] **Senaryo: Yerleştirme** · Verilen satın alınan dekor · Eğer yerleştirilirse · O zaman bahçe kenarındaki 12 dekor yuvasından (ızgara, sürükle-bırak yok: yuvaya dokun) birine konur, taşınabilir, satılmaz (iade yok); aynı tipten en fazla yuva sayısı kadar.
- [ ] **Senaryo: Kalıcılık** · Verilen dekor ve tarhlar · Eğer uygulama silinip Cloud Save ile geri gelirse · O zaman bahçe birebir geri gelir.
- [ ] **Senaryo: Boş bahçe** · Verilen 5. seviye bitmemiş · Eğer Bahçem açılırsa · O zaman "İlk tarhın 5. seviyede açılır" kartı ve kapalı ızgara; dekor mağazası açık ama yerleştirme yuvası 2 (teaser).
- [ ] **Senaryo: Ambient** · Verilen açılan tarh sayısı · Eğer ≥ 10 ise kuş sesi, ≥ 20 ise kelebek parçacığı, ≥ 40 ise "tam bahçe" tacı · O zaman bahçe büyüdükçe canlanır (Reduce Motion'da parçacık yok).

### Teknik görevler (Unity/C#)
- [ ] `GardenState` {openedBeds: int, decor: List<{type, slot}>}; Cloud Save anahtarı `garden`; `ProgressService` ile tutarlılık (`openedBeds = maxLevel / 5`, en yüksek kazanır).
- [ ] `Garden` sahnesi: arka plan, 40 `GardenBed` prefab'ı (toprak/çiçek durumu, bölgeye göre 4 çiçek seti), 12 `DecorSlot`, Dekor düğmesi, ev düğmesi.
- [ ] `DecorItem` SO (type, price, sprite, nameKey), `DecorShop` paneli, `EconomyService.TrySpend(amount, reason)` (14. story) çağrısı.
- [ ] Tarh açılış akışı: `LevelCompleteOverlay.Continue` → `GardenUnlockFlow` (yalnızca `level % 5 == 0` ve yeni) → Garden → Map.
- [ ] Ambient kademeleri ve kelebek `ParticleSystem`.
- [ ] `decor_prices` Remote Config.

### Analitik
- `garden_view` {beds: int, decor_count: int} — Garden her açıldığında
- `garden_bed_unlock` {bed_n: int, level: int} — tarh açılışında
- `decor_purchase` {type: string, price: int, coins_after: int} — satın almada

### Notlar
- Karar: dekor yerleştirme sürükle-bırak değil yuvaya dokun (senior ve çocuk için; 2 dokunuş).
- Kapı ikincil metriği: Bahçem'i açan oyuncu ≥ %60 (`garden_view` / D1 kullanıcı).
- Efor L: sanat (4 çiçek seti × 2 durum + 6 dekor) bu story'nin yarısı; placeholder ile başlanır, soft launch öncesi sanat tamamlanır.

## STORY: Tohum 14: Coin ekonomisi: kazanç, günlük hediye, mağaza ve coin paketleri
labels: tema:monetizasyon, P1, efor:M
milestone: MVP

**Tohum** epic'inin **14. story'si** (epic #EPIC). Tema: Ekonomi ve monetizasyon.

**Öncelik:** P1 · **Efor:** M · **Milestone:** MVP · **Dokunduğu yer:** `EconomyService`, `EconomyConfig`, `CoinCounter`, `ShopScreen` (Core iskeleti), `IapService` (Core), `DailyGift` (Core)

### Kullanıcı hikâyesi
Bir oyuncu olarak kazandığım coin'in ne işe yaradığını (booster, ipucu, dekor) ve nereden geldiğini (seviye, günlük hediye) her zaman bilmek isterim; geliştirici olarak da coin'in Fluffy Drop'taki gibi işlevsiz bir sayaç değil, dengeli bir ekonomi olmasını isterim.

### Kabul kriterleri
- [ ] **Senaryo: Seviye kazancı** · Verilen tamamlanan seviye · Eğer overlay açılırsa · O zaman 1★ 30, 2★ 45, 3★ 60 coin eklenir (tekrar oynanan seviyede 10; yıldız yükselirse fark kadar ek); sayaç Map üst çubuğunda 400 ms'de sayar.
- [ ] **Senaryo: Günlük hediye** · Verilen yeni gün (yerel tarih) · Eğer Map açılırsa · O zaman Core `DailyGift` kartı: 7 günlük döngü {50, 60, 70, 80, 100, 120, 150}; kaçırılan gün döngüyü başa almaz (bir sonraki kademe), dokununca alınır; reklam yok.
- [ ] **Senaryo: Harcama** · Verilen 120 coin · Eğer Kürek (150) denenirse · O zaman düğme soluk, "30 coin eksik · Mağaza" ipucu; `TrySpend` false döner ve hiçbir yan etki olmaz.
- [ ] **Senaryo: Mağaza** · Verilen coin sayacındaki + ya da alt çubuk Mağaza · Eğer açılırsa · O zaman üstte No Ads 4,99 USD (16. story), coin paketleri {500: 0,99 USD, 1.800: 2,99 USD, 3.500: 4,99 USD, 8.000: 9,99 USD}, "Satın alımları geri yükle"; fiyatlar yerel para biriminde (`localizedPrice`).
- [ ] **Senaryo: Satın alma** · Verilen Google Play Billing · Eğer 1.800 coin paketi alınırsa · O zaman coin anında eklenir, makbuz Cloud Code ile doğrulanır (başarısızsa coin geri alınır ve uyarı), `coin_purchase` olayı gider.
- [ ] **Senaryo: Çevrimdışı** · Verilen bağlantı yok · Eğer seviye bitirilirse · O zaman coin yerel eklenir, Cloud Save bağlantı gelince senkronlanır; mağaza "bağlantı yok" durumu gösterir.
- [ ] **Senaryo: Denge** · Verilen 9. story eğrisiyle 50 seviye · Eğer oyuncu hiç harcamazsa · O zaman biriken coin ≈ 2.400 + hediye (bir çeşme + bir bank + 2 çit) — `docs/economy.md` tablosuyla ±%10 uyumlu.

### Teknik görevler (Unity/C#)
- [ ] `EconomyService`: `Balance`, `Earn(amount, source)`, `TrySpend(amount, reason) → bool`, olaylar; Cloud Save anahtarı `economy` (balance, lifetimeEarned, lifetimeSpent); çakışmada en yüksek `balance`.
- [ ] `EconomyConfig` (Remote Config) okuma ve varsayılanlar (1. story şeması); `docs/economy.md`: kaynak/batak tablosu, 50/100/200 seviye simülasyonu (basit script).
- [ ] `CoinCounter` UI (sayaç tween, + düğmesi), `LevelCompleteOverlay` coin satırı bağlantısı.
- [ ] Core `DailyGift` bağlama: tablo `daily_gift_table`, yerel tarih, Cloud Save `daily_gift` {lastClaimDay, streakIndex}.
- [ ] Core `ShopScreen` iskeleti + oyuna özgü ürünler: Unity IAP tüketilebilir `coins_500`, `coins_1800`, `coins_3500`, `coins_8000`; Play Console ürün tanımları; Cloud Code `verifyPurchase`.
- [ ] Edit Mode testleri: `TrySpend` sınırları, günlük hediye döngüsü, yıldız yükseltme farkı.

### Analitik
- `coin_earn` {source: string ("level" | "gift" | "garden" | "iap" | "rewarded"), amount: int, balance: int} — her kazançta
- `coin_spend` {reason: string ("hint" | "undo" | "shovel" | "rain" | "bee" | "decor"), amount: int, balance: int} — her harcamada
- `shop_view` {source: string} · `coin_purchase` {sku: string, price_local: string, coins: int}

### Notlar
- Karar: fiyat noktaları Play'de dört ürün, Remote Config yalnızca hangi SKU'ların gösterileceğini seçer (`coin_pack_skus`); fiyat Play'den gelir.
- Coin'in harcanacak gerçek bir yeri var (epic ilkesi): booster ve ipucu kısa vadeli, dekor uzun vadeli batak.
- No Ads bu mağazada en üstte; coin paketiyle birlikte "Bahçıvan paketi" (No Ads + 1.000 coin, 6,99 USD) soft launch'ta A/B (16. story notu).

## STORY: Tohum 15: Boosterlar: Kürek, Yağmur, Arı
labels: tema:monetizasyon, P1, efor:M
milestone: MVP

**Tohum** epic'inin **15. story'si** (epic #EPIC). Tema: Ekonomi ve monetizasyon.

**Öncelik:** P1 · **Efor:** M · **Milestone:** MVP · **Dokunduğu yer:** `BoosterService`, `ShovelBooster`, `RainBooster`, `BeeBooster`, Play HUD alt çubuğu, `AdsService` (Core, ödüllü `bee`)

### Kullanıcı hikâyesi
Bir oyuncu olarak takıldığımda coin'imle bir taşı kaldırmak, tahtayı bir adım kaydırmak ya da bir tohumu tarha ışınlamak isterim, böylece ilerlemem hiçbir zaman reklama ya da şansa bağlı olmasın; fiyatı düğmede yazsın.

### Kabul kriterleri
- [ ] **Senaryo: Düğmeler** · Verilen Play HUD alt çubuğu · Eğer seviye açılırsa · O zaman Kürek (150), Yağmur (300), Arı (500 ya da "reklam izle") düğmeleri fiyatlarıyla görünür; seviye 1–8'de gizli, 9'dan itibaren görünür; yetersiz coin'de soluk.
- [ ] **Senaryo: Kürek** · Verilen Kürek seçildi · Eğer bir taş ya da yaban otu hücresine dokunulursa · O zaman 150 coin düşer, hücre boşalır (kazma animasyonu 300 ms), hamle sayılmaz, geri alınamaz (uyarı metni düğmede "geri alınamaz"); taş/ot olmayan hücreye dokunursa iptal, coin düşmez.
- [ ] **Senaryo: Yağmur** · Verilen Yağmur seçildi · Eğer bir yön (4 ok) seçilirse · O zaman 300 coin düşer, tüm tohum ve kütükler o yöne 1 hücre kayar (engele dayananlar kalır; tarha girenler girer; kaskad çalışır), hamle sayılmaz, geri alınabilir (ücretli geri al kuralı).
- [ ] **Senaryo: Arı** · Verilen Arı seçildi · Eğer bir tohuma dokunulursa · O zaman aynı renk, dolmamış bir tarh varsa tohum arı animasyonuyla (600 ms) tarha taşınır, yuva dolar, kaskad çalışır, hamle sayılmaz; dolmamış tarh yoksa Arı düğmesi zaten soluktur.
- [ ] **Senaryo: Arı ile reklam** · Verilen coin < 500 ve ödüllü reklam yüklü · Eğer Arı'ya basılırsa · O zaman "Reklam izle" seçeneği; reklam sonuna kadar izlenirse Arı uygulanır, yarıda kapatılırsa hiçbir şey olmaz; reklam yüklü değilse yalnızca coin seçeneği (boş teklif yok).
- [ ] **Senaryo: Yıldız etkisi** · Verilen booster kullanıldı · Eğer seviye bitirilirse · O zaman yıldız hesabı hamle sayısıyla yapılır (booster hamle sayılmaz), `boostersUsed` kaydedilir; 3★ booster ile de alınabilir (ceza yok).
- [ ] **Senaryo: İptal** · Verilen booster seçildi · Eğer HUD'daki "Vazgeç"e basılırsa ya da geçersiz hücreye dokunulursa · O zaman seçim kalkar, coin düşmez.

### Teknik görevler (Unity/C#)
- [ ] `BoosterService`: durum {None, Selecting(type), Applying}, `IBooster` arayüzü {Cost, CanApply(board), Apply(board, target)}; `ShovelBooster`, `RainBooster`, `BeeBooster`.
- [ ] `RainBooster`: `BoardRules.ShiftAll(state, dir)` (tek adım, tohum ve kütük; `SlideResolver`'dan farklı: 1 hücre); kaskad ve tarh girişi `LevelRunner` ile aynı yol.
- [ ] `BeeBooster`: hedef tarh seçimi (en yakın aynı renk dolmamış), arı sprite'ı uçuş tween'i.
- [ ] HUD: üç düğme + fiyat etiketi (büyük yazı modunda 20 pt), seçim modu vurgusu, Vazgeç; `booster_min_level` (9) Remote Config.
- [ ] `EconomyService.TrySpend` entegrasyonu; `MoveHistory` snapshot'ı Yağmur için alınır, Kürek için alınmaz.
- [ ] Core `AdsService.ShowRewarded("bee")`; `bee_rewarded_enabled` Remote Config; ödül yalnızca SDK callback'inde.
- [ ] Edit Mode testleri: `ShiftAll` 4 yön ve engeller, Arı hedef seçimi, Kürek geçersiz hedef.

### Analitik
- `booster_use` {type: string, level: int, move_n: int, paid_with: string ("coin" | "rewarded"), cost: int} — uygulamada
- `booster_cancel` {type: string} — iptalde
- `bee_ad_offered` · `bee_ad_rewarded` · `bee_ad_failed` {reason: string}

### Notlar
- Karar: booster hamle sayılmaz ve yıldızı cezalandırmaz; "dürüst" sözleşme için booster bir ceza değil bir kısayoldur. Soft launch'ta booster/seviye oranı yüksekse (> 0,3) fiyatlar yükseltilir (Remote Config).
- Karar: Kürek geri alınamaz (kalıcı değişim), Yağmur geri alınabilir (konum değişimi). Arı geri alınamaz.
- Ödüllü video yerleşimleri MVP'de yalnızca `bee`; `coin_double` ve `extra_undo` 20. story.

## STORY: Tohum 16: Interstitial kotası, dürüstlük şeridi ve No Ads
labels: tema:monetizasyon, P1, efor:M
milestone: MVP

**Tohum** epic'inin **16. story'si** (epic #EPIC). Tema: Ekonomi ve monetizasyon.

**Öncelik:** P1 · **Efor:** M · **Milestone:** MVP · **Dokunduğu yer:** `AdQuota` (Core), `AdsService` (Core), `HonestyStrip`, `NoAdsGate`, `IapService` (Core), Map ve Play üst çubuğu

### Kullanıcı hikâyesi
Bir oyuncu olarak ekranın üstünde "Süre yok · Bugün reklam 1/3 · Seviye arası reklam yok" yazısını görmek ve bunun gerçekten doğru olmasını isterim; No Ads aldığımda şeridin "Reklamsız" demesini ve bir daha hiç interstitial görmemek isterim, böylece Fluffy Drop'ta kırılan güven burada kurulsun.

### Kabul kriterleri
- [ ] **Senaryo: Kota** · Verilen `interstitial_daily_cap = 3`, `interstitial_min_level = 10`, `interstitial_min_gap_min = 8` · Eğer oyuncu seviye ≥ 10'u bitirip Map'e dönerse ve bugünkü sayaç < 3 ve son reklamdan ≥ 8 dk geçtiyse · O zaman Map görünür olduktan 600 ms sonra interstitial gösterilir ve sayaç +1; aksi hâlde hiçbir şey gösterilmez.
- [ ] **Senaryo: Yalnızca haritada** · Verilen kod tabanı · Eğer `AdsService.ShowInterstitial` çağrıları aranırsa · O zaman tek çağrı `MapScreen.OnReturnFromPlay` içindedir; `LevelCompleteOverlay`, `Garden`, `Play`, `ShopScreen` hiçbir reklam API'si çağırmaz (Edit Mode testi: reflection ile çağrı yeri doğrulanır ya da `AdsService` yalnızca `MapScreen` assembly'sinden erişilebilir `internal`).
- [ ] **Senaryo: Gün sıfırlama** · Verilen 3/3 dolu · Eğer yerel tarih değişirse · O zaman sayaç 0/3 olur; cihaz saati geri alınırsa sayaç sıfırlanmaz (son reklam zaman damgası ileri tarihliyse kota dolu sayılır).
- [ ] **Senaryo: Şerit** · Verilen Map ve Play üst çubuğu · Eğer ekran açılırsa · O zaman şerit "Süre yok · Bugün reklam N/3 · Seviye arası reklam yok" (TR) / "No timer · Ads today N/3 · No ads between levels" (EN) metnini gösterir; N her interstitial sonrası güncellenir; büyük yazı modunda iki satıra sarar, kırpılmaz.
- [ ] **Senaryo: Şerit bilgi kartı** · Verilen şerit · Eğer dokunulursa · O zaman sözleşme kartı: 4 madde (süre yok, seviye arası reklam yok, günde en fazla 3 reklam yalnızca haritada, No Ads her şeyi kapatır) + No Ads düğmesi; kapat.
- [ ] **Senaryo: No Ads satın alma** · Verilen Mağaza ya da şerit kartı · Eğer No Ads (4,99 USD) alınırsa · O zaman `NoAdsGate.IsActive` true, bekleyen interstitial iptal, şerit "Süre yok · Reklamsız · Teşekkürler" olur, kota mantığı hiç çalışmaz; ödüllü video (Arı) seçenek olarak kalır.
- [ ] **Senaryo: Geri yükleme** · Verilen yeni cihaz · Eğer "Satın alımları geri yükle" basılırsa · O zaman No Ads tanınır; Cloud Save'de de bayrak tutulur (çevrimdışı açılışta geçerli).
- [ ] **Senaryo: Reklam yüklenemedi** · Verilen kota uygun, reklam yüklü değil · Eğer Map'e dönülürse · O zaman hiçbir şey gösterilmez, sayaç artmaz, bekleme yok (boş ekran ya da "yükleniyor" yok).

### Teknik görevler (Unity/C#)
- [ ] Core `AdQuota`: {dailyCap, minLevel, minGapMin, countToday, lastShownUtc, dayKey}; `CanShow(level, nowUtc) → bool`, `RecordShown()`; yerel kalıcı + Cloud Save; saat koruması (`lastShownUtc > now` ise dolu).
- [ ] `MapScreen.OnReturnFromPlay`: `NoAdsGate` → `AdQuota.CanShow` → `AdsService.IsInterstitialReady` → 600 ms gecikme → `ShowInterstitial("map_return")` → `RecordShown` → şerit güncelle.
- [ ] Erişim kısıtı: `AdsService.ShowInterstitial` `internal` + `InternalsVisibleTo("Game.Map")`; Edit Mode testi: diğer assembly'lerden çağrı derlenmez.
- [ ] `HonestyStrip` prefab'ı: TR/EN metin, N bağlama, dokunma → `ContractCard`; Map ve Play'de aynı prefab.
- [ ] `NoAdsGate` (Core): Unity IAP tüketilemeyen `no_ads`, Cloud Code makbuz doğrulama, Cloud Save bayrağı, `Restore`.
- [ ] Remote Config: `interstitial_daily_cap`, `interstitial_min_level`, `interstitial_min_gap_min`; üst sınır kodda 3 (Remote Config 3'ten fazla yapamaz; sözleşme).
- [ ] Mağaza: No Ads en üstte; soft launch A/B "Bahçıvan paketi" (No Ads + 1.000 coin, 6,99 USD) `noads_bundle_enabled` Remote Config.

### Analitik
- `interstitial_show` {level: int, count_today: int, gap_min: int} — gösterimde
- `interstitial_skipped` {reason: string ("quota" | "min_level" | "gap" | "not_ready" | "no_ads")} — Map dönüşünde gösterilmediğinde
- `honesty_strip_tap` {screen: string} · `noads_view` {source: string} · `noads_purchase` {sku: string, price_local: string}

### Notlar
- Kapı metriği: No Ads dönüşümü ≥ %1,5 (`noads_purchase` / D3'e ulaşan).
- Karar: kota üst sınırı 3 kodda sabit; Remote Config yalnızca düşürebilir. "Günde en fazla 3" mağaza metninde de geçer, vaat tutulur.
- Karar: interstitial seviye 10'dan önce hiç; ilk 10 seviye sözleşmenin doğruluğunu kanıtlar (şerit 0/3 gösterir).
- ABD interstitial eCPM 12–13 USD; günde ≤ 3 ile ARPDAU düşük, No Ads ve coin paketleri ana gelir; bu bilinçli bir takas.

## STORY: Tohum 17: Erişilebilirlik: büyük yazı varsayılan, renk körü sembolleri, ana menü
labels: tema:erisilebilirlik, P1, efor:M
milestone: MVP

**Tohum** epic'inin **17. story'si** (epic #EPIC). Tema: Erişilebilirlik ve ayarlar.

**Öncelik:** P1 · **Efor:** M · **Milestone:** MVP · **Dokunduğu yer:** `BigTextMode` (Core), `SettingsScreen` (Core), `SeedSymbol`, `BedSymbol`, `ArrowInputMode`, `HomeButton`

### Kullanıcı hikâyesi
70 yaşında bir oyuncu olarak yazıları gözlük aramadan okumak, renkleri karıştırsam bile tohumun hangi tarha ait olduğunu sembolden anlamak ve her ekrandan ana menüye tek dokunuşla dönmek isterim; 8 yaşında bir oyuncu olarak da aynı şeyleri isterim.

### Kabul kriterleri
- [ ] **Senaryo: Büyük yazı varsayılan** · Verilen ilk açılış · Eğer herhangi bir ekran çizilirse · O zaman `big_text = true`: gövde 20 pt, düğme 22 pt, başlık 28 pt, HUD sayaçları 24 pt; Ayarlar'da "Normal yazı" seçilirse 16/18/24/20; hiçbir metin kırpılmaz, hiçbir düğme 56 px'den küçük değildir.
- [ ] **Senaryo: TMP kademeleri** · Verilen TR uzun metin ("Seviye arası reklam yok") · Eğer düğme genişliği yetmezse · O zaman auto-size alt sınır 16 pt'ye kadar küçülür, hâlâ sığmazsa iki satıra sarar; 3 satır yok.
- [ ] **Senaryo: Renk körü sembolleri** · Verilen varsayılan açık "Sembol ipuçları" · Eğer tohum ve tarh çizilirse · O zaman her renk için sembol (kırmızı daire, mavi kare, sarı üçgen, yeşil yıldız, mor altıgen, turuncu elmas) tohum üstünde ve tarh çerçevesinde görünür; kapatılabilir.
- [ ] **Senaryo: Kontrast** · Verilen palet · Eğer ölçülürse · O zaman metin/zemin ≥ 4,5:1, tohum/zemin ≥ 3:1, altı renk deuteranopi simülasyonunda sembolsüz bile ayırt edilebilir (en az 3 kademe açıklık farkı).
- [ ] **Senaryo: Ok modu** · Verilen Ayarlar'da "Ok ile oyna" açık · Eğer tohuma dokunulursa · O zaman 4 büyük ok (56 px) tohumun çevresinde belirir, oka dokununca kayar; sürükleme de çalışmaya devam eder.
- [ ] **Senaryo: Ana menü** · Verilen Play, Garden, Shop, Settings · Eğer sol üstteki ev düğmesine basılırsa · O zaman Map'e dönülür; Play'de seviye durumu kaydedilir, onay sorulmaz.
- [ ] **Senaryo: Reduce Motion** · Verilen sistem ayarı açık ya da Ayarlar'da açık · Eğer oynanırsa · O zaman parçacık, sarsıntı, çiçek açma taç yaprakları, kaydırma iz animasyonu kapalı; durum değişimleri anında ve renkle; fonksiyon kaybı yok.
- [ ] **Senaryo: Ayarlar listesi** · Verilen Ayarlar · Eğer açılırsa · O zaman Ses, Müzik, Haptik, Büyük yazı, Sembol ipuçları, Ok ile oyna, Reduce Motion, Dil, İpuçlarını sıfırla, Satın alımları geri yükle, Uygulamayı puanla, Gizlilik, Sürüm.

### Teknik görevler (Unity/C#)
- [ ] Core `BigTextMode`: `TextStyle` SO kademeleri {Body, Button, Title, Hud} × {Normal, Big}; tüm TMP bileşenleri `StyledText` ile bağlanır; auto-size min/max.
- [ ] `SeedSymbol`/`BedSymbol`: sembol sprite katmanı, `symbol_hints` ayarı; `SeedColor` → sembol tablosu SO.
- [ ] Palet doğrulama: `docs/palette.md`, deuteranopi/protanopi simülasyon görüntüleri (tasarım aşamasında).
- [ ] `ArrowInputMode`: tohuma dokun → 4 ok overlay, `SlideResolver` aynı; `DragInput` ile birlikte çalışır; `input_mode` ayarı.
- [ ] `HomeButton` tüm sahnelerde (1. story prefab'ı), Play'de `InProgressLevel` kaydı.
- [ ] Core `SettingsScreen` bağlama, oyuna özgü anahtarlar: `big_text` (varsayılan true), `symbol_hints` (true), `input_mode`.
- [ ] In-app review tetik kuralı: 15. seviye 3★ ve hiç çıkmaz yaşanmamış → bir kez (Google Play In-App Review).

### Analitik
- `settings_change` {key: string, value: string} — her değişimde
- `input_mode_used` {mode: string ("drag" | "arrow"), level: int} — seviye başında (hangi mod kullanıldıysa)
- `review_prompt` {shown: bool}

### Notlar
- Büyük yazı varsayılan açık kararı: kitle senior + çocuk; "normal" yazı tercih edenler kapatır (`settings_change` ile ölçülür).
- Sembol seti Daily Drop'un kademe sembolleriyle aynı aileden (Core'da paylaşılır).
- Risk (epic): senior kitlede sürükleme hassasiyeti; Ok modu bunun sigortası, soft launch'ta `input_mode_used` dağılımına bakılır; %30'u aşarsa Ok modu varsayılan olur.

## STORY: Tohum 18: Analitik olay şeması ve kapı raporu
labels: tema:analitik, P1, efor:S
milestone: MVP

**Tohum** epic'inin **18. story'si** (epic #EPIC). Tema: Analitik.

**Öncelik:** P1 · **Efor:** S · **Milestone:** MVP · **Dokunduğu yer:** UGS Analytics olay tanımları, `docs/analytics.md`, `docs/reports/*.sql`

### Kullanıcı hikâyesi
Bir geliştirici olarak soft launch kapısını (D1, D7, seviye 20'ye ulaşma, No Ads dönüşümü, çökme) ve takılma seviyelerini tek sorguyla görmek isterim, böylece Canlı / iterasyon / raf kararı ve Trivia / Puzzle kategori kararı veriyle verilsin.

### Kabul kriterleri
- [ ] **Senaryo: Şema** · Verilen `docs/analytics.md` · Eğer okunursa · O zaman tüm olaylar (app_open, session_start, map_view, level_select, level_start, move, obstacle_intro, cascade, deadlock, undo, hint, level_restart, level_complete, ftue_step, ftue_complete, garden_view, garden_bed_unlock, decor_purchase, coin_earn, coin_spend, shop_view, coin_purchase, booster_use, booster_cancel, bee_ad_*, interstitial_show, interstitial_skipped, honesty_strip_tap, noads_view, noads_purchase, settings_change, input_mode_used, review_prompt) parametre tipleriyle listelidir ve UGS'de tanımlıdır.
- [ ] **Senaryo: Doğrulama** · Verilen dev build · Eğer 10 seviye oynanıp bir dekor alınırsa · O zaman UGS Event Browser'da olaylar sırayla ve doğru parametrelerle görünür; `move` örnekleme kuralı (ilk 30 seviye tam, sonra her 5.) doğrulanır.
- [ ] **Senaryo: Kapı sorgusu** · Verilen UGS SQL · Eğer `gate_report.sql` çalıştırılırsa · O zaman D1, D7, seviye 20'ye ulaşma oranı, No Ads dönüşümü (D3 tabanlı), çökme oranı, ortalama oturum, Bahçem açma oranı tek tabloda gelir.
- [ ] **Senaryo: Seviye hunisi** · Verilen `level_funnel.sql` · Eğer çalıştırılırsa · O zaman seviye başına start, complete, tamamlama oranı, ortalama restart, ortalama süre, deadlock oranı, booster/seviye, hint/seviye; tamamlama < %85 ya da restart > 1,5 olan seviyeler işaretli.
- [ ] **Senaryo: Reklam sözleşmesi denetimi** · Verilen `ad_audit.sql` · Eğer çalıştırılırsa · O zaman oyuncu-gün başına `interstitial_show` sayısı dağılımı; 3'ü aşan tek bir satır bile varsa test başarısız (sözleşme ihlali).
- [ ] **Senaryo: Gizlilik** · Verilen olaylar · Eğer incelenirse · O zaman kişisel veri (ad, e-posta, kesin konum, yaş) yoktur; çocuk kitle için reklam SDK'sı kişiselleştirme kapalı (19. story).

### Teknik görevler (Unity/C#)
- [ ] Core `Analytics` sarmalayıcısı: `Track(string, Dictionary<string, object>)`, kuyruk + çevrimdışı tampon; `move` örnekleyici.
- [ ] UGS Analytics custom event tanımları (dashboard'dan), parametre tipleri.
- [ ] `docs/analytics.md`; `docs/reports/gate_report.sql`, `level_funnel.sql`, `economy_flow.sql` (kaynak/batak günlük), `ad_audit.sql`, `noads_funnel.sql` (noads_view → noads_purchase, kaynak: strip / shop / contract card).
- [ ] Crashlytics (Firebase) ya da UGS Crash raporlama: çökme oranı için.

### Analitik
- Bu story şemanın kendisidir.

### Notlar
- MustyBlockBlast ve Daily Drop'taki `docs/UGS SQL` yaklaşımı aynen uygulanır.
- `ad_audit.sql` kapı raporunun parçasıdır: sözleşme ihlali varsa kapı geçilmiş sayılmaz.

## STORY: Tohum 19: Mağaza sayfası, ASO ve soft launch yapılandırması
labels: tema:magaza, P1, efor:M
milestone: MVP

**Tohum** epic'inin **19. story'si** (epic #EPIC). Tema: Mağaza ve çıkış.

**Öncelik:** P1 · **Efor:** M · **Milestone:** MVP · **Dokunduğu yer:** Play Console, mağaza metinleri, ekran görüntüleri, kapalı test, kategori, aile politikası

### Kullanıcı hikâyesi
Bir geliştirici olarak oyunu "No Timer" vaadini başlıkta taşıyarak, Fluffy Drop'un Trivia kategorisi taktiğini deneyerek üç ülkede soft launch'a çıkarmak isterim, böylece organik aramadan (indirmelerin ≈%65'i) gelen gerçek oyuncu verisi gelsin.

### Kabul kriterleri
- [ ] **Senaryo: Başlık ve metin** · Verilen Play listesi · Eğer yayımlanırsa · O zaman EN başlık "Tohum: No Timer Garden Puzzle" (30 karakter sınırı), TR "Tohum: Süresiz Bahçe Bulmacası"; kısa açıklama ≤ 80 karakter ("No timer. No ads between levels. Slide seeds, fill the beds, grow your garden."); tam açıklamada "no timer", "no ads between levels", "relaxing garden puzzle", "color match slide puzzle", "max 3 ads a day" geçer; uzun tire yok.
- [ ] **Senaryo: Görseller** · Verilen ekran görüntüleri · Eğer yüklenirse · O zaman 8 görüntü (1: oyun + dürüstlük şeridi büyük, 2: "Süre yok" metni, 3: "Seviye arası reklam yok", 4: Bahçem, 5: çiçek açma, 6: büyük yazı/senior eli, 7: boosterlar, 8: harita), 1 özellik grafiği, 15 sn video (isteğe bağlı); görsellerde büyük yazı modu açık.
- [ ] **Senaryo: Kategori** · Verilen soft launch · Eğer yayımlanırsa · O zaman kategori Trivia (Fluffy Drop #2 taktiği); etiketler: Puzzle, Casual, Relaxing, Offline; kategori soft launch boyunca değişmez, karar kapı raporunda.
- [ ] **Senaryo: Aile politikası** · Verilen karma kitle (çocuk + yetişkin) · Eğer Play beyanı doldurulursa · O zaman hedef kitle "karma" (6+), reklam SDK'sı çocuk yönelimli işaret (MAX `setIsAgeRestrictedUser`, AdMob `tagForChildDirectedTreatment` ağ bazında), kişiselleştirilmiş reklam kapalı, veri güvenliği formu doğru.
- [ ] **Senaryo: Kapalı test** · Verilen 12+ test kullanıcısı · Eğer 14 gün sürerse · O zaman Play üretim erişimi formu doldurulur (MustyBlockBlast #1326 deneyimi); test grubunda en az 3 senior.
- [ ] **Senaryo: Ülkeler ve fiyat** · Verilen soft launch · Eğer yayımlanırsa · O zaman yalnızca TR, PH, CA; No Ads ve coin paketleri yerel fiyat kademesi (TR için Play yerel kademe).
- [ ] **Senaryo: Sürüm bilgisi** · Verilen build · Eğer yayımlanırsa · O zaman versionCode ve versionName CHANGELOG ile eşleşir; 1.0 (120 seviye), 1.1 ve 1.2 sürüm planı CHANGELOG'da; gizlilik politikası yayında.

### Teknik görevler (Unity/C#)
- [ ] Mağaza metinleri `docs/store/{en,tr}.md`; ASO anahtar kelime listesi (no timer, no ads between levels, relaxing garden puzzle, color match slide puzzle, slide puzzle, flower garden, senior friendly puzzle); rakip başlıkları (Fluffy Drop, Water Sort, Pixel Flow).
- [ ] Ekran görüntüsü şablonu (Design kanvası, Tohum · Oyun artboard'undan), 1080×1920; büyük yazı modu açık.
- [ ] Aile politikası: `AudienceConfig` (1. story) → MAX SDK bayrakları; veri güvenliği formu; `docs/privacy.md`.
- [ ] Play Console kurulumu: iç test → kapalı test → üretim (3 ülke); kategori Trivia.
- [ ] Sürümleme: `CHANGELOG.md`, `versionCode` CI'da otomatik; 1.1/1.2 seviye paketleri için Remote Config `max_level_available` planı.
- [ ] Play listing experiments: başlık A/B ("No Timer Garden Puzzle" vs "Relaxing Garden Puzzle") soft launch 2. haftasında.

### Analitik
- Mağaza: Play Console dönüşüm oranı, kategori sıralaması (günlük elle not, `docs/soft-launch-log.md`), arama terimi raporu.

### Notlar
- Risk (epic): Trivia kategorisi tutmazsa Puzzle'a geçiş sıralamayı sıfırlayabilir; bu yüzden soft launch boyunca sabit.
- Karar: başlıkta "Tohum" kalır (marka, TR nişi); EN kitle için alt başlık taşıyıcıdır.
- Aile politikası tek başına 1 gün: Play'in karma kitle incelemesi gecikebilir; kapalı testle paralel başlatılır.

## STORY: Tohum 20: Ödüllü video yerleşimleri: coin ikiye katlama ve ek geri al
labels: tema:monetizasyon, P2, efor:S
milestone: Soft launch

**Tohum** epic'inin **20. story'si** (epic #EPIC). Tema: Ekonomi ve monetizasyon.

**Öncelik:** P2 · **Efor:** S · **Milestone:** Soft launch · **Dokunduğu yer:** `AdsService` (Core, ödüllü `coin_double`, `extra_undo`), `LevelCompleteOverlay`, `UndoService`, `EconomyService`

### Kullanıcı hikâyesi
Bir oyuncu olarak istersem seviye sonunda bir reklam izleyip coin'imi ikiye katlamak ya da ücretsiz geri al hakkım bitince reklamla bir tane daha almak isterim; geliştirici olarak her ödüllü yerleşimin oyuncunun seçimi olmasını ve asla boş teklif göstermemesini isterim.

### Kabul kriterleri
- [ ] **Senaryo: Coin ikiye katla** · Verilen 2★ bitiş (45 coin), ödüllü reklam yüklü · Eğer overlay açılırsa · O zaman "Devam" (birincil) yanında "Reklam izle · 2×" (ikincil) görünür; reklam sonuna kadar izlenirse +45 coin, yarıda kapatılırsa hiçbir şey olmaz; Devam her zaman reklamsızdır.
- [ ] **Senaryo: Ek geri al** · Verilen ücretsiz geri al kullanıldı, coin < 50, reklam yüklü · Eğer Geri al'a basılırsa · O zaman "50 coin" ya da "Reklam izle" seçenekleri; reklamla alınan geri al anında uygulanır; seviye başına en fazla 2 reklamlı geri al.
- [ ] **Senaryo: Reklam yok** · Verilen ödüllü reklam yüklenemedi · Eğer overlay ya da geri al açılırsa · O zaman reklam seçeneği hiç görünmez (boş teklif yok); 10 sn sonra yeniden yüklenir.
- [ ] **Senaryo: Sıklık** · Verilen `coin_double_min_level = 10` ve `coin_double_every_n = 2` · Eğer seviye < 10 ya da her 2. bitişten biri değilse · O zaman 2× teklifi görünmez (Remote Config).
- [ ] **Senaryo: No Ads** · Verilen No Ads aktif · Eğer overlay açılırsa · O zaman 2× teklifi yine görünür (ödüllü video oyuncunun seçimidir, No Ads kapatmaz); Ayarlar'da "Ödüllü video tekliflerini gizle" anahtarı vardır.
- [ ] **Senaryo: Güvenilirlik** · Verilen reklam izlendi, uygulama callback'ten önce kapandı · Eğer yeniden açılırsa · O zaman ödül verilmez ve teklif yeniden sunulur (ödül yalnızca SDK callback'inde).

### Teknik görevler (Unity/C#)
- [ ] Core `AdsService.ShowRewarded("coin_double" | "extra_undo")`, yükleme/yeniden deneme, `OnRewarded` güvenilirliği.
- [ ] `LevelCompleteOverlay`: 2× düğmesi, `coin_double_min_level`, `coin_double_every_n` Remote Config, `EconomyService.Earn(amount, "rewarded")`.
- [ ] `UndoService`: `rewardedUndoPerLevel = 2`, seçenek kartı (coin / reklam).
- [ ] Ayarlar: `hide_rewarded_offers` anahtarı.
- [ ] Tüm ödüllü yerleşimlerin listesi `docs/ads.md`: `bee`, `coin_double`, `extra_undo`; interstitial `map_return`.

### Analitik
- `rewarded_offered` {placement: string, level: int} · `rewarded_start` · `rewarded_rewarded` {placement: string, amount: int} · `rewarded_failed` {placement: string, reason: string}

### Notlar
- ABD ödüllü video eCPM ≈ 15 USD; interstitial kotası düşük tutulduğu için gelir tarafı ağırlıkla ödüllü video ve IAP. Soft launch'ta `rewarded_offered → rewarded_rewarded` oranı ≥ %25 bekleniyor.
- P2 kararı: MVP yalnızca `bee` ile çıkar; soft launch 1. haftasında bu story eklenir ve etkisi D7 ile karşılaştırılır.

## STORY: Tohum 21: Türkçe bitki ve çiçek koleksiyonu kartları
labels: tema:meta, P3, efor:M
milestone: Canlı

**Tohum** epic'inin **21. story'si** (epic #EPIC). Tema: Meta ve tutma.

**Öncelik:** P3 · **Efor:** M · **Milestone:** Canlı · **Dokunduğu yer:** `CollectionScreen`, `PlantCard` SO, `GardenBed` çiçek türü bağı, Core koleksiyon iskeleti

### Kullanıcı hikâyesi
Bir oyuncu olarak bahçemde açan her çiçeğin adını, kısa bir notunu ve resmini içeren bir kart kazanmak isterim (lale, sümbül, karanfil, gül, zambak, menekşe …), böylece bahçem bir koleksiyon olsun ve Türkiye nişinde kültürel bir bağ kursun.

### Kabul kriterleri
- [ ] **Senaryo: Kart kazanımı** · Verilen Bahçem'de yeni tarh açıldı · Eğer açılış animasyonu biterse · O zaman o tarhın çiçek türü kartı "Koleksiyona eklendi" kartıyla gösterilir (bir kez); 40 tarh = 40 kart (4 bölge × 10 tür).
- [ ] **Senaryo: Koleksiyon ekranı** · Verilen Bahçem'deki "Koleksiyon" düğmesi · Eğer açılırsa · O zaman 40 kart ızgarası, kazanılanlar renkli (ad, 2 cümle not, resim), kazanılmayanlar siluet + "Seviye 85"; büyük yazı modunda not 20 pt.
- [ ] **Senaryo: İçerik** · Verilen kart metinleri · Eğer okunursa · O zaman TR birinci sınıf (Türkçe bitki adları ve Anadolu bağlamı: lale, sümbül, karanfil, nergis, gelincik, lavanta, çiğdem, kardelen …), EN çeviri; bilimsel ad küçük yazıyla.
- [ ] **Senaryo: Paylaşım** · Verilen kazanılan kart · Eğer "Paylaş" basılırsa · O zaman 1080×1350 PNG (kart + "Tohum" logosu + mağaza linki); oyuncu adı yok.
- [ ] **Senaryo: Kalıcılık** · Verilen kartlar · Eğer Cloud Save geri yüklenirse · O zaman koleksiyon `openedBeds`'ten türetilir (ayrı durum yok).

### Teknik görevler (Unity/C#)
- [ ] `PlantCard` SO ×40 (nameKey, noteKey, latinName, sprite, region, bedIndex); `GardenBed` → kart bağı.
- [ ] `CollectionScreen` (Core koleksiyon iskeleti), siluet/renkli durum, detay kartı.
- [ ] `ShareService` (Core, Daily Drop 12. story) ile kart PNG.
- [ ] İçerik: 40 kart metni TR/EN `docs/collection.md`; sanat: 40 çiçek illüstrasyonu (Bahçem çiçek sprite'larıyla paylaşılır, 13. story sanatının üstüne 40 tekil varyant).

### Analitik
- `collection_view` {owned: int} · `card_unlock` {card_id: string, level: int} · `card_share` {card_id: string}

### Notlar
- P3: Bahçem açma oranı ≥ %60 doğrulandıktan sonra; sanat maliyeti yüksek (40 illüstrasyon), Canlı sonrası.
- TR nişi: mağaza TR metnine "Türkiye'nin çiçekleri" vurgusu bu story ile eklenir.

## STORY: Tohum 22: Mevsimsel bahçe etkinlikleri
labels: tema:meta, P3, efor:L, SONRA
milestone: SONRA

**Tohum** epic'inin **22. story'si** (epic #EPIC). Tema: Meta ve tutma.

**Öncelik:** P3 · **Efor:** L · **Milestone:** SONRA · **Dokunduğu yer:** `SeasonEvent`, Remote Config etkinlik takvimi, `MapScreen` etkinlik düğümü, geçici seviye paketi, etkinlik dekoru

### Kullanıcı hikâyesi
Bir oyuncu olarak baharda "Lale Festivali", sonbaharda "Hasat" gibi 2 haftalık etkinliklerde 15 özel seviye oynayıp bahçeme mevsimlik dekor kazanmak isterim, böylece 200 seviye bitse bile geri dönmek için bir sebebim olsun.

### Kabul kriterleri
- [ ] **Senaryo: Takvim** · Verilen Remote Config `season_events` · Eğer bugün etkinlik aralığındaysa · O zaman Map üstünde etkinlik düğümü ve geri sayım (gün cinsinden; saat yok) görünür; aralık dışında düğüm yok.
- [ ] **Senaryo: Seviye paketi** · Verilen etkinlik · Eğer düğüme dokunulursa · O zaman 15 seviyelik ayrı yol (Addressables uzak grup ile indirilir, ≤ 100 KB), aynı kurallar, etkinlik temalı tarh/çiçek görselleri; süre ve hamle sınırı yine yok.
- [ ] **Senaryo: Ödül** · Verilen 5/10/15 seviye tamamlandı · Eğer eşik geçilirse · O zaman etkinlik dekoru (örn. lale tarhı, balkabağı) Bahçem'e eklenir; coin ödülü yok (ekonomi bozulmasın).
- [ ] **Senaryo: Bitiş** · Verilen etkinlik bitti · Eğer tamamlanmamışsa · O zaman kazanılan dekor kalır, yol kapanır, ilerleme bir sonraki aynı etkinlikte devam etmez; uyarı 3 gün önce (bildirim yok, Map rozeti).
- [ ] **Senaryo: Sözleşme** · Verilen etkinlik · Eğer oynanırsa · O zaman dürüstlük şeridi ve interstitial kotası aynen geçerli; etkinlik ekranında reklam yok.

### Teknik görevler (Unity/C#)
- [ ] `SeasonEvent` modeli ve Remote Config şeması {id, startUtc, endUtc, levelPack, rewards[]}; `SeasonService`.
- [ ] Addressables uzak katalog (UGS CCD) ile seviye paketi; `LevelCatalog` çoklu kaynak.
- [ ] Map etkinlik düğümü, etkinlik yolu ekranı, ödül akışı (13. story dekor sistemi).
- [ ] 4 etkinlik şablonu (Lale, Yaz, Hasat, Kış) × 15 seviye (editör + prosedürel).

### Analitik
- `event_view` {event_id: string} · `event_level_complete` {event_id: string, n: int} · `event_reward` {event_id: string, tier: int}

### Notlar
- LiveOps'un en ucuz biçimi değil (içerik var) ama tek seferlik şablonla 4 mevsim döner; Canlı sonrası, 200 seviyeyi bitiren oyuncu oranı ≥ %10 olunca.

## STORY: Tohum 23: 5 kişilik asenkron yarış (opsiyonel, kapatılabilir)
labels: tema:meta, P3, efor:L, SONRA
milestone: SONRA

**Tohum** epic'inin **23. story'si** (epic #EPIC). Tema: Meta ve tutma.

**Öncelik:** P3 · **Efor:** L · **Milestone:** SONRA · **Dokunduğu yer:** `RaceService`, UGS Leaderboards + Cloud Code eşleştirme, Map yarış kartı, Ayarlar "Yarışları kapat"

### Kullanıcı hikâyesi
Bir oyuncu olarak istersem haftalık 5 kişilik küçük bir grupta kim daha çok yıldız topluyor yarışına katılmak, istemezsem bunu tamamen kapatıp hiç görmemek isterim, böylece sakin bulmaca sözleşmesi bozulmadan rekabet seven oyuncuya bir kanca olsun.

### Kabul kriterleri
- [ ] **Senaryo: Katılım** · Verilen seviye ≥ 30 · Eğer Map'teki "Yarış" kartına dokunulursa · O zaman "Haftalık yarışa katıl?" açıklaması ve Katıl / Hayır; Hayır bir daha sormaz (Ayarlar'dan açılır).
- [ ] **Senaryo: Eşleştirme** · Verilen katılım · Eğer Cloud Code çalışırsa · O zaman benzer `maxLevel` (±20) 5 oyuncu bir gruba (eksikse bot değil, "henüz 3 kişi" gösterilir); hafta Pazartesi 00:00 UTC başlar.
- [ ] **Senaryo: Puan** · Verilen hafta içi · Eğer seviye tamamlanırsa · O zaman kazanılan yıldız (yalnızca yeni ya da yükselen) gruba işlenir; booster ve ipucu etkilemez.
- [ ] **Senaryo: Sonuç** · Verilen hafta bitti · Eğer Map açılırsa · O zaman sıralama kartı; 1. 300 coin, 2. 200, 3. 100, diğerleri 50; coin dışında ödül yok.
- [ ] **Senaryo: Kapatma** · Verilen Ayarlar'da "Yarışlar: kapalı" · Eğer Map açılırsa · O zaman yarış kartı, bildirim, rozet hiç görünmez; mevcut hafta sessizce biter.
- [ ] **Senaryo: Gizlilik** · Verilen grup üyeleri · Eğer gösterilirse · O zaman takma ad yok, rastgele bitki adı + numara ("Lavanta 42"); sohbet yok; çocuk kitle için güvenli.

### Teknik görevler (Unity/C#)
- [ ] UGS Leaderboards haftalık tablo + Cloud Code `joinRace` / `submitStars` / `closeWeek`; grup id'si Cloud Save.
- [ ] `RaceService`, Map yarış kartı, sonuç kartı, `race_enabled` ayarı ve Remote Config kill switch.
- [ ] Haftalık coin ödülü `EconomyService.Earn(amount, "race")`; `docs/economy.md` güncellenir.
- [ ] Takma ad üretimi: bitki adı listesi (Core yerelleştirme tablosu) + 2 hane, Cloud Code tarafında grup içinde benzersiz; uygunsuz ad riski yok (liste sabit).

### Analitik
- `race_opt_in` {joined: bool} · `race_week_result` {rank: int, stars: int} · `race_disabled` {source: string}

### Notlar
- Opsiyonel ve kapatılabilir: sözleşmenin parçası ("sakin" vaadi). Katılım oranı < %15 ise özellik kaldırılır.
- Sohbet ve gerçek ad yok: aile politikası ile uyum.

## STORY: Tohum 24: Günlük bulmaca
labels: tema:ritual, P3, efor:M, SONRA
milestone: SONRA

**Tohum** epic'inin **24. story'si** (epic #EPIC). Tema: Günlük ritüel.

**Öncelik:** P3 · **Efor:** M · **Milestone:** SONRA · **Dokunduğu yer:** `DailyPuzzle`, `LevelGenerator` (seed'li), Map günlük düğümü, `DailyGift` ile birleşik kart

### Kullanıcı hikâyesi
Bir oyuncu olarak her gün herkesle aynı tek bir bulmacayı çözmek ve seri tutmak isterim, böylece 200 seviyeyi bitirsem de her gün bir sebebim olsun (Daily Drop ritüelinin Tohum'a taşınması).

### Kabul kriterleri
- [ ] **Senaryo: Günün bulmacası** · Verilen yerel tarih · Eğer Map'teki "Bugün" düğümüne dokunulursa · O zaman `seed = SHA256("tohum:" + yyyy-MM-dd)` ile `LevelGenerator` 7×9, 4 renk, optimal 8–12 bir seviye üretir; aynı tarih her cihazda aynı seviye (Edit Mode testi).
- [ ] **Senaryo: Çözülebilirlik** · Verilen üretilen seviye · Eğer çalışma zamanında `LevelSolver` (50.000 durum) çözemezse · O zaman seed +1 ile yeniden üretilir (en fazla 5 deneme; deterministik).
- [ ] **Senaryo: Ödül ve seri** · Verilen tamamlama · Eğer gün biterse · O zaman 80 coin (3★ 120), seri +1; seri 7'de ek 200 coin; kaçırılan gün seri 0 (dondurma yok).
- [ ] **Senaryo: Tek deneme yok** · Verilen günlük bulmaca · Eğer yeniden başlatılırsa · O zaman sınırsız deneme (sözleşme: ceza yok); yıldız en iyi sonuçtan.
- [ ] **Senaryo: Hatırlatma** · Verilen Ayarlar'da günlük hatırlatma açık · Eğer gün değişirse · O zaman seçilen saatte yerel bildirim; bugün oynandıysa gelmez.

### Teknik görevler (Unity/C#)
- [ ] `DailyPuzzle`: seed, `LevelGenerator` (11. story) deterministik mod (kendi PRNG, xoshiro256** Core'dan), çözülebilirlik döngüsü.
- [ ] Map "Bugün" düğümü, günlük kart (DailyGift ile birleşik: hediye + bulmaca), seri `StreakService` (Core, Daily Drop 13. story).
- [ ] Unity Mobile Notifications (Core `LocalNotifications`), Ayarlar saat seçici.
- [ ] `DailyPuzzleState` {dayIndex, bestStars, moves, completed} Cloud Save; gün değişimi `MidnightWatcher` (Core, Daily Drop 8. story) ile Map kartı yenilenir.
- [ ] Edit Mode testleri: aynı tarih aynı seviye (3 seed örneği), 5 denemede çözülebilirlik, seri artış/kırılma.

### Analitik
- `daily_puzzle_start` {day_index: int} · `daily_puzzle_complete` {day_index: int, moves: int, stars: int, streak: int}

### Notlar
- 11. story'nin üreticisi kaskad/kanal üretmediği için günlük bulmaca v1'de taş/kütük/ot ile sınırlı; yeterli.
- Daily Drop'un paylaşım kartı burada yok (sakin oyun, sosyal baskı yok); yalnızca seri.
