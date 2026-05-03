# SOC Laboratuvarı: Splunk SIEM ile Log Analizi ve SPL Tabanlı Tehdit Avcılığı

## Laboratuvar Senaryosu ve Amacı
Bu laboratuvar çalışmasını, Endpoint üzerinde gerçekleşen siber tehditlerin merkezi bir SIEM platformunda nasıl inceleneceğini pratik olarak görmek amacıyla oluştudum. Splunk üzerinde analiz edebileceğim somut bir veri seti oluşturmak için, daha önceki çalışmamda oluşturup kullandığım davranışsal izlerini gözlemlediğim NET_PORCUPINE.exe zararlısını hedef sistemde yeniden koşturdum. Temel hedefim, bu süreçte üretilen ham logları Splunk ortamına aktarmak ve temel SPL komutlarını kullanarak tehdidin ayak izlerini log seviyesinde analiz etmekdi. 

## Laboratuvar Mimarisi ve Kullanılan Araçlar
* **SIEM Platformu:** Splunk Enterprise.
* **Network Tunneling & Pipeline:** Splunk arayüzünü internet üzerinden erişilebilir kılmak ve log verilerinin aktarım hattını oluşturmak için **Ngrok** tünelleme altyapısı.
* **Tehdit Senaryosu:** `NET_PORCUPINE.exe` zararlısına ait execution ve ağ bağlantısı telemetrileri.


---

## Analiz Adımları ve Tehdit Avcılığı Bulguları

### 1. Data Ingestion ve Indexing Süreci
Analiz sürecine başlamadan önce, Ngrok üzerinden kurduğum tünel sayesinde Splunk ortamına dış ağdan erişim sağlayan güvenli bir pipeline yapılandırdım. Ardından hedef sistem loglarının Splunk platformuna Data Ingestion işlemini gerçekleştirdim. Ham logların doğru bir şekilde index'lendiğini, arama arayüzünde başarıyla parse edildiğini ve Field Extraction adımıyla analiz edilebilir alanlara ayrıldığını doğruladım.


> <img width="523" height="705" alt="Bulut-Yerel Ağ Köprüsü Kanıtı" src="https://github.com/user-attachments/assets/7bcd20aa-c1f4-43c8-adb3-e9749b1a7623" />



### 2. Log Triage ve Anomali Tespiti
Veri seti üzerinde genel bir görünürlük sağlamak amacıyla temel SPL sorguları çalıştırdım. Analiz sürecinde Interesting Fields panelinin sunduğu önizlemelerden de faydalanarak; şüpheli Process Creation aktiviteleri ve olağandışı ağ bağlantıları gibi kritik olayları filtreledim. Böylelikle log yığınları içerisinden hedefe yönelik verileri ayıklayarak analiz çemberini daralttım. 

> <img width="1431" height="830" alt="5 SplunkSS" src="https://github.com/user-attachments/assets/c5eab0ff-29be-4bfb-92a0-dba468be8abe" />


### 3. SPL ile Threat Hunting ve Analiz
Daraltılmış log verileri üzerinde detaylı inceleme yaparken index, spath, stats count by ve table gibi temel SPL komutlarını kullanarak ham verileri anlamlandırdım. Log yapıları içinden spesifik değerleri çekmek için spath, olayları görülme sıklığına göre gruplamak için stats count by ve bulgularımı okunabilir raporlar haline getirmek için table komutundan faydalandım. Bu sorgular sayesinde, tespit edilen zararlı aktivitenin kaynak/hedef IP adreslerini ve zaman çizelgesini ham loglar üzerinden net bir şekilde tespit ettim.

*3A. Delivery - Event ID 11:*


> <img width="1429" height="751" alt="Kanit_1_Delivery_Event11" src="https://github.com/user-attachments/assets/e46c9608-beb8-45f8-a063-6856e11ac5cf" />


*3B. Persistence - Event ID 13:*


<img width="1432" height="836" alt="Kanit_2_Persistence_Event13" src="https://github.com/user-attachments/assets/92f6c1ec-ca64-4f88-ba65-566cbf0f2791" />



*3C. C2 Network Traffic:*


<img width="1434" height="831" alt="Kanit_3_C2_Network_Traffic" src="https://github.com/user-attachments/assets/4e64db6c-34d0-4b10-ad48-de877267d7c5" />

---

## Sonuç:
Bu laboratuvar simülasyonu ile NET_PORCUPINE.exe zararlısının sistemdeki hareketlerini bir SIEM ekranından uçtan uca takip etmeyi başardım. Ngrok ile dışa açık bir data pipeline kurma pratiği yaparken, Splunk üzerinde Interesting Fields yeteneği ve temel SPL komutlarıyla ham olayların nasıl filtrelenip anlamlı bir rapora dönüştürülebileceğini somut verilerle tecrübe ettim.
