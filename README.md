# custom-rom-installer-without-screen
pemasangan custom rom tidak ada layar tested on redmi 5a riva 
pastikan perangkat tidak terkunci bootloader 
dasar: 
pemasangan recovery/twrp sesuaikan dengan perangakt yang di pakai 
jika sudah terpasang recovery/twrp booting in twrp 

#1. Fase Instalasi (adb sideload)

Gunakan ini untuk menginstal Custom ROM atau Magisk.

    Masuk Mode Sideload: (Di TWRP)
    Cuplikan kode

    adb devices (biasanya terbaca perangkat recovery)
    
    adb shell "echo 'tw_has_shown_swipecert=1' > /twres/twrp.flags" (bypass on screnn wellcome twrp)

    adb shell twrp sideload (untuk masuk ke mode sideload)

    adb sideload name_file_magisk_or_rom.zip (Tunggu hingga proses di terminal 100%)



#2. Fase "Kunci Pintu" (Bypass Otorisasi ADB)

       adb shell twrp mount /data (di beberapa twrp dan custom rom terjadi error)

Suntik Kunci RSA:(sesuaikan lokasi peyimpana di windows/linux)

    adb shell "mkdir -p /data/misc/adb/"
   
    adb push ~/.android/adbkey.pub /data/misc/adb/adb_keys
    
    adb shell "chown 1000:1000 /data/misc/adb/adb_keys"
    
    adb shell "chmod 644 /data/misc/adb/adb_keys"



#3. Fase "Jalan Pintas" (Bypass Setup Wizard)

       adb shell twrp mount /system

       adb shell "mount -o remount,rw /system"

Matikan Izin Eksekusi APK: (Metode paling aman agar tidak bootloop)
Cuplikan kode
# Matikan Wizard LineageOS & Provisioning

     adb shell "chmod 000 /system/system/priv-app/LineageSetupWizard/LineageSetupWizard.apk"
  
     adb shell "chmod 000 /system/system/priv-app/ManagedProvisioning/ManagedProvisioning.apk"



#4. Fase "Nirkabel" (Aktifkan TCP/IP 5555)

Agar Anda bisa mengontrol HP via WiFi tanpa kabel USB menggunakan scrcpy.
Edit build.prop sesuaikan dengan varian twrp

    set BP "/system/system/build.prop"

    adb shell "echo 'service.adb.tcp.port=5555' >> $BP"

    adb shell "echo 'ro.setupwizard.mode=DISABLED' >> $BP"

    adb shell "sed -i 's/ro.adb.secure=1/ro.adb.secure=0/g' $BP"


Simpan & Reboot:

    adb shell sync
  
    adb reboot



#5. Fase booting ke android (tunggu 5-10 menit) 

       adb devices (jika berhasil lanjut jika gagal ulangi dari fase 1)

       scrcpy (done) 
  

Ringkasan Perintah "Sakti" (Quick Check)

  Cek Mount: adb shell df -h (Pastikan /system dan /data bukan tmpfs).

  Cek Jalur: adb shell find /system -name "*Wizard*.apk".

  Cek Log: adb logcat *:E (Gunakan jika HP tiba-tiba restart sendiri).

Tips Tambahan: Selalu jalankan adb shell sync sebelum reboot agar perubahan permanen. HP Anda sekarang sudah menjadi "Server Headless" yang tangguh!
