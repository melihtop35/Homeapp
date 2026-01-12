# HumanVerify - Video Liveness Detection API

## Genel Bakış

HumanVerify, Casaos üzerinde GPU ile çalışan, video tabanlı insan doğrulama sistemidir. Aşağıdaki teknolojileri kullanarak sahte profilleri, deepfake'leri ve uygunsuz sunuşları tespit eder:

- **Face Detection**: MediaPipe
- **Liveness Detection**: LBP + Texture Analysis
- **Deepfake Detection**: FaceForensics++
- **Face Recognition**: FaceNet/ArcFace
- **Expression Analysis**: Custom Action Unit Model

---

## Dosya Yapısı

```
HumanVerify/
├── Dockerfile              # Docker image definition
├── docker-compose.yml      # Casaos deployment configuration
├── requirements.txt        # Python dependencies
├── config.py              # Configuration management
├── app.py                 # FastAPI main application
├── models_placeholder.py   # Future model modules
├── .env.example           # Environment variables template
├── .dockerignore          # Docker build exclusions
├── README.md              # This file
│
├── models/                # [Will be created] Pre-trained models
├── uploads/               # [Created at runtime] Temporary uploads
├── temp/                  # [Created at runtime] Temporary processing
└── logs/                  # [Created at runtime] Application logs
```

---

## Kurulum & Çalıştırma

### 1. Casaos'ta Hızlı Başlangıç

```bash
# HumanVerify klasörüne git
cd /home/casaos/Apps/HumanVerify

# Docker compose ile başlat
docker-compose up -d

# Logları kontrol et
docker-compose logs -f humanverify-api

# Health check
curl http://localhost:8000/health
```

### 2. API Test

```bash
# API info
curl http://localhost:8000/

# Health check
curl http://localhost:8000/health

# Readiness check
curl http://localhost:8000/ready

# Swagger documentation
# Browser'da aç: http://casaos-ip:8000/docs
```

### 3. Video Doğrulama

```bash
# Single video verification
curl -X POST http://localhost:8000/api/verify-video \
  -F "file=@video.mp4"

# Reference photo ile
curl -X POST http://localhost:8000/api/verify-video \
  -F "file=@video.mp4" \
  -F "reference_photo=@profile.jpg"

# Batch verification
curl -X POST http://localhost:8000/api/verify-video-batch \
  -F "files=@video1.mp4" \
  -F "files=@video2.mp4" \
  -F "files=@video3.mp4"
```

---

## API Endpoints

### Health & Status

| Method | Endpoint      | Description               |
| ------ | ------------- | ------------------------- |
| `GET`  | `/`           | API info                  |
| `GET`  | `/health`     | Health check (GPU status) |
| `GET`  | `/ready`      | Readiness check           |
| `GET`  | `/api/config` | API configuration         |
| `GET`  | `/api/models` | Loaded models info        |

### Verification

| Method | Endpoint                  | Description                       |
| ------ | ------------------------- | --------------------------------- |
| `POST` | `/api/verify-video`       | Single video verification         |
| `POST` | `/api/verify-video-batch` | Batch video verification (max 10) |

### Verification Response

```json
{
  "success": true,
  "recommendation": "approve|reject|manual_review",
  "overall_score": 0.85,
  "processing_time": 25.5,
  "details": {
    "face_detection": {
      "success": true,
      "score": 1.0,
      "face_percentage": 95.2
    },
    "liveness": {
      "success": true,
      "score": 0.92
    },
    "deepfake": {
      "success": true,
      "deepfake_probability": 0.05
    },
    "expression": {
      "success": true,
      "expression_score": 0.88
    },
    "face_matching": {
      "success": true,
      "similarity": 0.78
    }
  }
}
```

---

## Özellikler

### Şu Anda Aktif (Phase 1)

✅ Docker & Casaos entegrasyonu  
✅ RTX 3050 GPU desteği  
✅ API framework (FastAPI)  
✅ Health check endpoints  
✅ Dosya upload & validation  
✅ Batch processing desteği  
✅ Logging & monitoring  
✅ CORS & security headers

### Phase 2 (Model Implementation)

⏳ Face Detection (MediaPipe)  
⏳ Liveness Detection  
⏳ Deepfake Detection  
⏳ Face Recognition  
⏳ Expression Analysis  
⏳ Verification Pipeline  
⏳ Manual review queue

### Phase 3 (Backend Integration)

⏳ PostgreSQL database  
⏳ Redis caching  
⏳ User verification status  
⏳ Moderator dashboard  
⏳ Audit logs

---

## Konfigürasyon

`.env` dosyasını oluştur (`.env.example`'dan kopyala):

```bash
cp .env.example .env
```

### Önemli Ayarlar

```env
# GPU
CUDA_VISIBLE_DEVICES=0          # GPU device ID

# Thresholds
FACE_DETECTION_THRESHOLD=0.70   # Yüz tespit güveni
LIVENESS_THRESHOLD=0.60          # Canlılık skoru
DEEPFAKE_THRESHOLD=0.50          # Deepfake ihtimali
FACE_MATCH_THRESHOLD=0.65        # Yüz eşleşme benzerliği

# Scoring
AUTO_APPROVE_SCORE=0.85          # Otomatik onayla
MANUAL_REVIEW_SCORE=0.70         # İnsan kontrolü
AUTO_REJECT_SCORE=0.70           # Otomatik reddet

# Timeouts
VIDEO_TIMEOUT=120                # Video işleme timeout (saniye)
PROCESS_TIMEOUT=180              # Toplam işlem timeout (saniye)
```

---

## Performance

### RTX 3050 ile Beklenen Performans

| Model                    | VRAM   | Time/Video | Accuracy |
| ------------------------ | ------ | ---------- | -------- |
| MediaPipe Face Detection | 50 MB  | 2-3 sec    | 99.5%    |
| Liveness Detection       | 100 MB | 5-8 sec    | 95%      |
| Deepfake Detection       | 150 MB | 15-20 sec  | 94%      |
| Face Recognition         | 80 MB  | 5-10 sec   | 99.3%    |
| Expression Analysis      | 100 MB | 5-8 sec    | 92%      |

**Toplam Sequential Süre**: 32-49 saniye  
**Toplam Parallel Süre**: 15-20 saniye  
**VRAM Gerekli**: ~500 MB (RTX 3050: 8GB)

---

## Log Files

```bash
# Real-time logs
docker-compose logs -f humanverify-api

# Specific lines
docker-compose logs --tail=100 humanverify-api

# Container'a bağlan
docker-compose exec humanverify-api bash

# Log dosyasını kontrol et (container içinde)
cat /app/logs/humanverify.log
```

---

## Troubleshooting

### GPU Algılanmıyor

```bash
# Docker'da NVIDIA desteği kontrol et
docker run --rm --gpus all nvidia/cuda:11.8.0-runtime-ubuntu22.04 nvidia-smi

# Docker daemon'ı restart et
sudo systemctl restart docker

# Casaos'ta GPU aktif mi kontrol et
docker run --rm --gpus all pytorch/pytorch:2.0-cuda11.8-runtime-ubuntu22.04 python -c "import torch; print(torch.cuda.is_available())"
```

### Memory Issues

```bash
# GPU memory temizle
docker exec humanverify-api python -c "import torch; torch.cuda.empty_cache()"

# Container resource limits kontrol et
docker stats humanverify-api

# Temp files temizle
rm -rf ./temp/*
rm -rf ./uploads/*
```

### API Yanıt Vermiyorsa

```bash
# Container durumu
docker-compose ps

# Container logs
docker-compose logs humanverify-api

# Port açık mı kontrol et
netstat -an | grep 8000

# API test
curl -v http://localhost:8000/health
```

---

## Docker Compose Komutları

```bash
# Start service
docker-compose up -d

# Stop service
docker-compose down

# Rebuild image
docker-compose build --no-cache

# View logs
docker-compose logs -f humanverify-api

# Execute command in container
docker-compose exec humanverify-api bash

# Remove all volumes
docker-compose down -v

# Scale service (if needed)
docker-compose up -d --scale humanverify-api=2
```

---

## Security

- ✅ CORS middleware yapılandırılı
- ✅ File type validation
- ✅ File size limits (100MB video, 10MB photo)
- ⏳ API key authentication (Phase 3)
- ⏳ JWT token support (Phase 3)
- ⏳ Rate limiting (Phase 3)

---

## Monitoring

### Health Endpoints

```bash
# Application health
GET /health
→ GPU status, memory usage, version

# Service readiness
GET /ready
→ All dependencies loaded and accessible

# Configuration
GET /api/config
→ API settings and capabilities

# Model status
GET /api/models
→ Loaded models and their accuracy metrics
```

### Metrics (Future)

```bash
# Prometheus metrics
GET /metrics
```

---

## Development

### Local Development (Windows/Mac)

```bash
# Virtual environment
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Run FastAPI dev server
uvicorn app:app --reload --port 8000
```

### Testing

```bash
# Test API endpoints
curl http://localhost:8000/health

# Test with sample video (Phase 2)
curl -X POST http://localhost:8000/api/verify-video \
  -F "file=@sample_video.mp4"
```

---

## Next Steps

1. **Phase 2**: Model implementation (MediaPipe, Liveness, Deepfake detection)
2. **Phase 3**: Backend database & moderator dashboard
3. **Phase 4**: Frontend integration (React Native)
4. **Phase 5**: Production deployment & scaling

---

## Support & Documentation

- FastAPI Docs: `http://casaos-ip:8000/docs`
- ReDoc: `http://casaos-ip:8000/redoc`
- Config: `config.py`
- Models: `models_placeholder.py`

---

**Version**: 1.0.0  
**Last Updated**: 2026-01-12  
**Status**: Ready for Phase 2 (Model Implementation)
