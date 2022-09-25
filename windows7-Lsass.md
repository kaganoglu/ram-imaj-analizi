# Windows 7 - Lsass.exe
### Ram İmajı Alınan Makina Hakkında Bilgi Toplama

Ram imaj analizi yapabilmek için volatility aracını kullanıyoruz. 

![image](https://user-images.githubusercontent.com/65306271/189488767-d8454039-53eb-4348-8450-10dfff41eb73.png)

İmaj dosyasını belirtiyoruz ve makina hakkında bilgi alabilmek için "imageinfo" komutunu kullanıyoruz.

`./volatility imageinfo -f ../ram.raw imageinfo `

![image](https://user-images.githubusercontent.com/65306271/189489318-1114ebee-524d-4b68-ae7a-12063ebe6838.png)

Çıktıda makinanın işeletim sistemi, imajın ne zaman alındığı, önerilen profiller gibi bir takım bilgileri görüyoruz. 

### Process Analizi

Bir profili seçerek işlemimize devam ediyoruz. Ram imajı alındığı sırada çalışan processleri görüntüleyebilmek için "pslist" komutunu kullanıyoruz.

`./volatility -f ../ram.raw --profile=Win7SP1x64 pslist`

![image](https://user-images.githubusercontent.com/65306271/189489776-41bb0c9f-844a-442f-9511-e53e34a716d6.png)

Çalışan processleri görüntüledikten sonra bunları incelemeye başlıyoruz. Burada dikkatimizi çeken bir durum karşımıza çıkıyor. Lssas.exe normal şartlarda windows açıldığında ilk ve bir kere çalışan bir processtir ve burada ikinci bir lsass.exe çalıştığını görüyoruz. Process ID'sine baktığımızda da ilk çalışan lsass.exe PID'sinden farklı olduğunu ve burada bir duplicate durumu olmadığını görüyoruz. Çalışan başka bir process olduğundan emin oluyoruz.

![image](https://user-images.githubusercontent.com/65306271/189490023-4ea62b0e-ccab-4339-a217-988cde6d9046.png)

### Zararlı Yazılım Analizi

Zararlı olduğunu düşündüğümüz exe yi "procdump" komutu ile PID değerini vererek indirmeye çalışıyoruz.

`./volatility -f ../ram.raw --profile=Win7SP1x64 procdump -p 2720  --output-file=lsass.exe --dump-dir=/home/agola/Masaüstü`

![image](https://user-images.githubusercontent.com/65306271/189491318-e6a2b20c-ff9a-4910-ae7d-ce7e41361341.png)

![image](https://user-images.githubusercontent.com/65306271/189491386-22723bd2-9ad0-485e-9e2d-ae9a31fa2405.png)

Exenin çözümlenmiş hex kodlarına baktığımızda ise zararlının meterpreter imzası taşıdığını görüntülüyoruz.

![image](https://user-images.githubusercontent.com/65306271/189492485-fd401071-1733-47c7-93af-9d6a52786cfb.png)

Virustotal'den kontrol ettiğimizde zararlı yazılım olduğunu tespit edebiliyoruz ve birçok bilgiye erişebiliyoruz.

![Screenshot_3](https://user-images.githubusercontent.com/65306271/192135164-28c043e2-e574-48ca-b59e-e7918620e19b.png)






