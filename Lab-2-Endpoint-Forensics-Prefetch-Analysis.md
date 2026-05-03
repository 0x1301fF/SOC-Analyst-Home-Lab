# DFIR Laboratuvarı: Endpoint Forensics ve Evidence of Execution

## Laboratuvar Senaryosu ve Amacı
Bu laboratuvar çalışması, bir Incident Response sürecinde Endpoint üzerinde gerçekleşen yetkisiz araç kullanımını (Rogue Tool Execution) simüle etmek amacıyla kurgulanmıştır. Temel hedef; sistemde çalıştırılan örnek bir aracın (`putty.exe`) ardında bıraktığı Windows Prefetch izlerini (artifact) analiz etmek ve "Dead-box Forensics" metodolojisiyle Evidence of Execution  tespit süreçlerini uygulamalı olarak gerçekleştirmektir.

## Analiz Metodolojisi ve Kullanılan Araçlar
Simülasyon kapsamında, saldırganların log silme ihtimaline karşı doğrudan işletim sistemi çekirdek artifact'lerine odaklanılmıştır.
* **Analiz Edilen Artifact:** Windows Prefetch Dosyaları (`C:\Windows\Prefetch\*.pf`)
* **Kullanılan DFIR Aracı:** PECmd.exe (Prefetch Explorer Command Line - Eric Zimmerman Tools)
* **İnceleme Formatı:** CSV çıktısı üzerinden Timeline (Zaman Çizelgesi) analizi.

---

## Analiz Adımları ve Laboratuvar Bulguları

### 1. Artifact Parsing  İşlemi

><img width="1920" height="1080" alt="1 DIFR" src="https://github.com/user-attachments/assets/6a4e1935-9a7c-4b96-98cf-68babfe41974" />



Doğrudan disk üzerinden elde edilen şifreli `.pf` binary dosyaları hedeflenmiştir. `PECmd.exe` komut satırı aracı kullanılarak bu dosyalar parse edilmiş ve analize uygun, okunabilir bir CSV zaman çizelgesine dönüştürülmüştür.


### 2. Evidence of Execution Tespiti

><img width="1920" height="1020" alt="10 DIFR" src="https://github.com/user-attachments/assets/35b078c1-0737-4f45-9b08-c3e94a7dd735" />


Parse edilen veriler içerisinde yapılan filtreleme sonucunda, simülasyon için kullanılan `putty.exe` aracının sistem üzerinde başarılı bir şekilde çalıştırıldığı somut timestamp ile kanıtlanmıştır.

* **İncelenen Araç:** putty.exe
* **First Execution (İlk Çalıştırılma):** 2026-04-22 20:21:37
* **Last Execution (Son Çalıştırılma):** 2026-04-22 21:10:52
* **Run Count (Çalıştırılma Sayısı):** 2

### 3. Timeline ve Run Count Analizi
Run Count metriği incelenerek, aracın sistemde kazara mı açıldığı yoksa periyodik olarak mı tetiklendiği analiz edilmiştir. Bu metrik, gerçek vakalarda persistence tespiti için kritik bir göstergedir.

### 4. Files Loaded 
putty.exe dosyasının execution anındaki ilk 10 saniyelik aktivitesini kaydeden "Files/Directories Referenced" (Başvurulan Dosya ve Dizinler) bölümü incelenmiştir. Bu analiz, aracın sistemde hangi Path çalıştırıldığını ve arka planda anormal bir Process başlatıp başlatmadığını doğrulamak için kritik önem taşır.

Execution Path: Dosyanın \PROGRAM FILES\PUTTY\PUTTY.EXE dizini üzerinden tetiklendiği tespit edilmiştir

Runtime DLL Yüklemeleri ve Anomali Kontrolü: Aracın execution sırasında NTDLL.DLL, KERNEL32.DLL ve USER32.DLL gibi standart Windows API kütüphanelerini çağırdığı gözlemlenmiştir. İnceleme sonucunda; aracın arka planda cmd.exe veya powershell.exe gibi shell araçlarını çağırmadığı veya geçici dizinlere (C:\Temp vb.) şüpheli bir dosya bırakmadığı doğrulanmıştır.

> <img width="1920" height="1020" alt="6 DIFR" src="https://github.com/user-attachments/assets/c9b4391b-abd0-4246-be42-dab0d46339af" />


---

## Sonuç ve Kazanımlar
Bu laboratuvar çalışması sonucunda; sadece SIEM veya EDR loglarına bağlı kalmadan, doğrudan işletim sistemi artifact'leri üzerinden timeline analizi yapma ve bir dosyanın execution durumunu forensics standartlarında kanıtlama yetkinliği edinilmiştir.
