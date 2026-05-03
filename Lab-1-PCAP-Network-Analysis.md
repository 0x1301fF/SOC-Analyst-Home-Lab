# Olay Müdahale Raporu: PCAP Analizi ve Dağıtım Vektörü Tespiti

## Laboratuvar Senaryosu ve Amacı
Bu çalışmayı, phishing ve typo-squatting vakalarının ağ seviyesinde nasıl tespit edileceğini laboratuvar ortamında görmek amacıyla gerçekleştirdim. Örnek bir PCAP dosyası üzerinden, sahte bir "Google Authenticator" uygulaması indiren hedefin ağ trafiğini analiz ettim. Temel amacım; zararlının dağıtım vektörünü belirlemek, enfekte olan hostu bulmak ve C2 altyapısına ait IoC'leri açığa çıkarmaktı.


## Ağ Topolojisi ve Ortam Bilgileri
Analizini gerçekleştirdiğim yerel ağ segmentine dair, vaka özeti kapsamında sağlanan temel ortam bilgileri aşağıda listelenmiştir: 
* **LAN Segmenti:** 10.1.17.0/24
* **Domain:** bluemoontuesday.com
* **Domain Controller:** 10.1.17.2 (WIN-GSH54QLW48D)
* **Ağ Geçidi (Gateway):** 10.1.17.1

---

## Analiz Adımları ve Bulgular

### 1. Host Tespiti
Şüpheli aktivitenin kaynağını detaylandırmak amacıyla ağdaki DHCP ve Kerberos trafiğini filtreleyerek inceledim. İlk olarak DHCP paketleri üzerinden enfekte olan cihazın IP adresini, MAC adresini ve Hostname bilgisini buldum. Ardından, o an bu cihazı kullanan personelin kimliğini saptamak için Kerberos (AS-REQ) kimlik doğrulama trafiklerini analiz ettim ve aktif kullanıcı hesabını tespit ettim. 

* **Kaynak IP Adresi:** 10.1.17.215
* **Kaynak MAC Adresi:** 00:0d:b7:26:4a:74
* **Hostname:** DESKTOP-L0CSGSJ 
* **User Account:** shutchenson 

> <img width="1440" height="900" alt="1 IP dhcp" src="https://github.com/user-attachments/assets/179dc53e-b806-4e43-93f0-4723215118f4" />
><img width="1440" height="900" alt="KERBEROS-USER ACCOUNT NAME" src="https://github.com/user-attachments/assets/25496d83-897c-4a64-a8a7-45d8aeac3ceb" />



### 2. Delivery ve Payload Tespiti
Ağ trafiği üzerinde yaptığım analizde, hedeflenen sitenin HTTPS trafik kullanması nedeniyle doğrudan HTTP payload analizinin mümkün olmadığını gözlemledim. Bu nedenle analizimi DNS sorguları ve TLS Handshake (SNI) paketleri üzerinden yürüttüm. Yaptığım filtrelemeler sonucunda, istemcinin typo-squatting yöntemiyle oluşturulmuş sahte bir indirme sayfasına yönlendirildiğini tespit ettim. İndirilen şüpheli dosyanın adını OSINT verileriyle doğruladım.


* **Şüpheli Domain:** authenticatoor[.]org
* **Tespit Edilen Vektör:** Malvertising / Şifreli (HTTPS) İndirme
* **Doğrulanan Şüpheli Dosya:** google-authenticator.exe


> <img width="1434" height="917" alt="7 SS" src="https://github.com/user-attachments/assets/c4c0c58c-bcb9-4607-b914-f71901bc4208" />
> <img width="1089" height="568" alt="sitenin paylaştığı cevap görseli" src="https://github.com/user-attachments/assets/5d059dfd-640e-4fc7-843b-44dce980fe4c" />



### 3. C2 Bağlantı Tespiti  ve TCP Analizi 
Zararlı dosyanın çalıştırılmasının ardından, enfekte olan sistemin dış ağdaki C2 sunucuları ile kurduğu iletişimi saptamak amacıyla ağ trafiğini inceledim. Yaptığım DNS trafik analizinde, cihazın şüpheli appointedtimeagriculture.com alan adına ulaşmak için 217.70.186.109 IP adresini çözümlediğini ve bu DNS yanıtının hemen ardından ilgili zararlı adrese yönelik TCP bağlantılarının (SYN paketleri) başlatıldığını paket seviyesinde tespit ettim. 

> <img width="1440" height="900" alt="9 SS" src="https://github.com/user-attachments/assets/db2ac61e-6d9c-4343-bbf0-06ecf2d54f3c" />


### 4. OSINT ve IoC Enrichment
Wireshark üzerinden tespit ettiğim şüpheli C2 domain adresini, açık kaynak tehdit istihbaratı platformu (VirusTotal) üzerinden sorguladım. Adresin birden fazla güvenlik üreticisi tarafından Malicious olarak işaretlendiğini teyit ettim.

><img width="1440" height="900" alt="2 IP research" src="https://github.com/user-attachments/assets/f7dc488a-93e3-4e82-8e04-33764c06cd8c" />



---

## Tespit Edilen IoC'ler 
* **Dağıtım (Delivery) Domaini:** authenticatoor[.]org
* **Dağıtım (Delivery) IP Adresi:** 82.221.136.26
* **C2 Domaini:** appointedtimeagriculture[.]com
* **C2 IP Adresi:** 217.70.186.109

## Sonuç:
Yaptığım PCAP analizi sonucunda tehdit aktörünün ağ seviyesindeki Initial Access ve Delivery aşamalarını ham trafik üzerinden doğruladım.


---
*Not: Bu analizi, Palo Alto Unit 42 tehdit istihbaratı raporlarına dayanan gerçek dünya senaryolarından derlenmiş veriler kullanarak gerçekleştirdim.*

