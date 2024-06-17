Burada check_tracks.sh adında bir dosya oluşturak, tracks loglarını kontrol edecek, 
son 10 satırda 3 defa "ERR Error in SubmitPod" ifadesi varsa, servisi yeniden başlattıracak ve bunları log olarak check_tracks.log içerisine yazdıracağız

aşağıdaki komutları 
    
    nano /root/check_tracks.sh 
içerisine kaydediniz

    bash
    #!/bin/bash
    
    # chmod +x check_tracks.sh
    # /root/check_tracks.sh >> /root/check_tracks.log 2>&1
    # * */10 * * * bash /root/check_tracks.sh >> /root/check_tracks.log 2>&1
    # Log dosyasını kontrol eder ve son 10 satırda 3 kere belirtilen hata satırı varsa, servisi yeniden başlatır
    # tail -f check_tracks.log
    
    log_command="journalctl -u tracksd -n 10 --no-pager | grep 'ERR Error in SubmitPod' | wc -l' | wc -l"
    
    # Logları kontrol et
    log_count=$(eval $log_command)
    
    # Hata sayısı kontrolü
    if [ $log_count -ge 3 ]; then
        echo "Hata sayısı $log_count, servis yeniden başlatılıyor..."
        # Servisi yeniden başlatma komutu
        restart_command="systemctl restart tracksd"
        eval $restart_command
    else
        echo "Hata sayısı $log_count, servis yeniden başlatılmıyor."
    fi

çalışma izni veriniz

    chmod +x check_tracks.sh

10 dk da bir kontrol etmesi için crontab içerisinde en alta kaydediniz

    crontab -e

bunu

    * */10 * * * bash /root/check_tracks.sh >> /root/check_tracks.log 2>&1
