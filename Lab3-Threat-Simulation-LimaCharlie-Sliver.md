# SOC Laboratuvarı: EDR Telemetri Analizi ve Adversary Emulation

## Laboratuvar Senaryosu ve Amacı
Bu laboratuvar çalışmasını; Endpoint üzerinde gerçekleşen siber tehditlerin bir EDR sistemine nasıl yansıdığını anlamak amacıyla kurguladım. Kontrollü bir laboratuvar ortamında kendi C2 altyapımı kurarak hedef Windows sistem üzerinde bir sızma simülasyonu gerçekleştirdim. Temel hedefim, zararlı bir payload'un sistemde bırakacağı Process Creation ve Network Connection izlerinin EDR telemetrisinde nasıl loglandığını uygulamalı olarak simüle etmekti.

## Laboratuvar Mimarisi ve Kullanılan Araçlar
Tehdit simülasyonu ve telemetri izleme fazları için izole ettiğim iki farklı sanal sistem kullandım:
* **Tehdit Simülasyonu:** Ubuntu Linux, Sliver C2 Framework (Payload üretimi ve C2 bağlantısı için konfigüre ettim).
* **İzleme ve Telemetri:** Windows 10, LimaCharlie EDR (Canlı telemetri ve süreç izleme amacıyla entegre ettim).

---

## Analiz Adımları ve Telemetri Bulguları

### 1. Sysmon Konfigürasyonu ve EDR Sensör Entegrasyonu
Hedef Windows makinesinin Event Log kapasitesini artırmak ve tehdit avcılığı için daha derin görünürlük sağlamak amacıyla, öncelikle Sysmon kurulumunu "SwiftOnSecurity" kuralları ile gerçekleştirdim.

> <img width="1440" height="829" alt="win10 sysmon ve rule dosyası kuruldu" src="https://github.com/user-attachments/assets/dbd7ed06-94db-4772-9aca-7961687e8781" />


Sysmon konfigürasyonunun ardından LimaCharlie sensörünü sisteme entegre ederek platform ile iletişimini doğruladım. Bu yapılandırma sayesinde, makinede çalışan işlemleri bulut tabanlı EDR konsolu üzerinden canlı olarak izlemek için gerekli uçtan uca görünürlüğü sağladım.

> <img width="1900" height="930" alt="win10 kurulan agent  lima dashboard " src="https://github.com/user-attachments/assets/c0e41e4a-d683-40c3-bb2f-26245e172728" />



### 2. Payload Execution ve C2 Bağlantısı
Ubuntu makinesi üzerinde Sliver C2 framework'ünü kullanarak kendi altyapımla haberleşecek özel bir zararlı payload ürettim (NET_PORCUPINE) ve hedef makineye aktardım. Payload'u çalıştırdığım anda, Sliver terminali üzerinden hedef sistemle başarılı bir şekilde Session bağlantısı kurduğumu doğruladım.

> <img width="1280" height="775" alt="ubuntu zararlı kontrolü" src="https://github.com/user-attachments/assets/fd5873d5-c446-447f-b2d5-62fb5c491ebd" />


### 3. EDR Telemetrisi Üzerinden Anomali Tespiti
Saldırgan konsolunda oturum elde ettikten sonra savunma ve izleme tarafına geçtim. LimaCharlie'nin Timeline  özelliğini kullanarak, çalıştırdığım zararlı payload'un sistemdeki ayak izlerini inceledim. Yaptığım analizde; payload'un imzasız olduğunu ve dış ağa (benim C2 sunucuma) olağandışı bir TCP bağlantısı başlattığını EDR telemetrisi üzerinden tespit ettim.

> <img width="1905" height="932" alt="6 Limacharlie" src="https://github.com/user-attachments/assets/8ae98c26-3972-4b06-b15d-d057bb5ffc28" />

GUI üzerindeki bu tespitin ardından, olayın JSON detaylarını inceleyerek Sysmon tarafından üretilen çekirdek `EventData` ve `System` kayıtlarını doğruladım.Bu sayede process ID, GUID ve EventRecordID eşleşmelerini ham log olarak inceleyerek, arka planda olayların nasıl korele edildiğini görmüş oldum.


<img width="833" height="754" alt="7 Limacharlie" src="https://github.com/user-attachments/assets/f9287f73-b7d1-4ea1-b7b0-5e4363bf0bc5" />


---

## Sonuç:
Bu laboratuvar simülasyonu ile modern siber saldırıların tespiti için sadece "Dead-box Forensics" yöntemlerinin veya ağ loglarının yeterli olmayabileceğini gözlemledim. EDR sistemlerinin sağladığı Process Level Visibility sayesinde bir tehdidi canlı olarak izlemeyi tecrübe ettim. Bu simülasyon, logların ardındaki saldırgan mantığını anlamam açısından vizyonumu genişletti.
