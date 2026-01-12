# 🏪 Melih's CasaOS App Store

Kendi özel CasaOS uygulama mağazam. Bu repository'yi CasaOS'a ekleyerek uygulamaları kurabilirsiniz.

## 📦 Mevcut Uygulamalar

| Uygulama                          | Açıklama                | Kategori  |
| --------------------------------- | ----------------------- | --------- |
| [LLM Council](Apps/LLMCouncil) | Çoklu AI Sohbet Arayüzü | Developer |
| [HumanVerify](Apps/HumanVerify) | Video Canlılık Tespiti ve Deepfake Önleme | Developer |

## 🚀 App Store Nasıl Eklenir?

### Yöntem 1: CasaOS Arayüzü

1. CasaOS Dashboard'a gidin
2. App Store'a tıklayın
3. Sağ üstteki ⚙️ ayarlar ikonuna tıklayın
4. "Add Source" butonuna tıklayın
5. Aşağıdaki URL'yi ekleyin:
   ```
   https://github.com/melihtop35/Homeapp/archive/refs/heads/master.zip
   ```

### Yöntem 2: Terminal

```bash
casaos-cli app-management register app-store https://github.com/melihtop35/Homeapp/archive/refs/heads/master.zip
```

## 📁 Repository Yapısı

```
casaos-appstore/
├── README.md
├── category-list.json          # Kategori tanımları
├── recommend-list.json         # Öne çıkan uygulamalar
├── Apps/
│   ├── llm-council/
│   │   ├── docker-compose.yml  # CasaOS metadata dahil
│   │   ├── icon.png            # Uygulama ikonu (256x256)
│   │   └── screenshot-1.png    # Ekran görüntüsü (opsiyonel)
│   ├── another-app/
│   │   ├── docker-compose.yml
│   │   └── icon.png
│   └── ...
```

## 🗂️ Repo Modeli

- Bu ana repo sadece CasaOS mağaza verilerini içerir (kategori listesi, öneriler, her uygulama için compose/icon dosyaları).
- `Apps/*` altındaki her uygulama klasörü kendi içinde ayrı bir git reposudur; uygulamanın kod geçmişi burada tutulur.
- Ana repoda uygulama kodunu commit etmeyin; gerekli mağaza dosyalarını (docker-compose, icon vb.) uygulama reposunda da tutup burada da güncellediğinizden emin olun.

## ➕ Yeni Uygulama Ekleme

1. `Apps/` klasöründe yeni bir klasör oluşturun (uygulama adı)
2. `docker-compose.yml` dosyası oluşturun ve `x-casaos` metadata ekleyin
3. `icon.png` dosyası ekleyin (256x256 piksel önerilir)
4. `recommend-list.json` dosyasını güncelleyin (opsiyonel)

## 🔧 Gereksinimler

- Docker compose formatında yapılandırma
- `x-casaos` metadata bloğu zorunlu
- İkon dosyası (PNG, 256x256 önerilir)

## 📝 Lisans

MIT License

## 🤝 Katkıda Bulunma

Pull request'lerinizi bekliyorum!
