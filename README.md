# custom-rom-installer-without-screen
pemasangan custom rom tidak ada layar tested on redmi 5a riva 
pastikan perangkat tidak terkunci bootloader

dasar: 
pemasangan recovery/twrp sesuaikan dengan perangakt yang di pakai 
jika sudah terpasang recovery/twrp booting in twrp vol up+power tunggu 10-15 detik 

# 1. Fase Instalasi (adb sideload)

Gunakan ini untuk menginstal Custom ROM atau Magisk
    
pastikan perangkat sebagai recovery
   
    adb devices

bypass on screnn wellcome twrp
   
    adb shell "echo 'tw_has_shown_swipecert=1' > /twres/twrp.flags"

    adb shell twrp sideload

    adb sideload name_file_magisk_or_rom.zip
tunggu proses flashing 100%

 Perintah ini akan menghapus seluruh partisi userdata termasuk enkripsi (opsional)
    
    adb shell twrp format data
 Jika tidak muncul pesan "Invalid argument", berarti pintu sudah terbuka.


# 2. Fase "Kunci Pintu" (Bypass Otorisasi ADB)

    adb shell twrp mount /data

 Suntik Kunci RSA:(sesuaikan lokasi peyimpana di windows/linux)

    adb shell "mkdir -p /data/misc/adb/"
   
    adb push ~/.android/adbkey.pub /data/misc/adb/adb_keys
    
    adb shell "chown 1000:1000 /data/misc/adb/adb_keys"
    
    adb shell "chmod 644 /data/misc/adb/adb_keys"



# 3. Fase "Jalan Pintas" (Bypass Setup Wizard Opsional) 

 perintahkan TWRP untuk melakukan mounting secara otomatis
  
    adb shell twrp mount /system

 Jika perintah ini berhasil, Anda tidak akan melihat pesan error. Jika gagal, TWRP akan memberitahu letak masalahnya.

 Cek apakah folder system_root ada

    adb shell ls -d /system

 Jika folder tersebut ada, gunakan:

    adb shell mount /system
    

 Remount partisi /system agar bisa ditulis (RW)
    
    adb shell "mount -o remount,rw /system"

 Jika gagal, coba gunakan jalur fisik (System-as-root)
    
    adb shell "mount -o remount,rw /"


 Matikan Izin Eksekusi APK: (Metode paling aman agar tidak bootloop)
 Cuplikan kode

 Mencari semua file APK yang mengandung kata "Wizard" atau "Provision"
     
     adb shell "find /system -name '*Wizard*.apk'"
     adb shell "find /system -name '*Provision*.apk'"

 Catatan: Simpan jalur (path) yang muncul dari hasil perintah di atas. Jalur itulah yang akan kita gunakan untuk perintah chmod.

 Ganti [PATH_HASIL_FIND] dengan hasil dari langkah 2
   
    adb shell "chmod 000 [PATH_HASIL_FIND]"
  
    # contoh
    adb shell "chmod 000 /system/system/priv-app/LineageSetupWizard/LineageSetupWizard.apk"
  
    adb shell "chmod 000 /system/system/priv-app/ManagedProvisioning/ManagedProvisioning.apk"

 Jika perintah ini tidak memunculkan error "Read-only", berarti Anda sudah berhasil.


# 4. Fase "Nirkabel" (Aktifkan TCP/IP 5555)

 gar Anda bisa mengontrol HP via WiFi tanpa kabel USB menggunakan scrcpy.

 Edit build.prop sesuikan jalur path system

    set BP "/system/build.prop" 

    adb shell "echo 'service.adb.tcp.port=5555' >> $BP"
   
    adb shell "grep -q 'ro.adb.secure' $BP || echo 'ro.adb.secure=0' >> $BP"
    
    adb shell "echo 'persist.sys.usb.config=mtp,adb' >> $BP"
    
    adb shell "echo 'ro.setupwizard.mode=DISABLED' >> $BP"
    
    adb shell "echo 'ro.setupwizard.enterprise_mode=1' >> $BP"
    
    adb shell "echo 'ro.no_setup_wizard=1' >> $BP"

 Pastikan baris-baris tersebut sudah benar-benar tertulis di dalam file sistem:
 Cuplikan kode

    adb shell "grep -E 'ro.adb.secure|persist.sys.usb.config|ro.setupwizard.mode|service.adb.tcp.port' /system/system/build.prop"

 Pastikan muncul output seperti ini:

    # ro.adb.secure=0
    # persist.sys.usb.config=mtp,adb
    # ro.setupwizard.mode=DISABLED
    # service.adb.tcp.port'

 Simpan & Reboot:

    adb shell sync
  
    adb reboot



# 5. Fase booting ke android (tunggu 5-10 menit) 

    # seharusnya sudah terbaca "devices"
    adb devices

    scrcpy
  booting ke 1 perangkat akan terhubung ke adb +-5 detik itu normal tunggu hingga booting ke 2 terhubung kembali -+1 menit 

# Ringkasan Perintah

  Tambahkan ini untuk membuka fitur Developer Options secara otomatis
  
    adb shell settings put global development_settings_enabled 1

  Cek Mount: adb shell df -h (Pastikan /system dan /data bukan tmpfs).

  Cek Jalur: adb shell find /system -name "*Wizard*.apk".

# jika terjadi error atau gagal flash rom 
  Format Data & System (Hapus sisa error):
  Bash

    adb shell "mke2fs -t ext4 -F /dev/block/bootdevice/by-name/userdata"
    adb shell "mke2fs -t ext4 -F /dev/block/bootdevice/by-name/system"
    # lanjut ke fase 1 

  Tips Tambahan: Selalu jalankan adb shell sync sebelum reboot agar perubahan permanen. HP Anda sekarang sudah menjadi "Server Headless" yang tangguh!

  support me cofe and Snacks :)
    
    # saweria:
    https://saweria.co/revanhd
    # ton: 
    UQChjikOpB7AlcSBfzKaK5ex-HzntzqbmwtKwmTrIpKZdx0u
    # pump:
    3W4jjH3GkCzienbR5YN4J3ZgE9cQkyLpZ9cD2HwRAGYh
    # btc: 
    bc1qfrsnqz5ly9nn6a4jngaev05nyvh70z4g2zk0ck

