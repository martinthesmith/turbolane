# feat: TurboLane — 2P local HTML5 racing prototype + GDD

## Summary

- **Game Design Document (GDD)** eklendi: tek klavyede 2 oyuncu odaklı, top-down arcade yarış oyunu. Kampanya, versus, party modları planlandı.
- **Çalışan HTML5 Canvas prototipi** eklendi: tek dosya (`index.html`), sıfır bağımlılık, tarayıcıda aç-oyna.
- **Local couch versus** birinci sınıf özellik — klavye sol/sağ blok bölüşümü ile ergonomik 2P.

---

## What's Inside

### `GDD.md` — 14 bölümlük tasarım dokümanı

| Bölüm | İçerik |
|-------|--------|
| 1. Künye | Platform, motor, hedef kitle, oyuncu sayısı (1-2 tek klavye) |
| 2. Vizyon | Micro Machines + Art of Rally + TowerFall referansları |
| 3. Oynanış | Kontrol şeması (sol/sağ klavye blokları), fizik, drift/nitro |
| 4. Modlar | Versus (ana), Party, Time Trial, Kampanya, Serbest |
| 5. İçerik | 4 araç, 4 tema × 6 pist |
| 6. Sanat | Top-down + dinamik zoom kamera (kritik 2P kararı) |
| 7. Ses | Synthwave/chiptune, dinamik motor sesi |
| 8. Ekonomi | Yıldız + coin + kozmetik |
| 9. Teknik | 1080p, 60 FPS, min gereksinimler |
| 10. Yol Haritası | 5 fazlı, ~6 aylık plan |
| 11. Riskler | Fizik hissi, AI dengesi, klavye ghosting |
| 12-13. Metrikler & Ekip | Post-launch KPI'lar |
| 14. Açık Sorular | Dinamik zoom vs split-screen, power-up kararı |

### `index.html` — Çalışan prototip

**Çekirdek sistemler:**
- [x] Araç fiziği: ivme, fren, yön, lateral/rolling sürtünme
- [x] Drift mekaniği (el freni → yan sürtünme düşer, boost dolar)
- [x] Nitro (boost barı tüketir, +impulse)
- [x] Dinamik zoom kamera (iki aracı da çerçeveler)
- [x] Oval pist + iç ada + duvar collision
- [x] Checkpoint-tabanlı lap sayacı
- [x] HUD (hız, tur, boost bar)
- [x] Kazanan ekranı + restart
- [x] Drift duman izi, nitro alev efektleri

**Teknoloji seçimleri:**
- Saf HTML5 Canvas + vanilla JS (framework yok)
- ~600 satır, tek dosya
- Tarayıcıda `file://` ile direkt çalışır, build adımı yok

---

## Controls (Tek Klavye 2 Oyuncu)

| Aksiyon | Oyuncu 1 (Sol) | Oyuncu 2 (Sağ) |
|---------|----------------|----------------|
| Gaz | `W` | `↑` |
| Fren | `S` | `↓` |
| Sol/Sağ | `A` / `D` | `←` / `→` |
| Drift | `Sol Shift` | `Sağ Shift` |
| Nitro | `Space` | `Enter` |

**Not:** Başlangıçta P1 nitro `Sol Ctrl` idi — macOS `Ctrl+Arrow` Spaces değiştirdiği için `Space`'e taşındı.

---

## Test Plan

- [ ] Tarayıcıda `index.html` aç (Chrome/Safari/Firefox) → BAŞLA'ya bas
- [ ] **P1 yalnız** sürüş: WASD ile gaz/fren/dönüş çalışıyor mu?
- [ ] **P2 yalnız** sürüş: Oklar ile aynı davranış
- [ ] **Eşzamanlı giriş:** İki oyuncu aynı anda oynarken tuş çakışması / ghosting yok
- [ ] **Drift:** Sol/Sağ Shift basılı + dönüş → yan kayma olmalı, duman çıkmalı, boost barı dolmalı
- [ ] **Nitro:** Space/Enter → alev + hız artışı, boost azalıyor
- [ ] **Kamera:** İki araç uzaklaşınca zoom açılıyor, yaklaşınca daralıyor
- [ ] **Collision:** Duvara çarpma hız düşürüyor, iç adaya girmiyor
- [ ] **Lap:** 4 checkpoint'i sırayla geçmeden lap artmıyor
- [ ] **Kazanan:** 3 tur tamamlayan "P1/P2 KAZANDI" ekranı görüyor, `R` ile restart çalışıyor
- [ ] **macOS:** `Ctrl+←/→` Spaces değiştirmeyi tetiklemiyor (fix doğrulama)

---

## Architecture Notes

```
index.html
├── <style>          CSS (overlay, HUD, canvas)
├── <body>
│   ├── <canvas>     1280×720, WebGL yok (2D context)
│   └── <overlay>    Başlangıç ekranı + kontrol rehberi
└── <script>         (IIFE)
    ├── Input        keydown/keyup listeners + preventDefault
    ├── TRACK        Pist geometrisi (outer, inner, checkpoints)
    ├── Car          Fizik + render + collision
    ├── Camera       Dinamik merkez + zoom
    ├── drawTrack    Çim/asfalt/duvar/start-line
    ├── drawHUD      Ekran-uzayı paneller + kazanan banner
    └── loop         rAF, dt-clamped, state machine
```

---

## Out of Scope (Gelecek PR'lar)

- AI rakip (1P modu için)
- Ses efektleri (WebAudio API)
- Birden fazla pist/tema
- Power-up sistemi (Mario Kart tarzı)
- Araç seçimi + istatistik farkı
- Gamepad desteği
- Online multiplayer

---

## Risks & Mitigations

| Risk | Durum |
|------|-------|
| Klavye ghosting (ucuz klavye) | Sol/sağ blok ayrımı azalttı; oyun içi test ekranı eklenebilir |
| Fizik "yapışkan" hissedebilir | Erken playtest ile değer tuning — MVP'nin esas testi |
| Collision basit AABB | Oval pist için yeterli; karmaşık pistlerde poligon collision gerekecek |

---

## Screenshots / Demo

`index.html` dosyasını bir tarayıcıda açarak lokal olarak çalıştırılabilir. Launch Preview panelinde de görüntülenebilir.
