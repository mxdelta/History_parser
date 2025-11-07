# History_parser

    В .zshrc добавляем в конец файла
    # === Настройка истории команд с метками времени ===

    HISTFILE=~/logs/command_history.zsh
    HISTSIZE=100000
    SAVEHIST=100000
    setopt EXTENDED_HISTORY
    setopt APPEND_HISTORY
    setopt INC_APPEND_HISTORY
    unsetopt HIST_IGNORE_DUPS

    Создаем папку для логов
    mkdir -p ~/logs
    
*********************************************

    cat history_parser.sh        
    #!/bin/zsh

    LOGFILE=~/logs/command_history.zsh

    if [ ! -f "$LOGFILE" ]; then
      echo "❌ Лог-файл не найден: $LOGFILE"
    exit 1
    fi

    echo "📜 Команды из лога:"
    echo "---------------------------"

    awk -F';' '
    /^: [0-9]+:/ {
    timestamp = substr($1, 3) + 0
    cmd = $2
    cmdtime = strftime("%Y-%m-%d %H:%M:%S", timestamp)
    printf "[%s] %s\n", cmdtime, cmd
    }
    ' "$LOGFILE"

**********************************************************************
vim scan_lan.sh
chmod +x scan_lan.sh

**********************
# Указать сеть и интерфейс!!!!!!!!!!!!

dnsrecon -r 192.168.50.0/24 -t rvl -c dnsrecon_results.csv
grep -E "([0-9]{1,3}\.){3}[0-9]{1,3}" dnsrecon_results.csv | awk -F, '{print $3}' | sort -u > target_ips.txt
sudo masscan -iL target_ips.txt -p1-65535 --rate=5000 --interface wlan0 | grep "Discovered open port" | awk '{print $6, $4}' | sed 's/\/tcp//g' | sort -V | awk '
{
    ip = $1;
    port = $2;
    if (ports[ip] == "") {
        ports[ip] = port;
    } else {
        ports[ip] = ports[ip] "," port;
    }
}
END {
    for (ip in ports) {
        print "nmap -sV -sC -A -p " ports[ip] " " ip " -oX scan_" ip ".xml";
    }
}' > nmap_commands.sh
chmod +x nmap_commands.sh
sh ./nmap_commands.sh
# sudo docker compose up 
