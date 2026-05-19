---
layout: post
title: "Gemma 4 26B-A4B’yi küçük GPU’lara indirgeme: Türkçe odaklı MoE budama"
date: 2026-05-19
---

Google’ın Gemma 4 26B-A4B modelini, Türkçe + İngilizce kullanım senaryosu için buduyoruz. İlk kanıt-çalışma Türkçe üzerinde; ama yöntem dil-agnostik: belirli dil/script örüntülerinde düşük aktive olan expert’ları ölç, kes, sonra kısa bir LoRA “iyileştirmesi” ile toparla.

## Motivasyon

MoE modellerde expert’lar zamanla örtük uzmanlaşma geliştiriyor: CJK karakterleri, Kiril, Devanagari, Arap alfabesi, Hangul gibi örüntüler farklı expert kümelerinde yoğunlaşabiliyor. Türkçe + İngilizce hedefli bir dağıtımda, expert’ların bir kısmı neredeyse hiç ateşlenmiyor.

## Yöntem (özet)

- Router katmanlarına hook ekleyip Turkish + code + math + web veri karışımında expert aktivasyonlarını topluyoruz.
- Uzun kuyruktaki düşük kullanım expert’larını katman bazında cerrahi şekilde çıkarıyoruz.
- Kesim sonrası bozulmayı telafi etmek için Türkçe instruction verisiyle kısa bir LoRA heal koşuyoruz.

## İlk sonuçlar

- 128 → 101 expert / layer
- 26B → 21B parametre (yaklaşık %21 küçülme)
- 4-bit GGUF boyutu: yaklaşık 11 GB
- 24 GB GPU’da rahat çalışıyor; IQ4_XS ile 12 GB sınıfı da mümkün
- Türkçe akıcılık + kod + genel bilgi: pratik testlerde güçlü

## Neden “prune + heal”?

Sıfırdan pretrain aylar ve çok yüksek maliyet demek; ayrıca Gemma’nın mevcut ön-eğitim değerini çöpe atıyor. Sadece finetune ise aynı büyük modeli taşımaya devam ediyor. Prune + heal yaklaşımı, pretrain bilgisini korurken kullanılmayan kapasiteyi atıyor.

## Neden önemli?

VRAM ucuzlasa bile görev-başına özelleşmiş küçük modeller (planner, vision, coder) yan yana koşacak. Bu dünyada ana beceri “sıfırdan dev model kurmak” değil, güçlü bir tabanı göreve göre inceltmek olacak.

Model bağlantısı: [huggingface.co/esokullu/gemma4-tr-26b-a4b-pruned-gguf](https://huggingface.co/esokullu/gemma4-tr-26b-a4b-pruned-gguf)
