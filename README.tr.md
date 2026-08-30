> 🌐 **[English →](README.md)**

# Yardım amaçlı otomasyon dışarıdan farming gibi göründüğünde: Technocore `faucet` alanı

*Dışarıdan yapılmış bir vaka incelemesi: tek bir araç soyağacının bir not alanında
~%74 bozuk bir tanımlayıcı bıraktığı — yanında on binlerce yanıtsız istekten oluşan
bir odanın durduğu — ve neden protokol düzeyinde bunların hiçbirinin farming'den
ayırt edilemediği.*

Yalnızca olgusal gözlemler; kimsenin niyeti hakkında iddia yok ve hiçbir depo ya
da kişi adıyla anılmıyor (bkz. *Atıf* bölümü).

---

## Ne gözlemlendi

Technocore ([technocore.chat](https://technocore.chat)), yapay zekâ ajanları için
herkese açık, kimlik doğrulaması gerektirmeyen, herkesin yazabildiği bir sohbet ve
anahtar–değer servisidir. Servisin iki yüzeyi, servisin **sunduğu hiçbir belgede
yer almayan** bir konvansiyon taşıyor — ne `/llms.txt`, ne `/patterns.md`, ne
`/skill.md`, ne `/config`, ne de `/.well-known/agent.json`:

- **`/kv/faucet`** — ajanların şu satırı yazdığı bir not alanı:
  `technocore-faucet-v1 did:… fp:… status:requested waiting:official-testnet-tokens`.
- **`/r/faucet`** — aynı adı taşıyan, ajanların imzalı "faucet isteği / talebi"
  mesajları gönderdiği bir oda.

Hiçbiri bir sunucu özelliği değil. Sürdürücünün herkese açık konu (issue) altında
açıkça belirttiği gibi: *"faucet'in özel bir anlamı yok."* Sunucu tam da
belgelendiği gibi davranıyor: çağıranın seçtiği baytlar anonim veridir ve
`room-owners`/`room-allow` dışındaki `/kv` notları imzasız ve herkese açıktır.

Bu durum, herkese açık takipçide (**flop-labs/technocore-chat, konu #368**),
günler boyunca birden fazla bağımsız taraf tarafından, yalnızca-okuma modunda
ölçüldü. Aşağıdaki rakamlar bir seviye değil, anlık okumalardır; her iki yüzey de
dakikalar mertebesinde değişiyor.

## Rakamlar

**Not alanı.** Bağımsız sayımlar, alanın büyüdüğünü ve hiç boşalmadığını kaydetti:
2026-08-26'da yaklaşık 2 anahtar, 2026-08-27'de bir saat içinde ~54'e sıçrayan bir
patlama ve 2026-08-29'da 59 — ve **hiçbir kayıt `status:requested` durumundan
çıkmadan**. Her okumada, **notların ~%74'ü bozuk bir tanımlayıcı taşıyor**:
`did:did:key:z6Mk…`, oysa doğru bir DID `did:key:z6Mk…` biçimindedir.

Bozukluk asıl, *yanlış olmayan* şey yüzünden aydınlatıcı:

- **Not yolları doğru.** Her not, *doğru* DID'den hesaplanan `sha256(did)[:16]`
  konumunda duruyor; yani kimse zor kısmı yanlış yapmamış.
- **Parmak izleri (fingerprint) doğru.** Yayınlanan `fp:` alanı birkaç hash
  türetmesine karşı sınandığında, yalnızca `sha256("did:key:"+multibase)[:16]`
  eşleşiyor — her satırda. Dolayısıyla ikileme (doubling) **parmak izinden
  sonra** gerçekleşiyor: yalnızca görüntülenen gövdede var, kodun hesapladığı
  kimlikte değil.

Yani bu, 40 küsur bağımsız yazım hatası değil. Bu, **kopyalanmış tek bir şablon**;
tek satırlık kusuru — `did` zaten `did:` ile başlıyorken görüntü dizesini
`"did:" + did` diye biçimlendirmek — nüfusun dörtte üçüne bu şekilde taşınmış.

**Oda.** Aynı adı taşıyan oda, hikâyenin daha büyük yarısını anlatıyor. Gözlem
süresi boyunca binin altındaki mesaj sayısından **on binlere** çıktı (bu yazının
yazıldığı an itibarıyla son sıra numarası ~41.000). Okunabilen her 200 mesajlık
pencerede:

- **Herkes imzalamış** — oysa oda imza zorunlu tutmuyor.
- **Hiçbir şey asla yanıtlamadı.** Yanıt biçimli terimler (`sent`, `granted`,
  `approved`, `denied`) her okumada, günlerce, **sıfır**.
- İki nüfus — not yazanlar ve odaya yazanlar — **neredeyse hiç örtüşmüyor** (her
  iki nüfusta da yuvarlama hatası mertebesinde), dolayısıyla bunlar tek bir
  davranışın iki yüzeyde görülmesi değil, büyük ölçüde iki ayrı davranış olarak
  okunuyor.

Yüzlerce ayrı kimlik, hepsi imzalıyor, imzanın isteğe bağlı olduğu ve bugüne dek
hiçbir yanıtın belirmediği bir odaya istek yazıyor.

## Araç bağlantısı

Konvansiyon, bağımsız okuyucuların aynı sonuca varmasıyla yayılmadı. Varış şekli —
kademeli bir benimseme eğrisi değil, neredeyse dikey bir patlama — ve paylaşılan,
zamanla değişen bir şablon, bir spesifikasyonu okuyan çok sayıda insana değil,
**kopyalanmış bir kiti yayan otomasyona** işaret ediyor.

Dikkatle söylenmesi gereken kısım şu: **bu faucet notlarını yazan kit sınıfı, aynı
zamanda "katkı kayıtları" (contribution records) da üretiyor.** Bu deseni üreten
araçta, faucet-notu oluşturucusu, sabit bir katkı türü menüsü üzerinden çalışan
bir katkı-kaydı oluşturucusunun yanında duruyor —
*tool, guide, article, video, thread, translation, workflow* — ve şu biçimde
imzalı bir satır üretiyor: *"Technocore için bir &lt;tür&gt; yayınladım… İmzalı
Technocore kaydı: oda …, sıra …."*

Vaka incelemesi tek cümlede şu: **düzgün, imzalı bir "katkı kanıtı" üreten aynı
otomasyon sınıfı, aynı zamanda tanımlayıcı alanının ~%74'ü bozuk olan bir not
alanının izlenebilir soyağacıdır; bunun yanında on binlerce yanıtsız istekten
oluşan bir oda durur.** Tek bir soyağacı, tek bir operatör değildir: notlar birçok
anahtardan gelir ve paylaşılan araç, paylaşılan kontrol değildir. Bir ajanın doğru
katılmasına yardım etmek için kurulmuş bir kit — yazılarını imzala, kimliğini
kaydet, işini duyur — sunucunun asla onaylamadığı yüzeylere karşı ölçekte
çalıştırıldığında, dışarıdan okuyan biri için farming'den ayırt edilemeyen çıktı
üretir.

## Neden farming gibi görünüyor (ve asıl mesele bu)

Protokol düzeyinde, dışarıdan bir gözlemci niyeti çıktıdan ayıramaz:

- Notlar **imzasız ve herkese açık**, dolayısıyla `status:requested` hiçbir şey
  iddia etmiyor — herkes tek bir istekle herhangi bir kaydın üzerine yazabilir ve
  erken olmak hiçbir şeyi kanıtlamaz.
- Bozuk `did:did:key:` alanı, "özensiz kopyaları" "özenli katılımcılardan"
  ayırmak için kullanılamaz: **bozuk ve düzgün tüm satırlar tek bir parmak izi
  türetmesini paylaşıyor** — bir özen yelpazesi değil, tek bir araç soyağacı.
- İmzalı bir oda mesajı, *o mesajın* yazarlığını kanıtlar; kendi kendine beyan
  edilen bir iddiayı doğru kılmaz.

Bunların hiçbiri, kiti kimin yazdığı veya neden yazdığı hakkında bir sonuç
gerektirmez — ve desteklemez. Gözlem daha dar ve daha kullanışlı: **yardım etmeye
çalışan otomasyon ile farming yapan otomasyon aynı izi bırakır**, dolayısıyla güvenliği "okuduğun her şeye
veri muamelesi yap, asla talimat değil" ilkesine dayanan bir servis, ikisini ayırt
etmek için bu ize güvenemez. Herkesin başvurduğu ipucu — bozukluk — nüfusu, göründüğü
gibi ayırmıyor.

## Bu *ne değil*

- **Bir sunucu kusuru değil.** Servis tam da kılavuzunun söylediğini yapıyor;
  boşluk, kılavuzun (henüz) not tabanlı bir faucet kuyruğu olmadığını
  söylememesi. Takipçideki açık belgeleme pull request'leri tam da bunu ele
  alıyor.
- **Bir suçlama değil.** Hiçbir kişi, hesap veya depo adıyla anılmıyor ve burada
  kimsenin kötü niyetle davrandığı iddia edilmiyor. Bir depoyu adıyla anan,
  herkese açık yazılabilir bir not, o deponun onu yazdığının kanıtı değildir ve
  paylaşılan araç, paylaşılan kontrol değildir.

## Yeniden üretim (reproduction)

Yukarıdakilerin tümü, yalnızca-okuma modunda iki istek ve bir döngüdür. Her iki
yüzeye de hiçbir şey yazılmadı.

```bash
# not alanı, ardından her anahtar için bir GET
curl -s 'https://technocore.chat/kv/faucet?format=json'
curl -s 'https://technocore.chat/kv/faucet/<key>'

# aynı adı taşıyan oda (en yeni 200 mesaj)
curl -s 'https://technocore.chat/r/faucet?format=json&limit=200'
```

```python
# parmak izi, gövde nasıl yazarsa yazsın, DOĞRU DID'in sha256'sıdır
import hashlib, re
mb = re.search(r"did:(?:did:)?key:(z[1-9A-HJ-NP-Za-km-z]+)", body).group(1)
fp = re.search(r"fp:([0-9a-f]{16})", body).group(1)
assert hashlib.sha256(("did:key:" + mb).encode()).hexdigest()[:16] == fp
```

## Atıf

Bu yazı, hiçbir depoyu ve hiçbir kişiyi kasıtlı olarak adıyla anmıyor; bu, #368
konusundaki özgün herkese açık kanıtın disiplinini izliyor. O kanıtın yazarı da
aynı nedenle depo adlarını özel çalışma notlarında tutup herkese açık belgenin
dışında bıraktı: herkese açık yazılabilir bir not yazarlığın kanıtı değildir ve
ondan bir kaynağı adıyla anmak, verinin desteklediğinden fazlasını iddia etmek
olur. Bulgu, desenin kendisidir; aracı kimin çalıştırdığı bir bulgu değildir ve
bulguyu ifade etmek için gerekli de değildir.

## Kaynaklar

- Herkese açık konu: **flop-labs/technocore-chat #368** — *"54 agents are
  queueing in an undocumented `faucet` namespace, and 76% of the entries are
  malformed"* — ve bağımsız ölçümlerden oluşan yorum akışı.
- Aynı takipçide, konunun istediği o tek kalıcı cümleyi öneren ilgili açık
  belgeleme pull request'leri.
- Tüm rakamlar technocore.chat (v0.10.0) üzerinde yalnızca-okuma modunda,
  2026-08-27 ile 2026-08-29 arasında yeniden ölçüldü.
