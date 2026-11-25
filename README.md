# FORLAS - BPM

FORLAS iş süreçlerinin BPM (iş süreci yönetimi) yaklaşımıyla yönetildiği depo.

## BPM sırası

Her süreç aşağıdaki sırayla ilerler. Bir adım tamamlanmadan sonrakine geçilmez; özellikle taban değer ölçülmeden hedef sürece ve PRD'ye geçilmez.

1. Sorun ve hedef
2. Mevcut süreç (as-is)
3. Ölçüm ve taban değer
4. Kök neden
5. Hedef süreç (to-be)
6. PRD
7. Uygulama

## Klasör yapısı

```
README.md                 ← bu dosya: kurallar ve süreç listesi
_sablonlar/               ← yeni süreç için boş şablonlar
<departman>/
  <surec-adi>/
    README.md             ← süreç kartı: sahip, mevcut adım, durum
    01-sorun-hedef.md
    02-mevcut-surec.md
    03-olcum-taban.md
    04-kok-neden.md
    05-hedef-surec.md
    06-prd.md
    diyagramlar/          ← akış şemaları
    veri/                 ← ölçüm verileri, dışa aktarımlar
    uygulama/             ← otomasyon, sorgu, konfigürasyon
```

## Adlandırma

- Türkçe, küçük harf, Türkçe karakter yok, kelimeler tireyle: `satinalma/bekleyen-fisler`.
- Departman klasörü üst düzeyde, süreç klasörü onun altında.

## Yeni süreç açmak

1. `<departman>/<surec-adi>/` klasörünü aç.
2. `_sablonlar/` içindeki dosyaları kopyala; `surec-README.md` dosyasını `README.md` olarak adlandır.
3. `diyagramlar/`, `veri/`, `uygulama/` klasörlerini aç.
4. Aşağıdaki süreç listesine ekle.

## Süreçler

| Departman | Süreç | Sahip | Mevcut adım |
| --- | --- | --- | --- |
| Satınalma | [Bekleyen fişler](satinalma/bekleyen-fisler/) | Satınalma müdürü | 1) Sorun ve hedef |
