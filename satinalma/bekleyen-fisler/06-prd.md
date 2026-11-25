# PRD: Satınalma Bekleyen Fiş Bildirimi

- **Tarih:** 2026-11-25
- **Sahip:** Muhammed
- **Durum:** Plan onaylandı, uygulama bekliyor
- **Canlı doküman:** https://claude.ai/artifact/KphHzUPJbtECJCx9wR4QHP

## Amaç

Logo'da bekleyen alım siparişleri ve faturalanmamış alım irsaliyeleri için satınalma müdürüne otomatik günlük liste gitsin; süre aşılırsa Muhammed'e eskale olsun. Hedef: bekleme süresini kısaltmak ve takılmaları görünür kılmak.

## Kapsam ("bekleyen" tanımı)

Tablo ve alan adları Logo Tiger/Go standardına göredir; firma/dönem numarası ve alanlar SQL'de doğrulanacak.

| Belge | Logo kaynağı | Bekleyen sayılma koşulu | Listeye girme | Eskalasyon |
| --- | --- | --- | --- | --- |
| Sevk edilmemiş alım siparişi | `LG_xxx_yy_ORFICHE` + `ORFLINE`, TRCODE = 2 | AMOUNT − SHIPPEDAMOUNT > 0, satır kapalı değil, teslim tarihi (DUEDATE) geçmiş | Teslim tarihi geçince hemen | Teslim + 5 gün [Tahmin] |
| Faturalanmamış alım irsaliyesi | `LG_xxx_yy_STFICHE`, TRCODE = 1 | BILLED = 0, iptal değil | İrsaliye tarihinde | İrsaliye + 7 gün [Tahmin] |

Kapsam dışı: talep onayı, sipariş onayı adımları; iptal/kapalı fişler.

## Alıcılar

| Kademe | Alıcı | İçerik |
| --- | --- | --- |
| Günlük liste | Satınalma müdürü | Bekleyen tüm fişler |
| Eskalasyon | Muhammed | Eskalasyon eşiğini aşan fişler, aynı mesajda ayrı başlık, Muhammed etiketli [Tahmin] |

Sorumlu modeli: tek sorumlu (seçenek D). Sorgu yine de fişi oluşturan kullanıcıyı (CAPIBLOCK_CREATEDBY) döndürür; ileride kişi bazlı dağıtıma geçiş kolay olsun.

## Kanal ve sıklık

- **Kanal:** Telegram grubu (satınalma müdürü + Muhammed), mesajı bir Telegram botu atar.
- **Sıklık:** Pazartesi–Cumartesi, 16:00 (İstanbul).
- **Sessiz günler:** Pazar ve resmi tatiller [Tahmin].
- Fiş başına ayrı mesaj yok; tek özet mesaj, en eski fiş en üstte, en fazla 10 satır + "ve X fiş daha".

## İşleyiş

Otomasyon sistemi: **n8n** (karar, 2026-11-25).

1. **Veri sorgusu:** Logo SQL'den bekleyen sipariş satırları ve irsaliyeler (fiş no, tarih, tedarikçi, malzeme, miktar, tutar, oluşturan kullanıcı, bekleme günü, kademe). Eşik kuralları sorguda/view'da durur.
2. **Zamanlayıcı:** n8n Schedule, Pzt–Cmt 16:00.
3. **Mesaj oluşturma:** n8n'de gruplama ve metin.
4. **Gönderim:** n8n Telegram düğümü ile gruba.
5. **Kayıt:** gönderilen her bildirim (fiş, tarih, kademe, sonuç) bir log tablosuna yazılır.

## Aşamalar

1. **Veri:** Logo alanlarını doğrulamak, sorguyu yazıp bugünkü bekleyen listeyi çıkarmak (baz ölçüm).
2. **Pilot:** sipariş ve irsaliye listesi birlikte, 1 hafta sadece Muhammed'e test.
3. **Canlı:** gruba açmak, 2 hafta izlemek.
4. **Genişletme:** gerekirse kişi bazlı dağıtım, haftalık yönetici özeti.

## Başarı ölçütleri [Tahmin]

- Ortalama bekleme süresi %30 azalır (baz: ilk hafta).
- Eskalasyon eşiğini aşan fiş sayısı 2 ayda yarıya iner.
- Yanlış/gereksiz bildirim şikayeti haftada 1'in altında.

## Değerlendirilen otomasyon seçenekleri (öncelik sırası)

1. **n8n** — seçildi. Forlas'ta zaten çalışıyor, SQL Server ve Telegram düğümleri hazır.
2. SQL Server Agent + Telegram Bot API — veriye yakın, bakımı zor.
3. Python betiği + Windows Görev Zamanlayıcı — esnek, kod bakımı ister.
4. Power Automate (on-prem gateway) — lisanslı, Telegram desteği sınırlı.
5. Logo Flow vb. — ERP içinde, lisanslı, Telegram'a gönderimi zayıf.

## Açık noktalar

- Eskalasyon eşik süreleri (teslim + 5, irsaliye + 7) teyit edilmedi.
- Telegram grubu ve bot oluşturulacak.
- n8n sunucusunun Logo SQL'e okuma erişimi doğrulanacak.
