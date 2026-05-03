# DFIR Laboratuvarı: Endpoint Forensics ve Evidence of Execution

## Laboratuvar Senaryosu ve Amacı
Bu laboratuvar çalışmasını, bir Incident Response sürecinde Endpoint üzerinde gerçekleşen yetkisiz araç kullanımını simüle etmek amacıyla oluşturdum. Temel hedefim; sistemde çalıştırdığım örnek bir aracın (`putty.exe`) ardında bıraktığı Windows Prefetch izlerini analiz etmek ve "Dead-box Forensics" metodolojisiyle Evidence of Execution tespit süreçlerini uygulamalı olarak gerçekleştirmeti.

## Analiz Metodolojisi ve Kullanılan Araçlar
Simülasyon kapsamında, saldırganların log silme ihtimaline karşı doğrudan işletim sistemi çekirdek artifact'lerine odaklandım.
* **Analiz Edilen Artifact:** Windows Prefetch Dosyaları (`C:\Windows\Prefetch\*.pf`)
* **Kullanılan DFIR Aracı:** PECmd.exe (Prefetch Explorer Command Line - Eric Zimmerman Tools)
* **İnceleme Formatı:** CSV çıktısı üzerinden Timeline analizi.

---

## Analiz Adımları ve Laboratuvar Bulguları

### 1. Artifact Parsing  İşlemi

><img width="1920" height="1080" alt="1 DIFR" src="https://github.com/user-attachments/assets/6a4e1935-9a7c-4b96-98cf-68babfe41974" />



Doğrudan disk üzerinden elde ettiğim şifreli `.pf` binary dosyalarını analiz için hedef aldım. `PECmd.exe` komut satırı aracını kullanarak bu dosyaları parse ettim ve analizi kolaylaştırmak için okunabilir bir CSV zaman çizelgesine dönüştürdüm.


### 2. Evidence of Execution Tespiti

><img width="1920" height="1020" alt="10 DIFR" src="https://github.com/user-attachments/assets/35b078c1-0737-4f45-9b08-c3e94a7dd735" />


Parse ettiğim veriler üzerinde yaptığım filtreleme sonucunda, simülasyon için kullandığım `putty.exe` aracının sistem üzerinde başarılı bir şekilde çalıştırıldığını somut timestamp verileriyle kanıtladım.

* **İncelenen Araç:** putty.exe
* **First Execution (İlk Çalıştırılma):** 2026-04-22 20:21:37
* **Last Execution (Son Çalıştırılma):** 2026-04-22 21:10:52
* **Run Count (Çalıştırılma Sayısı):** 2

### 3. Timeline ve Run Count Analizi
Run Count metriğini inceleyerek, aracın sistemde kazara mı açıldığını yoksa periyodik olarak mı tetiklendiğini analiz ettim. Bu metriğin, gerçek vakalarda persistence tespiti için kritik bir gösterge olduğunu uygulamalı olarak gözlemledim.

### 4. Files Loaded 
`putty.exe` dosyasının execution anındaki ilk 10 saniyelik aktivitesini kaydeden "Files/Directories Referenced" bölümünü inceledim. Bu analizi, aracın sistemde hangi dizinden çalıştırıldığını ve arka planda anormal bir Process başlatıp başlatmadığını doğrulamak amacıyla gerçekleştirdim.


* **Execution Path:** Dosyanın `\PROGRAM FILES\PUTTY\PUTTY.EXE` dizini üzerinden tetiklendiğini tespit ettim.
* **Runtime DLL Yüklemeleri ve Anomali Kontrolü:** Aracın execution sırasında `NTDLL.DLL`, `KERNEL32.DLL` ve `USER32.DLL` gibi standart Windows API kütüphanelerini çağırdığını gözlemledim. İncelemelerim sonucunda; aracın arka planda `cmd.exe` veya `powershell.exe` gibi shell araçlarını çağırmadığını veya geçici dizinlere (`C:\Temp` vb.) şüpheli bir dosya bırakmadığını log seviyesinde doğruladım.


> <img width="1920" height="1020" alt="6 DIFR" src="https://github.com/user-attachments/assets/c9b4391b-abd0-4246-be42-dab0d46339af" />


---

## Sonuç:
Bu laboratuvar çalışması ile; sadece SIEM veya EDR loglarına bağlı kalmadan, doğrudan işletim sistemi artifact'leri üzerinden timeline analizi yapma pratiği edindim. Bir dosyanın çalışma geçmişini ham veriler üzerinden nasıl ispatlayabileceğimi somut olarak analiz ettim.

