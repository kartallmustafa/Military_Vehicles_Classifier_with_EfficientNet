# Askerî Araç Sınıflandırma (EfficientNet-B2)

PyTorch ile geliştirilmiş, 22 sınıflı askerî araç görüntü sınıflandırma çalışması. Ön eğitimli (ImageNet) EfficientNet-B2 üzerinde aktarmalı öğrenme (transfer learning) uygulanmıştır.

## Veri Seti

- 22 sınıf (BMP-1, BMP-2, BTR-60/70/80, T-14/64/72/80/90, Iskander, Pantsir-S1, Tornado vb.)
- Eğitim: 4853 görüntü, test: 1218 görüntü
- Sınıf dağılımı dengesizdir: eğitim kümesinde sınıf başına görüntü sayısı 10 (Tornado) ile 413 (BTR-80) arasında değişir.

## Yöntem

| Bileşen | Ayar |
|---|---|
| Model | EfficientNet-B2 (`IMAGENET1K` ağırlıkları) |
| Backbone | Dondurulmuş (`model.features`) |
| Sınıflandırıcı | `Dropout(0.3)` + `Linear(1408, 22)` |
| Girdi boyutu | 288×288 (ağırlıkların kendi dönüşümü: resize, normalize) |
| Kayıp fonksiyonu | `CrossEntropyLoss` |
| Optimizer | Adam, öğrenme oranı 1e-3 |
| Batch boyutu | 32 |
| Epoch | 30 |

Yalnızca sınıflandırıcı katman eğitilmiştir (özellik çıkarıcı sabit).

## Sonuçlar

| Metrik | 30. epoch |
|---|---|
| Eğitim doğruluğu | %63,7 |
| Test doğruluğu | %51,0 |
| Test kaybı | 1,83 |

Eğitim doğruluğunun da düşük kalması, dondurulmuş ImageNet özelliklerinin birbirine benzeyen araç sınıflarını (BTR serisi, T serisi tanklar) ayırmak için yetersiz olduğunu göstermektedir (düşük uyum, underfitting).

## Sınırlamalar

- Backbone dondurulduğu için model alana özgü ayrıntıları öğrenememektedir.
- Eğitimde veri artırma (augmentation) kullanılmamıştır.
- Sınıf dengesizliği için ağırlıklandırma veya örnekleme uygulanmamıştır.
- Sınıf bazlı doğruluk ve karışıklık matrisi analizi yapılmamıştır.

## Planlanan Çalışmalar

- [ ] Backbone'un son aşamalarını açarak ince ayar (fine-tuning)
- [ ] Veri artırma (yatay çevirme, küçük döndürme, renk değişimi)
- [ ] Sınıf ağırlıklı kayıp fonksiyonu
- [ ] Karışıklık matrisi ve sınıf bazlı değerlendirme
- [ ] Katmana özgü öğrenme oranı ve öğrenme oranı çizelgesi

## Kullanım

```bash
pip install torch torchvision torchinfo
```

Veri seti `military_vehicles_dataset/train` ve `military_vehicles_dataset/test` klasör yapısında olmalıdır. Notebook'u baştan sona çalıştırmak yeterlidir.
