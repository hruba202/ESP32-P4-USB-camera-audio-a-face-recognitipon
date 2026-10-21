# ESP32P4-USB camera-audio-face recognition Esphome
waveshare esp32p4 dev board-huskylens-usb cam-media player-Homeassistant

# ESP32-P4: USB kamera, audio a HuskyLens

Experimentální projekt ESPHome pro **Waveshare ESP32-P4-WIFI6-POE-ETH**:

- USB kamery přes USB-A (UVC/MJPEG), obraz v Home Assistantu.
- Přehrávání audia přes ES8311 a `media_player`.
- HuskyLens V1 přes I2C, vlastní komponenta `huskylens_bridge`.
- Ethernet IP101; HomeKey zůstává na samostatném ESP32, BLE proxy není součástí.

## Konfigurace

| Soubor | Účel |
|---|---|
| `esp32p4.yaml` | Základ: Ethernet, HuskyLens a audio |
| `esp32p4-camera.yaml` | USB kamera 640×480, limit 10 fps |
| `esp32p4-camera-720p.yaml` | USB kamera 1280×720, limit 10 fps |

Varianty používají `packages`, proto zachovejte všechny tři YAML soubory
a adresář `components/`. Kamera je připnutá na commit
`53a315e76c69178d913e6744314408a98bc87234` z
[youkorr/esphome](https://github.com/youkorr/esphome/tree/53a315e76c69178d913e6744314408a98bc87234/esphome/components/esp_video_camera).
Lokální `camera_log_compat` doplňuje kompatibilitu logování pro ESPHome 2026.9.0.

## Spuštění

Vytvořte vlastní `secrets.yaml` (nepatří do Gitu):

```yaml
esp_encryption_key: "SEM_VLOZTE_VLASTNI_API_KLIC"
ota_password: "SEM_VLOZTE_VLASTNI_OTA_HESLO"
```

API klíč musí být platný 32bajtový klíč v Base64. Potom ověřte a sestavte
zvolenou variantu, například:

```sh
esphome config esp32p4-camera-720p.yaml
esphome compile esp32p4-camera-720p.yaml
```

Firmware nahrajte až po úspěšné kompilaci a zařízení přidejte přes ESPHome
integraci v Home Assistantu. Dřívější lokální binárky s testovacími údaji
nejsou určené k nasazení. Varianty sdílejí build adresář; výstupní binárky
patří vždy poslednímu sestavení.

## Ověřený stav

- Plná kompilace: ESPHome **2026.9.0**, ESP-IDF **5.5.5**.
- Test na desce: ESPHome **2026.9.1**, P4 rev1.3, 32 MB PSRAM.
- **Jelly Comb (1BCF:28C4, ve Windows FHD Camera)**: na této kameře
  proběhlo celé dosavadní testování a pocházejí z ní uvedené logy a fps.
- **Nulaxy (0C45:636B, ve Windows USB 2.0 Camera)**: samostatně otestovaná
  uživatelem, funguje bez hlášených problémů; vlastní měření fps není doloženo.
- **Logitech C270 (046D:0825) zatím nefunguje**: host při pokusech hlásí
  `0 USB device(s) on the bus`. Příčina nerozpoznání není zatím určená.
- VGA: uživatelem ověřen současný obraz, přehrávání hudby a načítání HuskyLens.
- 720p: funkční obraz, v dodaném logu **8,1–9,8 fps**. Dlouhodobý souběh
  s audiem a HuskyLens v tomto režimu zatím není doložen.

Původní přiřazení úspěšných testů Logitechu bylo nesprávné; uživatel
21. 10. 2026 výslovně přiřadil celé dosavadní testování k Jelly Comb.

`max_framerate` omezuje předávání do HA, nikoli frekvenci přenosu USB kamery.
Nepřidávejte `usb_host`: použitá UVC komponenta spravuje USB host sama.
Sestavení používá `esp_video 2.3.0` a `usb_host_uvc 2.5.2`; tato verze nemá
opravu [#571](https://github.com/espressif/esp-usb/pull/571), takže funkčnost
s jinými kamerami nebo režimy není zaručená.

Podrobnosti a výsledky jsou v [reportu B](ETAPA_B_KAMERA.md)
a [reportu C](ETAPA_C_KAMERA.md). Starší HomeKey návrhy jsou pouze historické
podklady a nejsou součástí aktuální kamerové konfigurace.

## Vznik projektu

Projekt vznikl ve spolupráci s **ChatGPT / Codex od OpenAI**. AI pomáhala
s průzkumem, konfigurací, implementací komponent a ověřením kompilace;
funkčnost na skutečném hardwaru ověřoval autor projektu.


