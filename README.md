# NightWave

Windows için karanlık temalı, minimalist masaüstü müzik çalar. YouTube'dan reklamsız akış, MP3 indirme,
çalma listeleri, kişisel öneriler, **müzik görselleştirici**, mini oynatıcı, medya tuşları ve görev çubuğu entegrasyonu.

## Kurulum (en kolay yol)

1. **Python 3.11 veya üstünü** kurun: https://www.python.org/downloads/  
   Kurulumda **"Add python.exe to PATH"** kutusunu işaretleyin.
2. Bu klasörde **`kur.bat`** dosyasına çift tıklayın. Şunları yapar:
   sanal ortam oluşturur → Python paketlerini kurar → **Deno** (YouTube için) ve **FFmpeg**'i (MP3 indirme için)
   `winget` ile kurar → sonunda bir sistem kontrolü çalıştırır.
3. **`calistir.bat`** ile uygulamayı başlatın.

> `kur.bat` ile yeni program kurulduysa, `calistir.bat`'ı **yeni** bir pencerede açın (PATH yenilensin).

### Elle kurulum (PowerShell)
```powershell
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
winget install DenoLand.Deno
winget install Gyan.FFmpeg
python main.py
```

## Nasıl çalıyor? (müziğin geç açılması / çalmaması için burayı okuyun)
Bir şarkıya **tek tıklayınca** uygulama sesi indirmeye başlar ve **ilk birkaç yüz KB gelir gelmez çalmaya başlar** (**hızlı başlat**),
indirme arka planda sürer. Ses çalma hızından (~16 KB/s) çok daha hızlı indiği için bağlantı yavaş olsa bile kesintisiz çalar.
Teknik olarak: yt-dlp'nin yazmakta olduğu dosya, uygulamanın içindeki küçük bir **yerel sunucudan** (yalnızca `127.0.0.1`) Qt'ye verilir.
Qt'nin YouTube adreslerini doğrudan açamadığı sistemlerde bu yöntem çalışır.

Her aşamada bir **yedek** vardır; siz bir şey yapmazsınız:
1. Hızlı başlat çalışmazsa (ses 15 sn içinde gelmezse / Qt çözemezse) → **tamamı inince yerelden** çalar.
2. Ham ses (m4a/webm) bu sistemde Qt'de hiç çalmazsa → **MP3'e çevirir** (FFmpeg ile), bunu hatırlar ve bir daha ham denemez.
3. Hâlâ olmuyorsa net bir hata mesajı (kırmızı `⚠`) gösterir.

Ayrıca **sıradaki şarkı** ve **fare ile üstüne geldiğiniz** (0,45 sn) şarkı arka planda hazırlanır; tıklayınca anında başlar.
Aynı şarkı tekrar çalınınca da anında başlar (ses önbelleği, varsayılan 500 MB, eskiler otomatik silinir).
Çalan şarkıya tekrar tıklamak **duraklatır / devam ettirir**.

- Ayarlar → **Müzik**: hızlı başlat, oynatma yöntemi, sıradakini/üstüne geleni önceden hazırlama, **IPv4** (YouTube çok yavaşsa dene), önbellek.
- Önbellekteki ses dosyaları sizin **indirdikleriniz** (İndirilenler) değildir; onlar MP3 olarak kalıcıdır.

### Yine de yavaşsa: nerede yavaşlıyor?
Hazırlık sırasında durum satırında aşama ve hız görünür (`Çözümleniyor…` → `Hazırlanıyor… %40 · 1.2 MB/s`; hız çok düşükse `· yavaş`).
Konsola da her şarkı için `çözümleme X sn · indirme Y sn (hız)` yazılır:
- **Çözümleme uzun** (10 sn üstü): Deno/JS çözücü sorunu. Ayarlar → **Sistem** sekmesine ve `python diagnose.py` çıktısına bakın.
- **İndirme yavaş** (`KB/s`): YouTube/ISS kısıtlaması olabilir. Ayarlar → Müzik'ten **"Yalnızca IPv4 kullan"** seçeneğini deneyin;
  konsolda `UYARI: JS çözücü çalışmıyor…` yazıyorsa Deno'yu kurun (`winget install DenoLand.Deno`).
- `python diagnose.py`: hızlı başlatın ilk veriyi kaç saniyede aldığını ve Qt'nin bunu çalıp çalamadığını doğrudan ölçer.

## Ana sayfa
Uygulama açılınca arama yapmadan **öneri rafları** gelir (her raf kapaklı kartlardan oluşur; karta tıkla = çal, sağ tık = menü, başlığa tıkla = tümü):
**Sana özel** (dinleme geçmişin + beğenilerin), **“…” aramana göre** (son aramaların), **Son dinlediklerin**, **Beğendiklerin**, **İndirdiklerin**
ve seçtiğin **türler** (Türkçe Pop, Rap, Rock, Lo-fi, Yeni Çıkanlar… Ayarlar → Müzik'ten 12 türden seç).
Bir şey aradıkça ve dinledikçe raflar sana göre şekillenir. Arama sonuçlarından **← Ana sayfa** ile ya da boş aratarak dönebilirsin.
Raflar diskte saklanır: sonraki açılışta internet beklemeden dolu görünür. Çevrimdışıyken ana sayfa yerel raflarla açılır.

## Neden Deno ve FFmpeg?
| Program | Ne için | Yoksa ne olur |
|---|---|---|
| **Deno** (ya da Node/Bun) | YouTube artık ses adreslerini çözmek için JavaScript çalıştırmayı şart koşuyor (yt-dlp bunu kullanır) | **Hiçbir şarkı çalmaz, indirilmez** |
| **FFmpeg** | **Kalıcı MP3 indirme** (İndir düğmesi) için ses dönüştürme. Önbellekten çalma FFmpeg istemez | Çalma çalışır, sadece **MP3 indirme** çalışmaz |

Uygulama bunları kendisi arar (PATH'te olmasa bile `winget` klasörlerine de bakar). Eksik varsa üstte **sarı bir uyarı bandı**
çıkar; **Ayarlar → Sistem** sekmesinde de durum ve kopyalanabilir kurulum komutları görünür.

## Sorun giderme
- **Müzik çalmıyor / indirmiyor:** önce uygulamanın üstündeki uyarı bandına ya da **Ayarlar → Sistem**'e bakın.
  Hâlâ çözülmediyse `python diagnose.py` çalıştırın; adım adım nedeni ve çözümü yazar.
- **Hata mesajları** arama kutusunun altındaki durum satırında **kırmızı** (`⚠`) görünür.
- **Beklenmeyen hata:** uygulama kapanmaz; ayrıntı `%APPDATA%\NightWave\error.log` dosyasına yazılır.
- **YouTube bozulursa** (yt-dlp güncellemesi gerekir): `pip install -U "yt-dlp[default]"`
- **Medya tuşları / medya paneli (SMTC):** Ayarlar → Genel'de "Şu an aktif" satırı hangi yöntemin çalıştığını gösterir.
- **Spotify:** Ayarlar → Spotify'a Client ID/Secret girin (developer.spotify.com/dashboard). Yalnızca arama ve parça bilgisi içindir;
  Spotify parçaları, "Sanatçı - Şarkı" YouTube'da aranarak çalınır.

## Müzik görselleştirici
Alttaki oynatıcı çubuğuna (**kapağa, şarkı adına ya da ▲ düğmesine**) tıklayın; **Küçült** düğmesi ya da **Esc** ile geri dönersiniz.
**Küçük ekran (mini oynatıcı)** de aynı görselleştiriciyi gösterir (stil ve renk Çalıyor ekranındaki seçimi izler). Çalan sesin **gerçek spektrumunu** (FFT) çizer:
5 stil (Çubuklar · Aynalı · Bloklar · Dalga · Dairesel), 2 renk modu (vurgu rengi / gökkuşağı), 32–96 çubuk, duyarlılık ayarı,
bas vuruşuna tepki. **F11** ile tam ekran, **Esc** ile çıkış. Dairesel stilde kapak halkanın ortasında döner.

- Gerçek analiz Qt 6.8+ ve FFmpeg arka ucu gerektirir (kurulumda hazır gelir) ve `numpy` kullanır.
  Ses verisi alınamazsa görsel, müziğin ritmine uyumlu bir **simülasyona** düşer; ekrandaki etiket hangisinde olduğunu söyler.
- Yakalama yalnızca **Çalıyor** ekranı ilk açıldığında bağlanır; görselleştiriciyi hiç açmayanın çalma yolu değişmez.
  Etiket "simülasyon" diyorsa bir sonraki şarkıya geçin. Ayarlar → Görünüm'den gerçek analiz kapatılabilir.
- Ekran kartı yükleniyorsa çubuk sayısını azaltın (CPU'da çizilir).

## Özellikler
Keşfet (YouTube / Spotify arama) · reklamsız akış · MP3 indirme ve çevrimdışı mod · sistem tepsisi · mini oynatıcı ·
karışık/tekrar · kuyruk · çalma listeleri (özelleştirme, paylaşım kodu, dosya, M3U) · ♥ Beğenilenler ·
**Sana özel** öneriler ve **radyo** · 6 tema + açık tema + sistemi izleme + özel tema oluşturucu + tema paylaşımı +
kapaktan vurgu rengi · **müzik görselleştirici (5 stil, tam ekran)** · Windows medya tuşları (SMTC) · görev çubuğu düğmeleri, ilerleme ve rozet.

## Veriler nerede?
`%APPDATA%\NightWave\` → `config.json`, `library.json`, `playlists.json`, `history.json` (dinleme geçmişi, yalnızca bu cihazda), `covers\`, `error.log`.  
Müzikler: `Müzik\NightWave` (Ayarlar'dan değiştirilebilir).

## Proje yapısı
```
main.py            giriş noktası          diagnose.py   sorun tanı aracı
kur.bat            tek tıkla kurulum      calistir.bat  uygulamayı başlat
core/  youtube · spotify · player · progressive · cache · play_order · library · playlists · history · recommend · home · analyzer · audio_tap
       health (harici program kontrolü) · errors (anlaşılır hata mesajları) · system_media (SMTC) · workers · config
ui/    main_window · home_view · thumbs · now_playing · visualizer · mini_player · tray · taskbar · settings_dialog · theme_dialog · playlist_ui · styles · icons · colors
tests/ saf mantık testleri + sahte Qt ile arayüz akış testi (gerçek Qt gerektirmez)
```

## Testler
```powershell
python tests/run_all.py
```
Gerçek Qt gerektirmeyen 10 test dosyası çalışır: indirirken çalma (gerçek yerel sunucuyla), önbellek, ana sayfa, ses analizi (FFT/tampon/vuruş), görselleştirici stilleri, öneri motoru, çalma listeleri,
temalar, hata mesajları ve arayüz akışları (sahte Qt ile).

## Not
YouTube'dan reklamsız akış ve indirme, YouTube'un kullanım şartlarıyla çelişebilir; kişisel kullanım sorumluluğu size aittir.
