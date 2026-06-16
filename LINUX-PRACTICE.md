# Чек-лист: практика Linux

Запускай `free-vps.yml` в GitHub Actions, подключайся по SSH через tmate и проходи пункт за пунктом. Каждое задание — задача → команды → проверка.

---

## 🟢 Уровень 1. Навигация и файлы

- [ ] **Где я?** `pwd` → должен показать `/home/runner`
- [ ] **Что вокруг?** `ls -la` → увидишь скрытые файлы (`.bashrc`, `.profile`)
- [ ] **Дерево целиком** `ls -R /etc | head -50`
- [ ] **Создать структуру**
  ```bash
  mkdir -p ~/practice/{docs,scripts,logs}
  cd ~/practice && pwd
  ```
- [ ] **Создать файл и записать**
  ```bash
  echo "hello linux" > docs/note.txt
  cat docs/note.txt
  ```
- [ ] **Найти файл** `find / -name "note.txt" 2>/dev/null`
- [ ] **Найти по содержимому** `grep -r "hello" ~/practice/`
- [ ] **Скопировать / переименовать / удалить**
  ```bash
  cp docs/note.txt docs/note.bak
  mv docs/note.bak scripts/
  rm docs/note.txt
  ```
- [ ] **Размер и тип** `file scripts/note.bak && du -h scripts/note.bak`
- [ ] **Справка** `man ls` (выход: `q`)

---

## 🟡 Уровень 2. Пользователи и права

- [ ] **Кто я?** `whoami && id`
- [ ] **Кто в системе?** `cat /etc/passwd | grep -v nologin | grep -v false`
- [ ] **Создать пользователя**
  ```bash
  sudo useradd -m -s /bin/bash testuser
  sudo passwd testuser
  ```
- [ ] **Стать им** `sudo -i -u testuser` (вернуться: `exit`)
- [ ] **Группы** `groups testuser` и `sudo usermod -aG sudo testuser`
- [ ] **Создать файл и поиграть с правами**
  ```bash
  touch ~/practice/secret.txt
  ls -la ~/practice/secret.txt
  chmod 600 ~/practice/secret.txt
  chmod 644 ~/practice/secret.txt
  chmod 755 ~/practice/scripts
  ```
- [ ] **Сменить владельца**
  ```bash
  sudo chown testuser:testuser ~/practice/secret.txt
  ls -la ~/practice/secret.txt
  ```
- [ ] **Проверить права**
  ```bash
  sudo -u testuser cat ~/practice/secret.txt
  chmod 000 ~/practice/secret.txt
  sudo -u testuser cat ~/practice/secret.txt
  chmod 644 ~/practice/secret.txt
  ```

📌 **Цифры прав**: `4=read 2=write 1=execute`. Например `755 = rwx r-x r-x`.

---

## 🟠 Уровень 3. Процессы и сервисы

- [ ] **Что запущено?** `ps aux | head -20`
- [ ] **Топ процессов** `top` (выход: `q`) или `htop`
- [ ] **Найти процесс** `pgrep -a sshd`
- [ ] **Убить процесс**
  ```bash
  sleep 300 &
  PID=$!
  kill $PID
  ```
- [ ] **Список сервисов** `systemctl list-units --type=service --state=running`
- [ ] **Статус sshd** `systemctl status sshd`
- [ ] **Логи в реальном времени** `sudo journalctl -u sshd -f` (выход: Ctrl+C)
- [ ] **Поставить nginx**
  ```bash
  sudo apt update
  sudo apt install -y nginx
  sudo systemctl status nginx
  curl localhost
  sudo systemctl stop nginx
  sudo systemctl start nginx
  ```

---

## 🔴 Уровень 4. Сеть

- [ ] **Мои интерфейсы** `ip a`
- [ ] **Маршрут по умолчанию** `ip route`
- [ ] **Соседи по L2** `ip neigh`
- [ ] **Открытые порты** `ss -tulnp`
- [ ] **Внешний IP** `curl ifconfig.me && curl -6 ifconfig.co`
- [ ] **DNS-резолв** `dig github.com`
- [ ] **Свой мини-сервер**
  ```bash
  cd ~/practice
  python3 -m http.server 8000 &
  curl http://localhost:8000
  ```
- [ ] **Пинговать** `ping -c 5 8.8.8.8`
- [ ] **Скачать файл** `curl -O https://example.com && head index.html && rm index.html`

---

## 🟣 Уровень 5. Пакеты

- [ ] **Обновить индекс** `sudo apt update`
- [ ] **Поиск** `apt search htop`
- [ ] **Инфо** `apt show htop`
- [ ] **Поставить** `sudo apt install -y htop tmux mc`
- [ ] **Что установлено** `dpkg -l | grep -E "htop|tmux"`
- [ ] **Удалить** `sudo apt remove -y mc`

---

## ⚫ Уровень 6. Диски и ФС

- [ ] **Размер диска** `df -h`
- [ ] **Размер папки** `du -sh /var/log`
- [ ] **Что примонтировано** `mount | column -t`
- [ ] **Блочные устройства** `lsblk`
- [ ] **Создать файл** `dd if=/dev/zero of=~/practice/big.bin bs=1M count=100 status=progress`

---

## 🟤 Уровень 7. Текст и пайпы

- [ ] **Грепать логи** `sudo journalctl | grep -i error | head`
- [ ] **Считать строки** `wc -l /etc/passwd`
- [ ] **Вырезать колонки** `cut -d: -f1 /etc/passwd | head`
- [ ] **Сортировка** `cut -d: -f7 /etc/passwd | sort -u`
- [ ] **awk** `awk -F: '{print $1 " → " $7}' /etc/passwd`
- [ ] **sed** `echo "old" > ~/practice/t.txt && sed -i 's/old/new/' ~/practice/t.txt`
- [ ] **Пайплайн** `sudo journalctl -u sshd | grep -oE 'from [0-9.]+' | sort | uniq -c | sort -rn | head`

---

## ⚙️ Уровень 8. SSH

- [ ] **Сгенерить ключ** `ssh-keygen -t ed25519`
- [ ] **Подключиться к себе** `ssh localhost`
- [ ] **Только ключи** (в `/etc/ssh/sshd_config`):
  ```
  PasswordAuthentication no
  ```
  `sudo systemctl restart sshd`
- [ ] **Конфиг клиента** — `~/.ssh/config`:
  ```
  Host myhost
    HostName localhost
    User runner
    IdentityFile ~/.ssh/id_ed25519
  ```
- [ ] **Туннель** `ssh -L 8888:localhost:8000 myhost` + `curl localhost:8888`

---

## 🛡 Уровень 9. Файрвол

- [ ] **Правила** `sudo iptables -L -n -v --line-numbers`
- [ ] **Блок ICMP**
  ```bash
  sudo iptables -A INPUT -p icmp --icmp-type echo-request -j DROP
  ping -c 3 127.0.0.1
  sudo iptables -D INPUT -p icmp --icmp-type echo-request -j DROP
  ```
- [ ] **UFW** `sudo ufw allow 22/tcp && sudo ufw enable && sudo ufw status`

---

## ⏰ Уровень 10. Cron и systemd

- [ ] **Cron** `crontab -e`:
  ```
  * * * * * echo "tick $(date)" >> ~/practice/logs/cron.log
  ```
- [ ] **Свой сервис** `/etc/systemd/system/mytimer.service`:
  ```ini
  [Unit]
  Description=My demo service
  [Service]
  ExecStart=/bin/bash -c 'echo hello from systemd $(date) >> /tmp/myservice.log'
  Type=oneshot
  ```
  `sudo systemctl start mytimer.service`

---

## 🧪 Челленджи

1. **Мини веб-сервер**: nginx + iptables + ufw + curl
2. **Ловец логов**: однострочник — успешные логины sshd по IP, топ-3
3. **Супер-юзер**: создать `devops`, sudo без пароля, только ключ
4. **Мини-мониторинг**: cron-скрипт раз в минуту пишет RAM / disk / процессы

---

## 🧹 Почистить
```bash
sudo userdel -r testuser
sudo apt purge -y nginx
crontab -r 2>/dev/null
rm -rf ~/practice
sudo apt autoremove -y
```
