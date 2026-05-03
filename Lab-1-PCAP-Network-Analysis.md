# Olay Müdahale Raporu: PCAP Analizi ve Dağıtım Vektörü Tespiti

## Olay Özeti
SOC ekibine yapılan bir bildirim doğrultusunda, bir kullanıcının arama motoru üzerinden sahte bir "Google Authenticator" uygulaması indirdiği şüphesiyle ilgili ağ segmentine ait PCAP kaydı incelemeye alınmıştır. Amacımız; zararlı yazılımın ağ üzerindeki dağıtım vektörünü belirlemek, enfekte olan sistemleri tespit etmek ve Komuta Kontrol (C2) altyapısına ait  Indicator of Compromise (IoC) raporlamaktır.

><img width="1089" height="568" alt="sitenin paylaştığı cevap görseli" src="https://github.com/user-attachments/assets/9066afa0-82b6-4988-a411-fa044bd5cf80" />



## Ağ Topolojisi ve Ortam Bilgileri
Analizi gerçekleştirilen yerel ağ segmentine ait mimari detaylar Wireshark (DHCP/DNS) üzerinden şu şekilde haritalandırılmıştır:
* **LAN Segmenti:** 10.1.17.0/24
* **Domain:** bluemoontuesday.com
* **Domain Controller:** 10.1.17.2 (WIN-GSH54QLW48D)
* **Ağ Geçidi (Gateway):** 10.1.17.1

---

## Analiz Adımları ve Bulgular

### 1. Host Tespiti
Ağ trafiği genel olarak incelendiğinde, ağ geçidi üzerinden dış ağa olağandışı HTTP istekleri başlatan kaynak IP adresi tespit edilmiştir.
* **Kaynak IP Adresi:** 10.1.17.215
* **Kaynak MAC Adresi:** 00:0d:b7:26:4a:74

> <img width="1440" height="900" alt="1 IP dhcp" src="https://github.com/user-attachments/assets/179dc53e-b806-4e43-93f0-4723215118f4" />
><img width="1440" height="900" alt="KERBEROS-USER ACCOUNT NAME" src="https://github.com/user-attachments/assets/25496d83-897c-4a64-a8a7-45d8aeac3ceb" />



### 2. Delivery ve Payload Tespiti
Wireshark üzerinde HTTP trafiği izlendiğinde, istemcinin harf benzerliği (typo-squatting) yöntemiyle oluşturulmuş sahte bir indirme sayfasına yönlendirildiği ve HTTP GET isteği ile zararlı bir payload indirdiği gözlemlenmiştir.
* **Şüpheli Domain/URL:** authenticatoor[.]org
* **İndirilen Şüpheli Dosya:** google-authenticator.exe

> <img width="1434" height="917" alt="7 SS" src="https://github.com/user-attachments/assets/85949a25-3f5d-4b70-8deb-238063e533b8" />
><img width="1089" height="568" alt="sitenin paylaştığı cevap görseli" src="https://github.com/user-attachments/assets/da24ff42-3a7e-4ecc-9245-6f711d5eea45" />


### 3. Command & Control (C2) Beaconing Analizi
Zararlı dosyanın indirilmesinin ardından Wireshark'ın istatistik modülleri kullanılarak, sistemin dış ağdaki C2 sunucuları ile kurduğu periyodik TCP bağlantıları saptanmıştır. Ağ üzerindeki DNS trafik analizinde, cihazın şüpheli appointedtimeagriculture.com alan adına ulaşmak için 217.70.186.109 IP adresini çözümlediği ve hemen ardından bu zararlı adrese yönelik TCP bağlantılarının başlatıldığı tespit edilmiştir.

> <img width="1440" height="900" alt="9 SS" src="https://github.com/user-attachments/assets/db2ac61e-6d9c-4343-bbf0-06ecf2d54f3c" />


### 4. OSINT ve IoC Enrichment
Wireshark üzerinden tespit edilen şüpheli C2 domain adresi, açık kaynak tehdit istihbaratı platformu (VirusTotal) üzerinden sorgulanmış ve birden fazla güvenlik üreticisi tarafından "Malicious" (Zararlı) olarak işaretlendiği doğrulanarak False Positive  ihtimali elenmiştir.

><img width="1440" height="900" alt="2 IP research" src="https://github.com/user-attachments/assets/f7dc488a-93e3-4e82-8e04-33764c06cd8c" />



---

## Tespit Edilen IoC'ler 
* **Dağıtım Alan Adı:** authenticatoor[.]org
* **C2 Sunucu IP Adresi:** 5.252.153.241

## Sonuç ve Devam Eden Süreç
Yapılan PCAP analizi sonucunda tehdit aktörünün ağ seviyesindeki Initial Access ve Delivery aşamaları doğrulanmıştır.

---
*Not: Bu analiz, Palo Alto Unit 42 tehdit istihbaratı raporlarına dayanan gerçek dünya senaryolarından derlenmiş veriler kullanılarak gerçekleştirilmiştir.*


