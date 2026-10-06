# Wolvox T-Soft Entegrasyon

T-Soft altyapılı e-ticaret mağazanız ile AKINSOFT Wolvox ERP arasında siparişleri, müşterileri, adresleri, tahsilatları,
ürünleri, fiyatları ve stokları aktaran entegrasyon yazılımı. Mağazadaki siparişler ERP'ye sipariş, müşteriler cari kart
olarak yazılır; ERP'deki ürün, fiyat ve stok bilgileri mağazaya gönderilir.

Bu depo ürün tanıtımı içindir; yazılımın kaynak kodu paylaşılmaz.

## Ürün hakkında

Wolvox T-Soft Entegrasyon, mağaza ile ERP arasındaki elle veri girişini büyük ölçüde azaltmak için geliştirildi. Windows
servisi olarak çalışır, ERP'ye WolvoxApi üzerinden bağlanır ve tarayıcıdan açılan Türkçe bir yönetim paneliyle yönetilir.
Hangi siparişlerin aktarılacağını, mağazaya hangi fiyatın gideceğini, ödemelerin, kargonun ve iptallerin nasıl işleneceğini
panelden siz belirlersiniz.

Entegrasyon temkinli varsayılanlarla gelir. Aktarılacak sipariş durumları ve başlangıç tarihi seçilmeden hiçbir sipariş
aktarılmaz. Siz ayarları değiştirene kadar her turda ERP'ye en fazla bir kayıt yazılır ve stok gönderiminde yalnızca bir
ürün gönderilir. Tahsilat, iptal, kargo takip numarası ve fatura bilgisi gibi özellikler siz açana kadar kapalı kalır. Cari,
adres, sipariş ve stok işlerinin her birinde, hiçbir şey yazmadan sonucu önceden gösteren bir deneme turu vardır.

Entegrasyonun çalışması için lisanslı ve çalışır durumda bir WolvoxApi kurulumu gerekir. WolvoxApi hakkında bilgi:
[Wolvox-REST-API](https://github.com/DijitalKobi/Wolvox-REST-API)

## Kimler için

**E-ticaret mağaza sahipleri** — T-Soft altyapısıyla satış yapan ve muhasebesini Wolvox ERP'de tutan işletmeler.

**Muhasebe ekipleri** — Siparişleri, carileri ve tahsilatları ERP'ye elle girmek yerine denetlenmiş ve hazır biçimde bulmak
isteyen ekipler.

**E-ticaret yöneticileri** — Ürün, fiyat ve stok bilgisini tek kaynaktan, ERP'den yönetmek isteyenler.

**Danışmanlar ve uygulama ekipleri** — Müşterilerinin T-Soft mağazasını ve Wolvox ERP'sini birbirine bağlamak isteyen
firmalar.

## Nasıl çalışır

```
T-Soft mağazası  <-->  Wolvox T-Soft Entegrasyon  <-->  WolvoxApi  <-->  AKINSOFT Wolvox ERP
```

ERP ile tüm veri alışverişi WolvoxApi üzerinden yapılır. Bir siparişin mağazadan ERP'ye yolculuğu şöyledir:

1. **Mağazada sipariş oluşur.** Sipariş, aktarım için seçtiğiniz durumlardan birine geldiğinde entegrasyonun kapsamına
   girer.
2. **Sipariş okunur ve denetlenir.** Müşteri, ürünler, tutarlar, ödeme türü ve kargo bilgisi kontrol edilir. Eksik ya da
   tutarsız bir bilgi varsa sipariş yazılmaz ve nedeni panelde belirtilerek bekletilir.
3. **Müşteri hazırlanır.** Müşterinin ERP'de carisi yoksa açılır, bilgileri değişmişse güncellenir; adresleri cari adres
   defterine eklenir. Yeni açılan carinin siparişi bir sonraki turda yazılır.
4. **Sipariş ERP'ye yazılır.** Kayıt WolvoxApi üzerinden ERP'ye gönderilir ve ERP'den geri okunarak doğrulanır.
5. **Mağazada işaretlenir.** Sipariş mağazada "aktarıldı" olarak işaretlenir ve bir daha alınmaz.
6. **Sonraki adımlar işlenir.** Açtığınız özelliklere göre tahsilat, kargo takip numarası, iptal ve e-Fatura ya da e-Arşiv
   olarak gönderilen faturanın bilgisi aynı turda işlenir.

Ters yönde, panelden başlatılan Stok Gönder işi ERP'deki stok kartlarını WolvoxApi üzerinden okur; ürün, fiyat, stok
miktarı, varyant, resim, kategori ve marka bilgilerini mağazaya gönderir. Gönderilen ürünün fiyatı, KDV'si ve kategorisi
mağazadan geri okunarak kontrol edilir.

## Sipariş aktarımı

### Siparişlerin seçimi
Mağazada seçtiğiniz durumlardaki ve belirlediğiniz başlangıç tarihinden sonra verilmiş siparişler ERP'ye sipariş olarak
yazılır. Yazılan her sipariş ERP'den geri okunarak doğrulanır, sonra mağazada "aktarıldı" olarak işaretlenir. Bir turda
ERP'ye en fazla kaç kayıt yazılacağını siz belirlersiniz; siparişle birlikte açılan yeni cari de bu sayıya dâhildir.
Varsayılan değer 1, üst sınır 500'dür.

### Ürün satırları ve stok eşleşmesi
Sipariş satırındaki ürün kodu, ERP'deki stok koduyla eşleştirilir. ERP'de kartı bulunmayan bir ürün içeren sipariş
varsayılan olarak bekletilir. İsterseniz bu satırlar ürün adıyla, stok kartına bağlanmadan yazılabilir; bu durumda satır
kart bazlı stok hareketlerinde ve raporlarda görünmez.

### İndirimler ve KDV
Ürün, kampanya ve kupon indirimleri ERP siparişine iskonto olarak yazılır. Satırlara ayrıştırılamayan indirim satır
fiyatlarına yansıtılır ve bu durum canlı akışta belirtilir. Fiyatlar varsayılan olarak KDV hariç yazılır ve KDV'yi ERP
hesaplar; ayarla KDV dâhil yazım seçilebilir. KDV dâhil yazımla mağazanın tahsil ettiği tutar kuruşu kuruşuna
sağlanamıyorsa o sipariş KDV hariç yazılır.

### Tutar denetimi
Mağazanın sipariş toplamı, ERP'nin hesaplayacağı toplamla her siparişte karşılaştırılır. İndirimli siparişlerde oluşan
kuruş farkı iskonto tutarı ayarlanarak kapatılır. Fark küçük yuvarlama payını aşarsa sipariş yazılmaz ve nedeni
belirtilerek bekletilir.

### Kargo, hizmet bedeli ve hediye paketi
Kargo ücreti, kapıda ödeme ücreti, kredi kartı vade farkı ve hediye paketi ücreti siparişe ayrı satırlar olarak yazılır.
Bu satırlarda ayarlarda belirlediğiniz stok kodları kullanılır; kapıda ödeme ücreti ve kredi kartı vade farkı için ayrı
hizmet stok kodu tanımlanabilir. Stok kodu girilmezse ya da ERP'de bulunmazsa satır, adıyla ve stok kartına bağlanmadan
yazılır. KDV oranı stok kartından, kart yoksa ayarlardan alınır.

### Sipariş bilgileri ve e-Arşiv alanları
Teslimat adresi siparişin sevk bilgilerine yazılır. Mağaza sipariş numarası ve ödeme bilgisi ek bilgi alanlarına aktarılır.
Sipariş notu, ürün notu ve hediye notu açıklama alanına yazılır. Sipariş e-ticaret satışı olarak işaretlenir. e-Arşiv için
site adı, ödeme türü, kargo firması ve taşıyıcı vergi numarası da siparişe eklenir.

### Ödeme türü
Mağazanın her ödeme seçeneği, panelden Wolvox'taki bir ödeme türüyle eşleştirilir ve siparişe bu tür yazılır.
Eşleştirilmemiş bir ödeme seçeneğinde ödeme türü boş kalır ve bu durum canlı akışta belirtilir.

### Depo, sipariş durumu ve muhasebe kodları
Sipariş satırları seçtiğiniz depoya ya da ürünün stok kartındaki geçerli depoya yazılır. Depo listesi ERP'den gelir; ERP'de
depo kullanımı kapalıysa siparişe depo yazılmaz. Siparişin ERP'deki durumu Wolvox sistem ayarlarındaki "Alınan Sipariş"
durumundan okunur ya da listeden elle seçilir. Stok kartına bağlı olmayan ürün, kargo, hizmet ve hediye paketi satırları
için muhasebe kodları tanımlanabilir.

### Yurt dışı siparişler
Teslimat ülkesi ayarlardaki ülkeden farklı olan sipariş, ERP'de yurt dışı sipariş türüyle açılır. Bu siparişin carisine
ayarlarda belirlediğiniz yurt dışı vergi numarası yazılır.

### Cariden ödeme ve limit denetimi
"Cariden Ödeme" ile verilen siparişte ERP'deki vadesi geçmiş borç ve carinin açık hesap limiti denetlenir. Limit aşılıyorsa
sipariş bekletilir ve limit uygun hâle geldiğinde sonraki turda aktarılır. İsterseniz siparişin aktarılıp yalnızca uyarı
verilmesi seçilebilir.

## Müşteri ve adres

### Cari açma ve güncelleme
Her mağaza üyesine ERP'de tek bir cari karşılık gelir; cari kodu, üyenin mağazadaki web servis kodudur. Sipariş aktarılırken
müşterinin carisi yoksa açılır, bilgileri değişmişse güncellenir. Cari Al işi aynı işlemi tüm üyeler için ayrıca çalıştırır
ve cari alanlarını panelde yaptığınız alan eşleştirmesine göre yazar. Sipariş sırasında açılan cariler standart alan
eşlemesiyle yazılır.

### Vergi numarası ve TC kimlik numarası
Kimlik bilgisi sırasıyla siparişin fatura adresinden, üyenin varsayılan fatura adresinden ve üyelik bilgisinden alınır.
Vergi numarası varsa TC kimlik numarası boş bırakılır. Kimlik bilgisi olmayan bireysel müşteride ayarlardaki varsayılan TC
kimlik numarası kullanılır; bu ayar boşaltılırsa böyle müşterilerin carisi açılmaz ve sipariş bekletilir.

### Cari grubu, muhasebe kodu ve döviz bilgisi
Cari Al işinde üyenin mağazadaki müşteri grubu, ERP'de aynı adla tanımlı cari grubuna yazılır; ERP'de tanımı olmayan grup
yazılmaz ve uyarı verilir. Sipariş sırasında açılan yeni carilere ayarlarda seçtiğiniz varsayılan cari grubu ve muhasebe
kodu kalıbı verilir. Yeni açılan carinin döviz bilgileri Wolvox'un genel ayarlarına göre doldurulur; var olan carinin döviz
hesabına dokunulmaz.

### Adres aktarımı
Üyenin mağazadaki adresleri ERP'deki cari adres defterine eklenir ve değişen adresler güncellenir. Hiçbir adres silinmez.
Sipariş aktarılırken o müşterinin adresleri de aktarılır; Adres Al işi aynı işlemi tüm üyeler için ayrıca çalıştırır.

## Ödeme ve tahsilat

### Otomatik tahsilat
Havale/EFT ve kredi kartı tahsilatı tek bir "Siparişteki ödemeleri otomatik işle" ayarıyla açılır ve bu ayar varsayılan
olarak kapalıdır. Her ödeme türünde, yalnızca o türün eşleştirmesi tamamlandıktan sonra entegrasyonun ERP'ye yazdığı TL
siparişleri kapsar; ERP'de önceden var olan siparişlere tahsilat yazılmaz. Tahsilat her sipariş için bir kez yazılır.

### Havale ve EFT
Havale ya da EFT ile ödenen sipariş, mağazada seçtiğiniz bir duruma geçtiğinde genel toplamıyla cariye dekont olarak yazılır.
ERP'deki banka hesapları, mağazanın havale hesaplarıyla panelden eşleştirilir.

### Kredi kartı
Kartla ödenen sipariş, seçtiğiniz bir duruma geçtiğinde vade farkı dâhil tutarıyla cariye POS tahsilatı olarak yazılır.
ERP'deki her sanal POS tanımının karşısına, T-Soft Taksit Yönetimi'nde görünen POS adı ve taksit sayısı yazılarak eşleştirme
yapılır. Eşleşmeyen POS ya da taksitte tahsilat yazılmaz.

### Kapıda ödeme
Mağazanın her kargo firmasına ERP'de bir cari atanır. Kapıda ödemeli sipariş yazılırken, tahsilatı yapacak kargo firmasının
carisi virman carisi olarak belirtilir; sipariş ERP'de faturalandırıldığında müşterinin borcu bu kargo firmasının carisine
aktarılır. Eşleşme bulunamazsa sipariş bekletilir; isterseniz bu siparişlerin virmansız aktarılması seçilebilir. Kapıda
ödemeli siparişlere otomatik tahsilat yazılmaz.

### Tahsilatın iptali
Mağazada iptal edilip ERP'de de iptal edilen siparişin, entegrasyonun yazdığı tahsilatı da iptal edilir.

## Kargo, iptal ve fatura bilgisi

### Kargo takip numarası
Mağazada kargo takip numarası oluştuğunda ERP siparişinin kargo numarası, kargo firması ve e-Arşiv taşıyıcı bilgisi
güncellenir. ERP'de elle farklı bir numara girilmişse üzerine yazılmaz. Özellik varsayılan olarak kapalıdır.

### Sipariş iptali
ERP'ye aktarılmış bir sipariş mağazada iptal saydığınız bir duruma geçerse, ERP'de seçtiğiniz iptal durumuna çekilir. Kayıt
silinmez. İrsaliyesi ya da faturası kesilmiş siparişe dokunulmaz ve uyarı verilir. İptal sayılacak durumlar seçilmediği
sürece özellik kapalıdır.

### Fatura bilgisinin mağazaya yazılması
Aktarılan siparişin faturası ERP'de e-Fatura ya da e-Arşiv olarak gönderildiğinde, faturanın numarası, tarihi ve ETTN'si
mağazadaki siparişe yazılır. Bunun için faturanın ERP'de entegrasyonun yazdığı siparişten dönüştürülerek kesilmesi gerekir.
ERP'de etkin e-Fatura entegratörü EDM, izibiz ya da Digital Planet ise faturanın görüntüleme bağlantısı da eklenir. Bu
bilgi müşterinin sipariş sayfasında görünebilir ve gönderildikten sonra geri alınamaz. Her fatura bir kez gönderilir ve son
30 günün faturalarına bakılır. Özellik varsayılan olarak kapalıdır.

## Ürün, fiyat ve stok gönderimi

### Ürün gönderimi
ERP'de aktif ve webde görünür olarak işaretli stok kartları, panelden başlatılan Stok Gönder işiyle mağazada ürün olarak
açılır. Ürün adı, e-ticaret açıklaması, barkod, birim ve KDV oranı gönderilir; mağazadaki ürün kodu ERP stok koduyla
aynıdır. Gönderilen ürünün fiyatı, KDV'si ve kategorisi mağazadan geri okunarak kontrol edilir.

### Gönderim koşulları
Bir ürünün gönderilebilmesi için stok grubunun mağazada kategori olarak bulunması gerekir; bunun için önce Kategorileri Aç
işi çalıştırılır. Satış fiyatı ve KDV oranı girilmiş, stok kodu en fazla 30 karakter olmalıdır. Fiyatı sıfır olan ürünler
varsayılan olarak gönderilmez. Gönderilmeyen her ürün, nedeniyle birlikte deneme turunda listelenir.

### Mağazadaki ürünlerin güncellenmesi
Mağazada zaten bulunan ürünlerin (entegrasyonun daha önce açtıkları da dâhil) fiyatı, stok miktarı, resimleri, ek
kategorileri ve varyantları yalnızca "Mağazada zaten olan ürünleri güncelle" ayarı açıkken güncellenir; güncellemede yalnızca
değişen alanlar yazılır. Bu ayar varsayılan olarak kapalıdır; stok ve fiyatın düzenli eşitlenmesi için ilk kontrolden sonra
açın. Mağaza panelinde elle düzenlenmiş ürünler isteğe bağlı olarak atlanır.

### İlk ürün kontrolü
Varsayılan olarak her turda yalnızca bir ürün gönderilir ve tur durur. Ürünü mağazada kontrol ettikten sonra bu ayarı
kapatırsınız.

### Fiyatlar
Stok kartındaki dört satış fiyatından hangisinin gönderileceğini siz seçersiniz. ERP fiyatının ve mağaza fiyatının KDV
dâhil olup olmadığı ayrı ayrı belirtilir. Kartında döviz tanımı olan ürünlerde döviz fiyatı gönderilebilir. İndirimli fiyat,
"İndirimli Göster" işareti ve alış fiyatı da ayarla gönderilir.

### Stok miktarları
Ürünün kullanılabilir stok miktarı mağazaya yazılır. Varsayılan olarak tüm depoların toplamı gönderilir; isterseniz yalnızca
seçtiğiniz deponun miktarı gönderilir. Eksiye düşmüş stok mağazaya sıfır olarak yazılır. Stok Gönder işi panelden başlatılır.

### Varyantlar
ERP'de renk ve beden kartları bulunan ürünler mağazada alt ürün olarak açılır; fiyat, barkod ve stok her varyant için ayrı
yazılır. Bunun için mağaza panelinde Renk ve Beden özellik gruplarının tanımlı olması gerekir. Özellik varsayılan olarak
kapalıdır.

### Ürün resimleri
Stok kartına eklenen resimler mağazaya yüklenir ve ana görsel ilk sırada yer alır. Resim yalnızca ERP'de değiştiğinde yeniden
yüklenir. Entegrasyonun yüklediği bir resim ERP'den silinirse mağazadan da silinir. Varyant resimleri seçtiğiniz niteliğe,
genellikle renge, bağlanır. Özellik varsayılan olarak kapalıdır.

### Kategori, marka ve model
ERP stok grubu mağazada ana kategori olur. Stok kartındaki kategori bilgileri isteğe bağlı olarak ek kategori olarak
işaretlenir ve ERP'den kaldırılan atama mağazada da kaldırılır. Mağazada bulunmayan kategoriler Kategorileri Aç, markalar ve
modeller Marka/Model Aç işiyle açılır. Ürün gönderilirken ilgili kategori, marka ve modele bağlanır.

### Menşei, yeni ürün işareti ve yayın durumu
Stok kartındaki üretim yeri, ERP'nin ülke tanımlarıyla ülke koduna çevrilir ve mağazaya menşei olarak yazılır. Kayıt tarihi
belirlediğiniz gün sayısı içinde olan ürünler mağazada "yeni ürün" olarak işaretlenir ve süresi dolanların işareti kaldırılır;
bu işaret varsayılan olarak kapalıdır. Entegrasyonun mağazaya açtığı ya da güncellediği bir ürün ERP'de pasif yapılırsa ya da
webde görünmesi kapatılırsa mağazada yayından kaldırılır. Mağaza panelinden yayından kaldırdığınız ürün, entegrasyon
tarafından yeniden açılmaz.

## Eşleştirmeler

### Alan eşleştirme
Cari, cari adresi ve ürün için ERP alanları ile mağaza alanları panelden eşleştirilir. Mağazada karşılığı olmayan bir ERP
alanına sabit değer verilebilir. Kilitlenen satır değiştirilemez ve silinemez. ERP stok kartındaki özel tanımlar da mağaza
alanlarına bağlanabilir, örneğin desi. Alan eşleştirme ekranlarında ERP solda, mağaza sağda yer alır.

### Ödeme, banka ve kargo eşleştirmeleri
Panelden yapılan eşleştirmeler şunlardır:

**Ödeme seçenekleri** — Mağazanın ödeme seçenekleri ile Wolvox ödeme türleri.

**Havale hesapları** — Mağazanın havale hesapları ile ERP banka hesapları.

**POS ve taksit** — ERP sanal POS tanımları ile T-Soft'taki POS adı ve taksit sayısı.

**Kargo firmaları** — Kapıda ödeme için kargo firmaları ile ERP carileri.

Bu eşleştirmeler tahsilatın ve e-Arşiv bilgilerinin doğru yazılmasını sağlar. Kargo firmalarının taşıyıcı vergi numaraları
da ayarlardan girilir.

## Çalıştırma ve izleme

### Otomatik çalıştırma
"Otomatik çalıştır" ayarı açıldığında Sipariş Al işi belirlediğiniz aralıkla kendiliğinden çalışır. Aralık 2 ile 1440 dakika
arasında seçilir, varsayılan değer 15 dakikadır. Tahsilat, kargo takip numarası, iptal ve fatura bilgisi adımları bu turun
içindedir. Ayar varsayılan olarak kapalıdır. Cari Al, Adres Al, Stok Gönder, Kategorileri Aç ve Marka/Model Aç işleri
panelden başlatılır.

### Elle çalıştırma
Her iş panelden tek düğmeyle başlatılabilir ve tek tek açılıp kapatılabilir. Sipariş işinde üç ek düğme vardır: yalnızca tek
bir sipariş yazan düğme, yalnızca tek bir cari açan düğme ve ERP'ye yazılmış ama mağazada henüz "aktarıldı" olarak
işaretlenmemiş siparişleri ERP'ye dokunmadan işaretleyen düğme.

### Deneme turu
Cari Al, Adres Al, Sipariş Al ve Stok Gönder işlerinin her birinde, panelde "kuru tur" adıyla görünen bir deneme turu düğmesi
vardır. Deneme turu gerçek turla aynı kararları verir ama ERP'ye de mağazaya da hiçbir şey yazmaz; hangi kaydın neden
yazılacağını ya da bekletileceğini satır satır gösterir. Kategorileri Aç ve Marka/Model Aç işlerinin deneme turu yoktur ve bu
işlerin mağazada açtığı kayıtlar geri alınamaz.

### Hata yönetimi
Hatalar kalıcı, geçici ve sonucu belirsiz olarak ayrılır. Okuma isteklerinde kısa bağlantı kesintileri birkaç kez otomatik
olarak yeniden denenir. Yazma istekleri, mükerrer kayıt oluşmaması için hiçbir zaman kendiliğinden yeniden gönderilmez;
yazılamayan kayıt bir sonraki turda yeniden ele alınır ya da İşlenemeyen Kayıtlar ekranında sizi bekler. ERP'nin reddettiği
sipariş ve cari açma kayıtları ile mağazaya yazılamayan ürünler bu ekrana düşer ve siz "Yeniden dene" diyene kadar tekrar
gönderilmez; hatanın yanında hata kodunun anlamı da gösterilir. Ürün sonraki turda başarıyla gönderilince kaydı kendiliğinden
kapanır. Cari Al, Adres Al, tahsilat, kargo takip numarası, iptal ve fatura bilgisi hataları canlı akışta ve Denetim ekranında
görünür.

### Sonucu belirsiz yazmalar
Bağlantının yazma sırasında kopması gibi durumlarda bir siparişin ERP'ye yazılıp yazılmadığı kesinleşmeyebilir. Böyle bir
yazma, mükerrer kayıt oluşmaması için tekrar gönderilmez ve sipariş İşlenemeyen Kayıtlar ekranında listelenir. Siparişi
ERP'de kontrol ettikten sonra "ERP'de var — yazıldı say" düğmesine basarsınız: düğme ERP'ye bir şey yazmaz, sistem siparişi
o anda ERP'de arar, bulursa kaydı kapatır ve sipariş bir sonraki turda mağazada "aktarıldı" olarak işaretlenir. Sipariş
ERP'de yoksa önce ERP'ye aynı sipariş numarasıyla elle girilir, ardından aynı düğmeye basılır.

### Mükerrer kayıt önleme
Aynı sipariş ERP'ye ikinci kez yazılmaz. Bunun için üç kontrol birlikte çalışır: entegrasyonun kendi kayıt defteri, ERP'de
sipariş numarasıyla yapılan arama ve yazmadan sonraki geri okuma. Tahsilat, kargo numarası ve fatura bilgisi de her sipariş
için yalnızca bir kez gönderilir.

### Acil durdurma
Panelden tek düğmeyle tüm aktarım işleri durdurulur. Acil durdurma her yazmadan hemen önce yeniden kontrol edildiği için
çalışan tur yarıda kesilir. Yazılmamış kayıtlar silinmez; acil durdurma kaldırıldıktan sonraki turda ele alınır. Acil
durdurma açıkken Durum ekranının en üstünde kırmızı bir uyarı görünür.

### Canlı akış, günlük ve denetim izi
Çalışan işin her adımı panelde canlı akış olarak anlık izlenir ve günlük dosyasına yazılır. Canlı akış ve günlük dosyaları
14 gün saklanır. Denetim ekranı, tetiklenen işleri ve yazma sonuçlarını listeler.

### Bağlantı sağlığı
Mağaza ve WolvoxApi bağlantısı düzenli aralıklarla denetlenir ve panelde gösterilir. Ayarlar ekranındaki test düğmesi, iki
tarafa veri yazmadan gerçek bir çağrı yaparak bağlantıyı sınar.

## Güvenlik ve işletim

### Şirket, çalışma yılı ve şube
Kurulumda ERP şirketi ve çalışma yılı seçilir. Şube sistemi kullanan şirketlerde şube de seçilir. Şubeli şirkette sipariş,
cari ve adres seçilen şubeye yazılır ve şube seçilmeden bu işler başlamaz. Şube sonradan Ayarlar ekranından değiştirilebilir.

### Güvenlik
Mağaza kullanıcı bilgileri ve WolvoxApi anahtarı Windows'un şifreli deposunda saklanır. Kaydedilen değerler panelden geri
okunamaz, yalnızca tanımlı olup olmadıkları görünür. Panel yönetici parolasıyla korunur ve art arda beş hatalı denemede hesap
15 dakika kilitlenir. Panel yalnızca entegrasyonun kurulu olduğu bilgisayardan açılır. Entegrasyon ERP'ye yalnızca WolvoxApi
üzerinden erişir.

### Windows servisi
Entegrasyon Windows servisi olarak çalışır. Başlangıç türü otomatik seçildiğinde bilgisayar açılınca kendiliğinden başlar.
Servis, panelin Servis ekranından kurulabilir, durdurulabilir ve kaldırılabilir; başlangıç türü de aynı ekrandan değiştirilir.

## Aktarılan veriler

| Veri | Yön | Açıklama |
|---|---|---|
| Sipariş | Mağaza → ERP | Seçilen durumlardaki siparişler; ürün, kargo, hizmet ve hediye paketi satırları, indirimler, notlar, teslimat adresi, ödeme ve e-Arşiv bilgileri |
| Müşteri (cari) | Mağaza → ERP | Üye bilgileri, vergi numarası ya da TC kimlik numarası; cari yoksa açılır, değiştiyse güncellenir |
| Müşteri grubu | Mağaza → ERP | Cari Al işinde, ERP'de aynı adla tanımlı cari grubuna yazılır |
| Müşteri adresleri | Mağaza → ERP | Cari adres defterine eklenir ve güncellenir; silinmez |
| Havale/EFT tahsilatı | Mağaza → ERP | Cariye dekont olarak yazılır (ayarla açılır, banka eşleştirmesi gerekir) |
| Kredi kartı tahsilatı | Mağaza → ERP | Cariye POS tahsilatı olarak yazılır (ayarla açılır, POS eşleştirmesi gerekir) |
| Sipariş iptali | Mağaza → ERP | ERP siparişi iptal durumuna çekilir, kayıt silinmez (ayarla açılır) |
| Kargo takip numarası | Mağaza → ERP | Kargo numarası, kargo firması ve taşıyıcı bilgisi (ayarla açılır) |
| Aktarıldı işareti | Entegrasyon → Mağaza | ERP'ye yazılan sipariş mağazada işaretlenir |
| Fatura bilgisi | ERP → Mağaza | Fatura numarası, tarihi, ETTN ve görüntüleme bağlantısı (ayarla açılır) |
| Ürün | ERP → Mağaza | Ad, e-ticaret açıklaması, barkod, birim, KDV oranı ve eşleştirilen diğer alanlar |
| Fiyat | ERP → Mağaza | Seçilen satış fiyatı; isteğe bağlı döviz, indirimli ve alış fiyatı |
| Stok miktarı | ERP → Mağaza | Tüm depoların toplamı ya da seçilen depo |
| Varyant | ERP → Mağaza | Renk ve beden alt ürünleri; ayrı fiyat, barkod ve stok (ayarla açılır) |
| Ürün resimleri | ERP → Mağaza | Stok kartı resimleri (ayarla açılır) |
| Kategori, marka ve model | ERP → Mağaza | Ayrı işlerle açılır, ürünler bunlara bağlanır |
| Menşei ülkesi | ERP → Mağaza | Üretim yeri ülke koduna çevrilerek gönderilir |
| Yeni ürün işareti | ERP → Mağaza | Belirlenen gün sayısı boyunca (ayarla açılır) |
| Yayın durumu | ERP → Mağaza | ERP'de kapatılan ürün mağazada yayından kaldırılır |
| Seçim listeleri | ERP → Panel | Depolar, sipariş durumları, banka hesapları, POS tanımları, cari grupları ve ERP sistem ayarları, panel seçimleri için yalnızca okunur |

Mağazada zaten bulunan ürünlerin fiyat, stok, resim ve varyant bilgileri "Mağazada zaten olan ürünleri güncelle" ayarı
açıkken güncellenir.

## Yönetim paneli

Entegrasyon, tarayıcıdan açılan Türkçe bir panelle yönetilir. Panel açık ve koyu temayı destekler. Ekranlar aşağıda
tanıtılmıştır.

### Giriş ve ilk yönetici hesabı
İlk açılışta, entegrasyonun kurulu olduğu bilgisayarda oluşturulan kurulum koduyla yönetici hesabı oluşturulur. Sonraki
girişler kullanıcı adı ve parolayla yapılır. Parola unutulduğunda giriş ekranı, bilgisayarda çalıştırılacak kurtarma
komutunu gösterir.

### Kurulum sihirbazı
İki adımlıdır. İlk adımda "Wolvox API adresi" ve "API anahtarı" alanları doldurulur ve ERP'deki şirket listesi okunarak
bağlantı sınanır. İkinci adımda ERP şirketi ve çalışma yılı seçilir ve mağaza için kısa bir ad (T-Soft site anahtarı) girilir.
Şubeli kurulumda şube de bu adımda seçilir.

### Menü ve bağlantı göstergeleri
Ekranlar sol menüde üç grupta listelenir: İzleme ve Operasyon, Kayıt ve Tanı, Sistem ve Servis. Menüde arama yapılabilir ve
menü daraltılabilir. Her ekranın üst şeridinde seçili şirket, yıl ve şube ile ERP ve T-Soft bağlantı göstergeleri bulunur.

### Durum
Panelin ana ekranıdır ve kendini düzenli olarak yeniler. Üst kısımda dört gösterge vardır: sağlık, acil durdurma, en eski
işlenemeyen kaydın yaşı ve çalışan iş sayısı. Acil durdurma anahtarı ve işlerin son tur özeti de bu ekrandadır. WolvoxApi
bağlantısını anında sınayan bir test düğmesi ve ERP'de yapılan bir değişikliğin hemen görünmesi için güncel verinin yeniden
okunmasını sağlayan bir düğme bulunur. Mağaza bağlantısının durumu ve Wolvox ERP sistem ayarlarının entegrasyonla uyum özeti
gösterilir. Lisans sorunu varsa ekranın en üstünde bildirilir.

### Entegrasyon
Cari, Cari Adres, Sipariş ve Stok işleri ile mağaza bağlantısını düzenli olarak denetleyen Oturum sağlığı işi ayrı kartlarda
görünür. Her kartta işin durumu, son turun zamanı ve sonucu yer alır; sonuç yapılan, hata ve atlanan sayılarıyla gösterilir.
Kartta işi açıp kapatan anahtar, ayar düğmesi, gerçek tur ve deneme turu düğmeleri bulunur. Sipariş kartında otomatik
çalışmanın durumu ve sonraki turun saati görünür. Kaldığı yeri izleyen işlerde taramayı baştan başlatan bir sıfırlama düğmesi
vardır; bu düğme kayıt silmez.

### Canlı akış
Entegrasyon ekranının altında, çalışan işin her adımını anlık gösteren bir akış penceresi yer alır. Satırlar aranabilir ve
başarılı, bilgi, uyarı ve hata süzgeçleriyle ayrılabilir. Görünen satırlar kopyalanabilir ya da metin dosyası olarak
indirilebilir.

### Cari ve Cari Adres eşleştirme
İş kartındaki ayar düğmesi sağdan bir eşleştirme paneli açar. Bu panelde ERP alanları aranıp eklenir ve karşılarına bir
mağaza alanı ya da sabit değer seçilir. Satırlar tek tek ya da topluca kilitlenebilir. Cari eşleştirmesinde, isteğe bağlı
olarak mağazada boşaltılan bir alan ERP'de de boşaltılır; adres eşleştirmesinde bu seçenek yoktur. Değerini programın kendisi
hesapladığı için elle bağlanamayan alanlar ayrıca listelenir. Tek düğmeyle varsayılan eşlemeye dönülebilir.

### Stok ayarları ve eşleştirme
Ekranda Ayarlar ve Stok Eşleştirme sekmeleri bulunur; ERP'de stok kartı özel tanımı varsa Özel Alan Eşleştirme sekmesi de
çıkar. Ayarlar sekmesi Genel, Fiyat, Envanter ve Dosya alt sekmelerine ayrılır. İlk ürün kontrolü, güncelleme, varyant, fiyat
ve KDV, stok deposu ve resim ayarları bu sekmelerdedir. Dosya sekmesinde onay isteyen ve geri alınamayan bir resim sıfırlama
işlemi vardır: mağazadaki ürün resimlerini siler ve ERP'deki resimleri baştan yükler; mağaza panelinden eklenen resimler de
silinir.

### Sipariş ayarları
Ekran şu sekmelerden oluşur: Genel Ayarlar, Fiyat Ayarları, Kargo Ayarları, Ödeme Ayarları, İptal-İade Ayarları ve Genel
Muhasebe. Aktarılacak durumlar, başlangıç tarihi, tur başına yazma sayısı, otomatik çalıştırma, ERP sipariş durumu ve depo
seçimi Genel Ayarlar sekmesindedir. Her ayarın yanında bir açıklama düğmesi vardır; geri alınamayan ya da mağazayı etkileyen
ayarlarda ayrıca uyarı gösterilir. Tek bir Kaydet düğmesi tüm sekmeleri birlikte kaydeder.

### Ödeme eşleştirmeleri
Ödeme Ayarları içindeki eşleştirme pencerelerinde ERP banka hesapları ile mağaza havale hesapları, ERP sanal POS tanımları ile
mağaza POS adı ve taksit sayısı, kargo firmaları ile ERP cari kodları eşleştirilir. Tahsilatın yazılacağı mağaza durumları da
bu pencerelerde seçilir.

### İşlenemeyen Kayıtlar
ERP'nin reddettiği sipariş ve cari açma kayıtları, sonucu belirsiz kalan siparişler ve mağazaya yazılamayan ürünler burada
kartlar hâlinde listelenir. Kartlar duruma göre süzülebilir ve aranabilir. Bir karta tıklanınca açılan inceleme panelinde hata
ayrıntısı ve hata kodunun anlamı görülür. "Yeniden dene" tek bir deneme izni verir, "Vazgeç" kaydı kapatır ve kayda operatör
notu eklenebilir. Sonucu belirsiz siparişlerde yeniden deneme ve vazgeçme yoktur; sipariş ERP'de kontrol edilip "ERP'de var —
yazıldı say" düğmesiyle kapatılır.

### Denetim
Entegrasyonun yaptığı işlemler bir tabloda listelenir: zaman, kategori, eylem, sonuç ve ayrıntı. Tetiklenen işler ve yazma
sonuçları buradan izlenir.

### Servis
Windows servisinin durumu, başlangıç türü ve yönetici yetkisi bu ekranda görünür. Servis buradan kurulabilir, durdurulabilir
ya da kaldırılabilir; başlangıç türü otomatik ya da elle olarak ayarlanabilir. Yönetici yetkisi yoksa düğme yerine
çalıştırılacak komut gösterilir. Servis durdurulduğunda panel de kapanır.

### Ayarlar
Bu ekranda mağaza bağlantısı, WolvoxApi bağlantısı, bağlantı testi ve çalışma alanı yer alır. Mağaza bağlantısı için site
adresi, web servis kullanıcı adı ve parolası; WolvoxApi bağlantısı için adres ve anahtar girilir. Çalışma alanı şirket,
çalışma yılı ve şubeden oluşur. Kimlik bilgileri kaydedildikten sonra gösterilmez; yalnızca tanımlı olup olmadıkları ve son
değişiklik tarihi görünür. "Bağlantıyı test et" düğmesi iki tarafa veri yazmadan gerçek bir çağrı yapar. "Lisans sözleşmesi"
kartında onaylanan sözleşmenin sürümü, onaylayan ve onay zamanı görünür; onaylanan metin ve onay geçmişi buradan açılır.

## Kurulum ve devreye alma

1. **WolvoxApi'yi hazırlayın.** WolvoxApi'nin güncel sürümü kurulu, lisanslı ve çalışır durumda olmalıdır. Lisansınızın
   T-Soft entegrasyonu ek ürününü kapsadığından emin olun.
2. **Mağaza erişimini hazırlayın.** T-Soft mağaza panelinizde gerekli yetkilere sahip bir web servis kullanıcısı tanımlayın.
   Mağazanızda IP kısıtı varsa entegrasyonun çalışacağı bilgisayara izin verin.
3. **Entegrasyonu kurun.** Entegrasyonu WolvoxApi'ye erişebilen bir Windows bilgisayara kurun ve yönetim panelini açın.
4. **Yönetici hesabını oluşturun ve sözleşmeyi onaylayın.** İlk açılışta, bilgisayarda oluşturulan kurulum koduyla yönetici
   hesabınızı oluşturun; ardından lisans sözleşmesini okuyup onaylayın.
5. **Kurulum sihirbazını tamamlayın.** "Wolvox API adresi" ve "API anahtarı" alanlarını doldurun; ardından ERP şirketini,
   çalışma yılını, gerekiyorsa şubeyi seçin ve mağazanız için kısa bir ad girin. Veri oluştuktan sonra bu ad, şirket ve
   çalışma yılı değiştirilemez; seçiminizi dikkatle yapın.
6. **Mağaza bağlantısını girin ve test edin.** Ayarlar ekranında web servis kullanıcı bilgilerini girin ve "Bağlantıyı test
   et" düğmesiyle iki tarafı sınayın.
7. **ERP tanımlarını hazırlayın.** Kullanacağınız özelliklere göre ERP'de gerekli tanımları yapın: banka hesapları, sanal POS
   tanımları, kargo firmaları için cari kartlar, kargo, hizmet ve hediye paketi stok kartları, cari grupları ve depolar.
   Menşei gönderecekseniz ülke tanımlarına ülke kodlarını girin.
8. **Ayarları ve eşleştirmeleri yapın.** Önce aktarılacak sipariş durumlarını, başlangıç tarihini ve ülke kodunu belirleyin.
   Sonra ödeme türü, banka, POS ve kargo eşleştirmelerini yapın; fiyat ve KDV ayarlarını belirleyin.
9. **Deneme turuyla kontrol edin.** Her işi önce kuru turla çalıştırın ve hangi kaydın neden yazılacağını canlı akıştan
   inceleyin. Cari Al ve Adres Al işlerinde tur sınırı yoktur, tüm üyeler tek turda işlenir; bu işleri gerçek turdan önce
   mutlaka kuru turla deneyin.
10. **Ürün gönderimine hazırlanın.** Varyant gönderecekseniz mağazada Renk ve Beden özellik gruplarını tanımlayın. Ardından
    Kategorileri Aç ve Marka/Model Aç işlerini çalıştırın ve Stok Gönder'i önce kuru turla deneyin.
11. **Küçük başlayın.** İlk turlarda bir sipariş ve bir ürün gönderin. ERP'de ve mağazada sonucu kontrol edin; fiyat ve KDV
    seçimlerinin doğru olduğundan özellikle emin olun. Sonra "Mağazada zaten olan ürünleri güncelle" ayarını açın.
12. **Otomatik çalıştırmayı açın.** Sipariş Al işinin otomatik çalışmasını açın ve tur başına yazma sayısını hacminize göre
    artırın. Varsayılan ayarlarla, yani turda bir kayıt ve 15 dakikalık aralıkla, saatte en fazla dört kayıt yazılır.
13. **Servisi otomatik başlatmaya alın.** Servis ekranından başlangıç türünü otomatik yapın; böylece bilgisayar yeniden
    başladığında entegrasyon kendiliğinden çalışır.

Kurulum ve devreye alma sürecinde destek almak için bizimle iletişime geçebilirsiniz.

## Kapsam ve sınırlar

**Dövizli siparişler** — Para birimi ERP'nin taban para biriminden farklı olan siparişler ERP'ye otomatik yazılmaz; canlı
akışta bildirilir ve ERP'ye elle girilir. Bu siparişlerin tahsilatı da elle girilir.

**Ek tutarlar** — Gümrük hizmet bedeli içeren ya da kısmen puanla ödenen siparişler, bu tutarlar ERP satırlarına
yansıtılamadığı için otomatik aktarılmaz ve elle girilir.

**Cari eşleşmesi** — Cariler mağazadan ERP'ye aktarılır; ERP carileri mağazaya üye olarak gönderilmez. Var olan cari yalnızca
cari koduyla, yani üyenin web servis koduyla bulunur; vergi numarası, TC kimlik numarası ya da e-posta ile eşleştirme
yapılmaz. Bu nedenle ERP'de daha önce açılmış bir cari, mağaza üyesiyle kendiliğinden eşleşmez ve o müşteri için web servis
koduyla yeni bir cari açılır. Cari kodu ERP'de elle değiştirilirse cari ile mağaza üyesi arasındaki bağ kopar.

**Cari grubu** — ERP'de tanımlı olmayan cari grubu açılmaz; grup önce Wolvox'ta tanımlanmalıdır.

**Geri alma** — ERP'ye yazılan sipariş silinmez; iptal yalnızca siparişin durumunu değiştirir. ERP'de silinen sipariş bir daha
otomatik aktarılmaz. Kısmi iade desteklenmez; iptal siparişin tamamı için yapılır.

**Tahsilat** — Otomatik tahsilat yalnızca Havale/EFT ve kredi kartı ödemeleri için yapılır. Kapıda ödeme ve cariden ödeme
siparişlerine tahsilat yazılmaz. Sonucu doğrulanamayan tahsilat tekrar gönderilmez; tahsilatın ERP'de olup olmadığını kontrol
edin, yoksa elle girin.

**Faturalandırma** — Entegrasyon fatura kesmez ve siparişi faturaya dönüştürmez; faturalandırma ERP'de yapılır. Kargo tarihi
ERP'ye elle girilir.

**Stok gönderimi** — Stok Gönder işi otomatik çalışmaz, panelden başlatılır. Mağazada zaten bulunan ürünlerin stok ve fiyatı
yalnızca güncelleme ayarı açıkken güncellenir.

**Ürün ve adres silme** — Mağazadan ürün silinmez; ERP'de kapatılan ürün yalnızca yayından kaldırılır. Mağazada açılan ürünler
ERP'ye aktarılmaz. Adresler de silinmez; yanlış eklenen adres ERP'den elle kaldırılır. Üyeliksiz (misafir) siparişlerde adres
cari adres defterine yazılmaz.

**Kategori, marka ve model** — Ürün gönderiminde kendiliğinden açılmaz, ayrı işlerle açılır. Bu işlerin deneme turu yoktur ve
açılan kayıtlar mağazadan geri alınamaz.

**Şube** — Tüm sipariş, cari ve adres kayıtları çalışma alanında seçilen şubeye yazılır.

**Stok blokesi** — ERP'de sipariş stok blokesi kapalıysa yazılan sipariş stoğu düşürmez. Durum ekranı bu ayarı gösterir ve
uyarır.

**Panel erişimi** — Panelde tek bir yönetici hesabı vardır. Panel yalnızca entegrasyonun kurulu olduğu bilgisayardan açılır;
uzaktan erişim yoktur.

**Kişisel veri** — Canlı akış ve günlük dosyaları müşteri adı, e-posta ve telefon gibi kişisel veriler içerebilir ve 14 gün
saklanır. Denetim kayıtları ve çözülmüş işlenemeyen kayıtlar, işleyişin ihtiyaç duyduğu kayıtlar dışında 90 gün sonra silinir.
Destek talebine eklemeden önce dosyaları gözden geçirin.

## Sık sorulan sorular

### Aynı sipariş ERP'ye iki kez yazılabilir mi?
Hayır. Entegrasyon bir siparişi yazmadan önce kendi kayıt defterine ve ERP'deki siparişlere bakar. Yazdıktan sonra siparişi
ERP'den geri okuyarak doğrular ve mağazada "aktarıldı" olarak işaretler. Sonucu kesinleşmeyen bir yazma kendiliğinden tekrar
gönderilmez; sipariş İşlenemeyen Kayıtlar ekranında listelenir ve ERP'de kontrol edildikten sonra "ERP'de var — yazıldı say"
düğmesiyle kapatılır.

### WolvoxApi'ye ya da ERP'ye ulaşılamazsa ne olur?
Bağlantı sorunu Durum ekranında ve üst şeritteki göstergede görünür. ERP'ye yazılamayan sipariş mağazada "aktarıldı" olarak
işaretlenmez ve bağlantı geri geldiğinde sonraki turlarda yeniden ele alınır. Bağlantı yazma sırasında koptuysa kayıt tekrar
gönderilmez; İşlenemeyen Kayıtlar ekranında listelenir ve ERP'de kontrol edilmesi istenir.

### Lisansın süresi dolarsa ne olur?
Bitişe 30 gün kala panelde kalan gün sayısıyla bir uyarı çıkar. Lisansın süresi dolarsa ya da lisans T-Soft entegrasyonu ek
ürününü kapsamıyorsa işler başlamaz ve nedeni Durum ve Entegrasyon ekranlarında gösterilir. Lisans nedeniyle bekleyen kayıtlar
hata sayılmaz. Lisans yenilendiğinde işler kendiliğinden devam eder.

### Dövizli siparişler aktarılıyor mu?
Para birimi ERP'nin taban para biriminden farklı olan siparişler ERP'ye otomatik yazılmaz; canlı akışta bildirilir ve ERP'ye
elle girilir. Mağazanın varsayılan para birimi ERP'nin taban para birimiyle aynı olmalıdır.

### Mağazada iptal edilen sipariş ERP'de ne olur?
İptal sayılacak mağaza durumlarını ayarlardan seçerseniz, aktarılmış bir sipariş bu durumlardan birine geçtiğinde ERP'de
seçtiğiniz iptal durumuna çekilir. Kayıt silinmez ve entegrasyonun yazdığı tahsilat da iptal edilir. İrsaliyesi ya da
faturası kesilmiş siparişe dokunulmaz ve uyarı verilir.

### Kapıda ödemeli siparişler nasıl aktarılır?
Mağazanızdaki her kargo firmasına ERP'de bir cari atarsınız. Kapıda ödemeli sipariş yazılırken tahsilatı yapacak kargo
firmasının carisi virman carisi olarak belirtilir; sipariş faturalandırıldığında müşterinin borcu bu cariye aktarılır.
Eşleşme yoksa sipariş bekletilir; isterseniz virmansız aktarımı seçebilirsiniz.

### ERP'ye yazmadan deneyebilir miyim?
Evet. Cari Al, Adres Al, Sipariş Al ve Stok Gönder işlerinin her birinde panelde "kuru tur" adıyla görünen bir deneme turu
vardır. Deneme turu gerçek turla aynı kararları verir ama ERP'ye de mağazaya da hiçbir şey yazmaz; hangi kaydın neden
yazılacağını ya da bekletileceğini satır satır gösterir.

### Mağazadaki mevcut ürünlerim etkilenir mi?
Varsayılan olarak hayır. Mağazada zaten bulunan ürünler, "Mağazada zaten olan ürünleri güncelle" ayarı açılmadıkça
değiştirilmez. Ayar açıldığında yalnızca değişen alanlar yazılır ve mağaza panelinde elle düzenlenmiş ürünleri atlama seçeneği
de vardır. Mağazadan ürün silinmez.

### Stoklar mağazaya kendiliğinden gönderilir mi?
Stok Gönder işi panelden başlatılır; sipariş aktarımı gibi belirli aralıklarla kendiliğinden çalışmaz. Mağazada zaten bulunan
ürünlerin stok ve fiyatının güncellenmesi için "Mağazada zaten olan ürünleri güncelle" ayarının açık olması gerekir.

## Gereksinimler

- 64 bit Windows 10, Windows 11 ya da Windows Server
- ASP.NET Core 10 Runtime
- AKINSOFT Wolvox ERP
- Güncel sürüm, lisanslı ve çalışır durumda WolvoxApi; lisansın T-Soft entegrasyonu ek ürününü kapsaması gerekir
- T-Soft mağazanızda web servis erişimi ve gerekli yetkilere sahip bir web servis kullanıcısı
- Mağazanın varsayılan para biriminin ERP'nin taban para birimiyle aynı olması

## Lisanslama

Wolvox T-Soft Entegrasyon, WolvoxApi lisansına eklenen T-Soft entegrasyonu ek ürünü olarak çalışır. Kullanım, iki ürün için
ortak olan son kullanıcı lisans sözleşmesine tabidir; sözleşme ilk kurulumda panelde okunup onaylanır, yeni sürümü
yayımlandığında onay yeniden istenir. Lisansın durumu ve bitiş tarihi WolvoxApi yönetim aracında görünür. Entegrasyon panelinde bitişe 30 gün kala kalan gün sayısıyla bir uyarı çıkar;
lisans geçersizse işler çalışmaz ve nedeni Durum ve Entegrasyon ekranlarında gösterilir. Lisans yenilendiğinde işler
kendiliğinden devam eder. Fiyatlandırma ve lisans koşulları için bizimle iletişime geçin.

## İletişim

Dijitalkobi E-Ticaret Yazılım Bilişim Reklam ve Danışmanlık Hizmetleri Sanayi ve Ticaret Limited Şirketi

Web: https://www.dijitalkobi.com.tr/

E-posta: kenan@dijitalkobi.com.tr

Ürün tanıtımı, fiyat ve lisans bilgisi için bizimle iletişime geçebilirsiniz.

## Yasal bilgi

Wolvox T-Soft Entegrasyon, Dijitalkobi tarafından bağımsız olarak geliştirilmiştir ve AKINSOFT ya da T-Soft ile bir ortaklık
veya onay ilişkisi içermez. AKINSOFT, Wolvox ve T-Soft, sahiplerinin ticari markalarıdır; sayfada geçen diğer ürün ve firma
adları da sahiplerinin ticari markalarıdır. Yazılımın tüm hakları Dijitalkobi'ye aittir.
