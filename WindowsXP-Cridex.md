# Windows XP - Cridex Malware - reader_sl.exe

Ram imaj analizi yapabilmek için volatility aracını kullanıyoruz. 

![image](https://user-images.githubusercontent.com/65306271/189488767-d8454039-53eb-4348-8450-10dfff41eb73.png)

İmaj dosyasını belirtiyoruz ve makina hakkında bilgi alabilmek için "imageinfo" komutunu kullanıyoruz.

![image](https://user-images.githubusercontent.com/65306271/192135310-00699925-b0a8-49ee-902b-8328006665b7.png)

Çıktıda makinanın işletim sistemi, imajın ne zaman alındığı, önerilen profiller gibi bir takım bilgileri görüyoruz. 

Bir profili seçerek işlemimize devam ediyoruz. Ram imajı alındığı sırada çalışan processleri görüntüleyebilmek için "pslist" komutunu kullanıyoruz.

![image](https://user-images.githubusercontent.com/65306271/192135419-d6d895eb-3002-4f4a-acfa-03d62952a4f5.png)

Daha detaylı bir kontrol için process ağacına bakıyoruz. Bu yüzden "**pstree**" komutunu kullanıyoruz. Çıktıya baktığımızda"**explorer.exe**" altında çalışan "**reader_sl.exe**" şeklinde bir procces görüyoruz.

**Fakat** Explorer.exe kullanıcının masaüstünden sorumlu olduğu bir proccestir ve dosya uzantıları aracılığıyla dosyaları başlatmaktan sorumludur.  Oturum açmış kullanıcı başına yalnızca **1 işlem** oluşturmaktadır ve bir **parent**'a sahip değildir.

Dolayısıyla bu duruma şüpheyle bakmaya başlıyoruz.

![image](https://user-images.githubusercontent.com/65306271/192136036-1d34a6b9-10ee-4904-b949-2cc791c16c0d.png)

Şimdilik bunu bir kenara bırakarak makinanın imajı alınırken kurulan bağlantılarını görebilmek için "**connscan**" komutunu kullanıyoruz.

![image](https://user-images.githubusercontent.com/65306271/192137377-edfe9ce6-0790-46df-b328-078b2f3bc952.png)

Çıktıda gördüğümüz üzere "172.16.112.128" IP adresinin "1038" ve "1037" portundan "41.168.5.140" ve "125.19.103.198" IP adreslerinin 8080 portuna doğru bağlantı kurulmuştur. PID ise 1484'dür ve bu procces ID'nin daha önce tespit ettiğimiz ve şüpheli gördüğümüz reader_sl.exe'ye ait olduğunu farkediyoruz.


Daha da detaya inerek imaj sırasında bu bağlantının devap edip etmediğini görmek amacıyla "**connections**" komutunu kullanıyoruz. 1038 portundan yapılan bağlantının devam ettiğini görüntülüyoruz.

![image](https://user-images.githubusercontent.com/65306271/192137407-c10de5b7-c2e6-439d-9804-765c4958f8e3.png)

Proccese ile ilgili anliz yapabilmek için "memdump" komutunu kullanarak reader_sl.exe'ye ait olan "1640" PID'nin bellek içeriğini indiriyoruz.

![image](https://user-images.githubusercontent.com/65306271/192137624-00a5a00e-9ccd-4f0d-919b-718841e093f0.png)

İşlem tamamlandıktan sonra dosya üzerinde inceleme yapmak için "strings" ve  "grep" komutunu kullanıyoruz fakat çok fazla içerik olduğu için imaj alınırken hala bağlantısı devam eden "41.168.5.140" IP ve "8080" portu özelinde bir arama yapıyoruz.

![image](https://user-images.githubusercontent.com/65306271/192138765-f163c0c0-3152-4eab-a71f-2794c8494175.png)

Aldığımız çıktıda yeni bir IP ve bir dizin görüyoruz. Bunlarla ilgili de ayrıca bir arama yapıyoruz.

![image](https://user-images.githubusercontent.com/65306271/192138821-4bee8a2d-4ea0-407c-82c5-9df5766b20cf.png)

Bulduğumuz dizine bir POST isteği olduğunu görüntülüyoruz ve aramamızı buna göre filtreliyoruz. "41.168.5.140" IP adresinden "/zb/v_01_a/in/" dizinine doğru veri gönderildiğini tespit ediyoruz. 

![image](https://user-images.githubusercontent.com/65306271/192139005-841f904d-2197-4010-84fe-628ddb4f365c.png)

Daha sonra "less" komutu ile manuel bir kontrol yapıyoruz ve birçok domain görüyoruz.

![image](https://user-images.githubusercontent.com/65306271/192139340-bbbf2990-5380-4aa7-a1cc-63a7f24a8abd.png)

Buradan bulabildiklerimizi not alıyoruz ve araştırmaya devam ediyoruz. Makina üzerinde çalıştırılan komutları görebilmek için "cmdline" komutunu kullanıyoruz.

![image](https://user-images.githubusercontent.com/65306271/192139550-3a187f3c-ae37-45aa-84d6-41b3670c5ea0.png)

Burada Reader_sl.exe Adobe Reader ile ilgiliymiş gibi görünmektedir. Çalışan exe yi analiz edebilmek için dump ediyoruz. Bunun için "procdump" komutunu kullanıyoruz.

![image](https://user-images.githubusercontent.com/65306271/192139831-f1402c5d-6d55-4ea6-8844-576cc69bfe95.png)

EXE indikten sonra virustotale atıyoruz ve zararlı olduğunu tespit ediyoruz.

![image](https://user-images.githubusercontent.com/65306271/192139979-b82a5aef-2b6d-4c41-ad18-9c11c12a93d8.png)

Strings komutu ile exe'yi incelediğimizde ise Meterpreter imzasının bulunduğunu görüyoruz, ayrıca adobe ile ilgili de birtakım bilgiler mevcuttur.

![image](https://user-images.githubusercontent.com/65306271/192140035-ba30210d-7121-4c0a-b17f-bc64d92e362e.png)

![image](https://user-images.githubusercontent.com/65306271/192140054-6bb1dcde-2c87-4a47-aff0-e503d57b9ab7.png)

![image](https://user-images.githubusercontent.com/65306271/192140562-97e05586-eca1-4aac-b705-4875951a355e.png)


Tüm bunlara ek olarak çoğu kötü amaçlı yazılım, her sistem başlangıcında yürütmelerini otomatik hale getirmeye çalışır, bu nedenle silinmelerini zorlaştırır. Burda da durumun böyle olup olmadığını anlamak için sistem başlangıcında kullanılan kayıt defteri girdilerine bakıyoruz.

![image](https://user-images.githubusercontent.com/65306271/192141080-cfbea23c-dd9c-409a-b8fe-bf3cd742667a.png)

Printkey komutu ile kayıt defteri anahtarının içeriğini, alt anahtarlarını ve değerlerine bakıyoruz. Tarih olarak kıyasladığımızda ise imajın alındığı tarihe en yakın  zamanda değiştirilen tek kayıt“\Device\HarddiskVolume1\Documents and Settings\Robert\NTUSER.DAT” kayıt defteridir.

![image](https://user-images.githubusercontent.com/65306271/192141217-b6f153c2-39e2-4588-b159-fc468b221f7d.png)

Buradaki "KB00207877.exe"'nin bizim bulduğumuz zararlı ile ilişkili olup olmadığını anlayabilmek için indirdiğimiz bellek dökümünde tekrar bir arama yapıyoruz.

![image](https://user-images.githubusercontent.com/65306271/192141371-3b01fa46-dfce-4685-9c2a-2773dc3ced39.png)

"KB00207877.exe"'nin yürütülebilir dosya, 1640 PID bellek dökümünde bulunduğundan, artık Cridex'in kendisini kalıcı kılmak için kurbanın bilgisayarının başlangıç kayıt defteri anahtarını değiştirdiğini farkettik.
