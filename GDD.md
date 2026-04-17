# Game Design Document (GDD)

## 1. Oyun Künyesi

| Alan | Değer |
|------|-------|
| **Oyun Adı (çalışma)** | TurboLane (değiştirilebilir) |
| **Tür** | 2D Arcade Araba Yarışı — **Local 2-Player (couch versus)** |
| **Perspektif** | Top-down (kuş bakışı) — paylaşımlı tek ekran |
| **Platform** | PC (Windows/macOS/Linux) + Web (WebGL) |
| **Motor Önerisi** | Godot 4 / Unity 2D / PixiJS |
| **Hedef Kitle** | 10+ yaş, arkadaş/kardeş/aile ile aynı klavyede oynayanlar |
| **Oyuncu Sayısı** | **1-2 (tek klavye)** — 1P solo veya 2P versus |
| **Oynanış Süresi** | Tek yarış 2-4 dk, kampanya 4-6 saat |
| **Fiyat Modeli** | Premium ($9.99) veya Free-to-Play + kozmetik |

---

## 2. Oyun Vizyonu (High Concept)

**Tek cümleyle:** Tek klavyede 2 kişinin yan yana oynadığı, paylaşımlı ekranda drift ve nitro ile birbirini geçmeye çalıştığı hızlı tempolu 2D yarış oyunu.

**Referanslar:**
- *Micro Machines* (paylaşımlı ekran, top-down, couch versus — ana referans)
- *Mario Kart (split dynamics)* (iki oyuncu rekabet hissi)
- *Hotline Miami* (görsel stil, ivme hissi)
- *Art of Rally* (drift mekaniği)
- *TowerFall Ascension* (local multiplayer'ın "tekrar oyna!" döngüsü)

**Benzersiz Özellik (USP):** Tek klavye ile iki oyuncu — ergonomik tuş ayrımı, kamera otomatik olarak ikisini de çerçeveler, dinamik zoom ile rekabet hissini yükseltir.

---

## 3. Oynanış (Gameplay)

### 3.1 Temel Döngü (Core Loop)
```
Pist Seç → Yarış → Ödül (para + yıldız) → Araç/Upgrade Al → Zorlaşan Pist
```

### 3.2 Kontroller — Tek Klavye 2 Oyuncu

Klavye fiziksel olarak ikiye bölünür: **sol taraf = Oyuncu 1**, **sağ taraf = Oyuncu 2**. Her oyuncu 6 tuş kullanır — parmak çakışması yok, aynı anda 12 tuş baskısına dayanıklı klavye gerekmez (6+6 ayrı bloklar).

#### Oyuncu 1 (Sol el bloğu — WASD çevresi)

| Aksiyon | Tuş |
|---------|-----|
| Gaz | **W** |
| Fren / Geri | **S** |
| Sol | **A** |
| Sağ | **D** |
| Drift / El Freni | **Sol Shift** |
| Nitro | **Sol Ctrl** |

#### Oyuncu 2 (Sağ el bloğu — Ok tuşları çevresi)

| Aksiyon | Tuş |
|---------|-----|
| Gaz | **↑** |
| Fren / Geri | **↓** |
| Sol | **←** |
| Sağ | **→** |
| Drift / El Freni | **Sağ Shift** |
| Nitro | **Enter** (veya Numpad 0) |

#### Sistem

| Aksiyon | Tuş |
|---------|-----|
| Duraklat | **Esc** veya **P** |
| Onayla/Menü | Space |

**Önemli — Klavye Ghosting:** Ucuz membran klavyelerde aynı anda 3'ten fazla tuş basılınca takılma (ghosting) olabilir. Tasarım sol ve sağ blokları fiziksel olarak ayırdığı için çoğu klavyede sorun yaşanmaz, fakat **oyun içi "klavye testi" ekranı** ilk açılışta tüm tuşları test ettirip sorunu erken gösterir. Alternatif tuş eşlemeleri (remapping) menüden değiştirilebilir.

**1 Oyuncu Modu:** Oyuncu 1 tuşları aktif, Oyuncu 2 AI tarafından oynanır. Gamepad desteği opsiyonel (ileride).

### 3.3 Sürüş Mekaniği

- **Hız:** Araç tipine göre 0-280 km/h arası.
- **Drift:** El freni + yön tuşu. Drift boyunca "boost meter" dolar.
- **Nitro:** Drift metre dolduğunda 2 sn boost verir (+%25 hız).
- **Çarpışma:** Duvara çarpma %40 hız kaybı; rakibe bindirme ise dirsek mekaniği (yan vurma).
- **Pist Tutunması:** Çim/kum bölgelerde tutunma %50 düşer.

### 3.4 Fizik (Basitleştirilmiş)

```
acceleration = engine_power - drag * velocity^2 - friction
steering = base_steering * (1 - speed_factor) + drift_bonus
```

Gerçekçi Box2D veya özel arcade fizik. Arcade his öncelikli.

---

## 4. Oyun Modları

Tüm modlar hem 1P hem 2P oynanabilir. 2P modunda ikinci oyuncu her zaman aynı klavyede; AI rakipler boşta kalan slotları doldurur (örn. 2P + 2 AI = 4'lü yarış).

1. **Versus (Ana Mod — 2P odaklı):** İki oyuncu kafa kafaya. 1-3-5 yarışlık seri ("Best of"). Seri sonu skor tablosu. *Odak buradan başlar.*
2. **Kampanya (Career):** 4 şampiyona × 6 yarış = 24 pist. 1P için AI rakiplere karşı. 2P'de "co-op kampanya" — ikisi beraber sıralamada üst 2'ye girmeli.
3. **Party Mode (2P):** Kısa mini-oyunlar — son arabada kalan kazanır, çekiç-çekiç, halka toplama yarışı vb. (4-6 mini-oyun).
4. **Zaman Deneme (Time Trial — 1P):** Kendi hayaletine karşı. Lokal leaderboard (aile/arkadaş isimleri).
5. **Serbest Yarış (Quick Race):** Pist + araç seçimi, 1P veya 2P.

**Tasarım önceliği:** Versus > Party > Time Trial > Kampanya. Menüde ilk sekme **"2 Oyuncu"**.

---

## 5. İçerik

### 5.1 Araçlar (Başlangıç)

| Araç | Hız | İvme | Tutunma | Kilit |
|------|-----|------|---------|-------|
| Hatchback | 5/10 | 7/10 | 8/10 | Açık |
| Muscle | 8/10 | 5/10 | 6/10 | 2. şampiyona |
| Rally | 6/10 | 7/10 | 9/10 | 3. şampiyona |
| Formula | 10/10 | 9/10 | 5/10 | Son şampiyona |

Yükseltmeler: Motor, Lastik, Nitro, Şasi (her biri 3 seviye).

### 5.2 Pistler

- **4 Tema:** Şehir (gece neon), Kır (gündüz), Çöl (gün batımı), Dağ (karlı).
- Her tema 6 pist + 1 ters versiyon.
- Uzunluk: 45-90 sn tur.
- Pist elemanları: zıplama rampası, kestirme, su birikintisi, trafik (isteğe bağlı).

---

## 6. Sanat Yönü

- **Stil:** Piksel art (32x32 tile) + ışık efektleri (WebGL shader) **VEYA** düşük-poli vektörel (flat shading).
- **Palet:** 16-renk retro + neon vurgular (mor, turkuaz, magenta).
- **Kamera (2P için kritik):** Paylaşımlı tek kamera. İki aracı da çerçevede tutan **dinamik zoom** — araçlar yaklaştıkça yakınlaşır, uzaklaştıkça açılır. Max mesafe aşılırsa ekran ikiye bölünür (dinamik split-screen) VEYA geride kalan araç "lastik bandı" (rubber-band) ile otomatik çekilir (Micro Machines tarzı). **MVP'de dinamik zoom + max-mesafe ile geride kalana elektrik şoku (hız kaybı)** tercih ediliyor — kod ve sanat açısından split'ten daha basit.
- **UI:** Minimal HUD — tur, hız, sıralama, nitro barı.

**Örnek renk paleti (hex):** `#0D0221 #241734 #F92C85 #2DE2E6 #F9F871`

---

## 7. Ses Tasarımı

- **Müzik:** Synthwave / chiptune karışımı. Her tema için 1-2 track (4-6 track toplam).
- **SFX:**
  - Motor (RPM'e göre dinamik pitch)
  - Drift (lastik cıyaklaması)
  - Nitro (whoosh)
  - Çarpışma (metalik çıngırak)
  - UI (8-bit beep)
- **Voice:** Yarış başı sayaç, "DRIFT!" / "BOOST!" anonsları.

---

## 8. İlerleme & Ekonomi

- **Para (Coins):** Yarış sonu ödülü. Pozisyon × zaman bonusu.
- **Yıldızlar:** Her pistte 3 hedef (1. gel / tur rekoru / hasar almama).
- **Kilit Açma:** Yıldız sayısı → yeni şampiyona/araç.
- **Kozmetik:** Boya, sticker, jant (sadece görsel).

---

## 9. Teknik Gereksinimler

| Alan | Karar |
|------|-------|
| Çözünürlük | 1920×1080 default, 16:9 ölçekli |
| FPS Hedef | 60 sabit |
| Kaydetme | Lokal JSON (ilerde bulut) |
| Yerelleştirme | TR, EN (başlangıç) |
| Min RAM | 2 GB |
| Min GPU | Entegre (Intel HD 4000+) |

---

## 10. Geliştirme Yol Haritası

### Faz 1 — Prototip (4 hafta)
- [ ] Araç fiziği (1 araç, 1 pist)
- [ ] Temel drift + nitro
- [ ] Yarış bitiş koşulu + süre

### Faz 2 — Dikey Dilim (6 hafta)
- [ ] 3 araç, 4 pist (1 tema tamamen bitirilmiş)
- [ ] AI rakip davranışı (waypoint + gumming)
- [ ] Temel UI + menü akışı
- [ ] Oyna-test-iterasyon

### Faz 3 — İçerik Üretimi (10 hafta)
- [ ] 4 tema × 6 pist
- [ ] Tüm araçlar + yükseltme sistemi
- [ ] Ses + müzik entegrasyonu
- [ ] Kampanya akışı

### Faz 4 — Cila (4 hafta)
- [ ] Parçacık efektleri, shader polish
- [ ] Performans optimizasyonu
- [ ] Bug bash + balancing
- [ ] Lokalizasyon

### Faz 5 — Yayın (2 hafta)
- [ ] Steam sayfası, trailer, demo
- [ ] Kapanış testleri, sertifikasyon

**Toplam:** ~26 hafta (~6 ay) solo/2 kişilik ekip için.

---

## 11. Riskler & Azaltma

| Risk | Olasılık | Etki | Azaltma |
|------|----------|------|---------|
| Fizik "yapışkan" hissederse | Orta | Yüksek | Erken playtest, arcade değerlerine öncelik |
| AI çok kolay/zor | Yüksek | Orta | Rubber-banding, 3 zorluk seviyesi |
| İçerik şişmesi | Orta | Yüksek | 24 pist sabit; ekstra pist sonraya |
| Performans (WebGL) | Düşük | Orta | Tile-batching, object pooling |

---

## 12. Başarı Metrikleri (Post-Launch)

- D1 Retention > %40
- Ortalama oturum > 20 dk
- Kampanya bitirme oranı > %25
- Steam "Çoğunlukla Olumlu" (%70+)

---

## 13. Ekip (Önerilen)

- 1× Programcı (gameplay + fizik)
- 1× Piksel sanatçısı
- 1× Ses tasarımcısı (freelance)
- 1× QA/tester (son 2 ay)

Solo geliştirme için: asset store + freelance müzisyen.

---

## 14. Ek Notlar / Açık Sorular

- [x] **Perspektif:** Top-down seçildi (2P paylaşımlı kamera için en uygunu)
- [x] **Oyuncu sayısı:** 1-2, tek klavye — netleşti
- [ ] Kamera stratejisi: Dinamik zoom vs. dinamik split-screen — prototipte test edilecek
- [ ] Power-up/silah olsun mu? — **2P rekabeti için güçlü argüman var** (Mario Kart hissi); MVP sonrası 3-4 basit power-up eklenebilir
- [ ] Hikâye olsun mu? (2P versus odaklıyken gereksiz — çıkarılabilir)
- [ ] Gamepad desteği ne zaman? (MVP sonrası, tek klavye öncelikli)

---

*Bu doküman canlı bir referanstır. Prototip çıktılarına göre revize edilir.*
