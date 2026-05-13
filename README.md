# BSM316 Gömülü Sistemler - Ödev 3

Bu proje, Bartın Üniversitesi Bilgisayar Mühendisliği Bölümü "BSM316 Gömülü Sistemler" dersi kapsamında STM32F103C8T6 (Blue Pill) mikrodenetleyicisi kullanılarak geliştirilmiş bir donanımsal zamanlayıcı (Timer) ve Flash bellek (kalıcı hafıza) uygulamasıdır.

## 📋 Proje Özeti
Proje, `HAL_Delay` gibi bekletme fonksiyonları kullanılmadan, **TIM2 zamanlayıcısı kesmeleri (interrupt)** kullanılarak dâhili LED'in (PC13) kontrol edilmesini amaçlamaktadır. Cihazın yanıp sönme (blink) sayısı 4 ile 7 arasında değişebilmekte ve bu değer **Dâhili Flash Belleğe** kaydedilerek güç kesintilerinde bile korunmaktadır.

## 🛠️ Kullanılan Donanımlar
* STM32F103C8T6 (Blue Pill) Geliştirme Kartı
* ST-Link V2 Programlayıcı
* Jumper Kablolar & Cımbız (veya Buton)

## 📌 Pin Konfigürasyonu
* **PC13 (GPIO Output):** Blue Pill üzerindeki dâhili yeşil LED'i kontrol eder.
* **PA0 (GPIO Input - PullUp):** Buton girişidir. (Dışarıdan gelen kısa/uzun basımları algılar).
* **PA1 (GPIO Output - Low):** PA0 pinine cımbızla kısa devre yaparak buton simülasyonu yapabilmek için sürekli Lojik 0 (GND) seviyesinde tutulur.

## 🚀 Temel Özellikler
1. **Donanımsal Zamanlama:** TIM2, saniyede 1 kere tetiklenecek şekilde (Prescaler: 7999, Period: 999) ayarlanmıştır. LED'in yanıp sönmesi tamamen kesme (interrupt) tabanlı bir durum makinesi (state machine) ile kontrol edilir.
2. **Kalıcı Hafıza (Flash Memory):** `blink_count` değişkeni, cihazın enerjisi kesilse dahi kaybolmaması için STM32'nin dâhili Flash belleğine (Sayfa 63: `0x0800FC00`) kaydedilir ve cihaz açıldığında buradan okunur.
3. **İlk Kurulum Kontrolü:** Eğer Flash bellek daha önce hiç yazılmamışsa (içi `0xFFFF` doluysa), değer güvenli bir şekilde başlangıç ayarı olan 4'e sabitlenir.
4. **Buton Kontrolü:**
   * **Kısa Basım:** `blink_count` değerini 1 artırır (7'ye ulaştığında tekrar 4'e döner).
   * **Uzun Basım (Çalışırken):** Sistemi sıfırlamaz, kısa basım gibi sayılarak değeri 1 artırır.
   * **Uzun Basım (Sistem Açılırken):** Cihaz açılırken butona (PA0-PA1 arası) 3 saniye basılı tutulursa, sistem **Fabrika Ayarlarına** döner ve değer tekrar 4 olur.

## ⚙️ Geliştirme Ortamı
* **Araçlar:** STM32CubeMX, Visual Studio Code (STM32 Extension), GCC ARM Toolchain
* **Framework:** STM32 HAL Library
* **Derleme/Build:** Makefile (mingw32-make)

## 🎥 Çalışma Senaryoları ve Test
Projenin donanım üzerindeki test senaryoları şu şekildedir:
1. Sisteme güç verildiğinde LED 4 kere yanıp söner, 5 saniye bekler ve tekrarlar.
2. Butona kısa basıldığında, yanıp sönme döngüsü 5'e çıkar.
3. Cihazın enerjisi kesilip geri verildiğinde (ST-Link tak-çıkar), cihaz flash'tan okuma yapar ve 5 kere yanıp sönmeye devam eder.
4. Cihaz çalışırken butona 3 saniye basılı tutulduğunda değer sadece 1 artarak 6 olur.
5. Cihazın gücü kesilip, butona basılı tutularak güç verildiğinde ve 3 saniyeden fazla beklendiğinde sistem fabrika ayarlarına (4) geri döner.

## 🎥 Uygulama Videosu
Projenin çalışma kanıtını içeren YouTube videosuna aşağıdaki bağlantıdan ulaşabilirsiniz:

-------------------------
