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

