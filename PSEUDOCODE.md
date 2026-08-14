# IARS sözde kodu

Aşağıdaki sözde kod, doktora tezinin **§3.3.4 "IARS bileşen görevleri ve teknik
ayrıntılar"** alt bölümündeki **Algoritma 1**'den aktarılmıştır. Gövde metni birebirdir;
yalnızca algoritma başlığındaki "–BID" eki çıkarılmıştır (tezdeki başlık
"IARS: irace–ADAM–SA–BID Hibrit Çizelgeleme Yaklaşımı" biçimindedir).

- **Tez:** Uygunluk ve Kapasite Kısıtlı İlişkisiz Paralel Makine Çizelgeleme Problemi
  için Zeki ve Hibrit Bir Model Önerisi — Elif Yıldırım, T.C. Sakarya Üniversitesi,
  Fen Bilimleri Enstitüsü.
- **Tez danışmanı:** Doç. Dr. Berrin Denizhan.
- **Problem:** $R_m \mid M_p, \mathrm{cap}_m \mid C_{\max}$ — ilişkisiz paralel makine
  çizelgeleme problemi (UPMSP), uygunluk ve kapasite kısıtlarıyla.
- **Notasyon:** Semboller tezin §3.1 notasyon alt bölümünde ve Tablo 3.1'de
  tanımlanmıştır. Parantez içindeki denklem numaraları tezin kendi numaralandırmasına
  aittir.

---

## Algoritma 1. IARS: irace–ADAM–SA Hibrit Çizelgeleme Yaklaşımı – Sözde Kod

```text
Girdi:   P (ürünler), M (karıştırıcılar), dₚ, cₘ, tₚₘ, Eₚₘ; irace seçkin yapılandırması Θ.
Çıktı:   Uygulanabilir x*; Cₘₐₓ(x*); CPU süresi.

01:  // Faz 1; irace ile Θ yapılandırması (önceden çalıştırılmış)
02:  // Faz 2; ADAM sürekli gevşetmesi
03:  θ ← Eₚₘ maskesi altında küçük rastgele ham atama skorlarıyla başlat
04:  for t = 1 .. IADAM do
05:      gₜ ← ∇θ F(θ); mₜ, vₜ moment güncellemeleri (4.9–4.11)
06:      θ ← θ − αA · m̂ₜ / (√v̂ₜ + ε)
07:  end for
08:  x ← argmaxₘ∈Mₚ πₚₘ(θ)   // ayrık izdüşüm (3.19)
09:  // Faz 3a; Uygunluk öncelikli kapasite onarımı
10:  while ∃ m: Uₘ(x) > cₘ do
11:      ihlalli karıştırıcıdaki en yüksek dₚ olan ürünü, uygunluk içinde en az ek yük
         artıran karıştırıcıya taşı
12:  end while
13:  // Faz 3b; Tavlama benzetimi (SA) iyileştirme
14:  T ← T₀; x̂ ← x (uygulanabilirse)
15:  for k = 1 .. ISA do
16:      podak olasılıkla darboğaz karıştırıcısını hedefleyen 1-opt veya takas;
         aksi hâlde kapasite-farkındalıklı çeşitlendirme → x′
17:      Δ ← F̂(x′) − F̂(x)
18:      eğer Δ ≤ 0 veya U(0,1) < exp(−Δ/Tₖ) ise x ← x′
19:      eğer x uygulanabilir ve Cₘₐₓ(x) < Cₘₐₓ(x̂) ise x̂ ← x
20:      Tₖ₊₁ ← αsoğuma · Tₖ; 500 yineleme boyunca iyileşme yoksa T ← T₀/2
21:  end for
22:  // Faz 3c; Darboğaz odaklı 1-opt iniş (BID)
23:  m* ← argmaxₘ Lₘ(x̂)
24:  while m* üzerindeki ürünler için iyileştiren komşu varsa: en iyi iyileştireni
         kabul et; m* yeniden hesapla
25:  döndür x̂, Cₘₐₓ(x̂), CPU süresi
```

> Satır 11, 16 ve 24 tezde tek satır hâlindedir; burada yalnızca satır genişliği
> nedeniyle sarmalanmış, metin değiştirilmemiştir.

---

## Fazların özeti

| Faz | Satır | İşlev |
|---|---|---|
| 1 | 01 | irace ile çevrim dışı hiperparametre yapılandırması (Θ) |
| 2 | 02–08 | ADAM sürekli gevşetmesi ve ayrık izdüşüm |
| 3a | 09–12 | Uygunluk öncelikli kapasite onarımı |
| 3b | 13–21 | Tavlama benzetimi (SA) ile iyileştirme |
| 3c | 22–25 | Darboğaz odaklı en iyi iyileştiren 1-opt iniş (BID) |

**Önerme 3.1 (BID yerel-optimum değişmezi).** Tezde, IARS'ın ürettiği çizelgenin yükü
en yüksek olan darboğaz karıştırıcısı $m^* = \arg\max_m L_m(x)$ çevresinde 1-opt
optimal olduğu gösterilmektedir; ispat §3.3.3'tedir.
