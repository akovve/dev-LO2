# Лабораторная работа №2: Автоматизация в Linux (Bash, JSON, Cron, Nginx)

## Цель работы
Написать bash-скрипт, принимающий город, выводящий температуру и влажность; настроить nginx; 
запускать скрипт по cron раз в минуту с записью результата в index.html

## 1. Установка ПО

```bash
sudo apt update
sudo apt install nginx jq curl -y
```

## 2. Создание index.html

```bash
sudo touch /var/www/html/index.html
sudo chmod a+w /var/www/html/index.html
ls -l /var/www/html/
```
<img width="856" height="106" alt="image" src="https://github.com/user-attachments/assets/3e7c1d66-a8a2-41f2-bc78-fa7727fb73e9" />


## 3. Проверка API погоды

```bash
curl -s --max-time 60 "wttr.in/Perm?format=j1" -o /tmp/weather.json
jq -r '.["current_condition"][0] | .temp_C, .humidity' /tmp/weather.json
```
<img width="1237" height="457" alt="image" src="https://github.com/user-attachments/assets/2b325e0f-bc22-4cc0-aa7c-903115c55c49" />

## 4. Скрипт ~/weatherperm.sh

```bash
nano ~/weatherperm.sh
chmod +x ~/weatherperm.sh
~/weatherperm.sh Perm
```

**Содержимое скрипта:**
```
#!/bin/bash

if [ -z "$1" ]; then
    echo "Использование: $0 <город>"
    exit 1
fi

CITY=$1
OUTPUT_FILE="/var/www/html/index.nginx-debian.html"

# Запрашиваем JSON с wttr.in
JSON_DATA=$(curl -s "wttr.in/${CITY}?format=j1")

# Извлекаем температуру и влажность
TEMP=$(echo "$JSON_DATA" | jq -r '.["current_condition"][0] | .temp_C')
HUMIDITY=$(echo "$JSON_DATA" | jq -r '.["current_condition"][0] | .humidity')

CURRENT_DATE=$(date)

# Записываем в index-файл nginx
{
  echo "<HTML><BODY>"
  echo "\"$TEMP\""
  echo "\"$HUMIDITY\""
  echo "$CURRENT_DATE"
  echo "</BODY></HTML>"
} > "$OUTPUT_FILE"

echo "Обновлено: $CITY, темп. $TEMP°C, влажность $HUMIDITY%"
```
<img width="1280" height="1124" alt="telegram-cloud-photo-size-2-5308004533734680099-y" src="https://github.com/user-attachments/assets/09addede-2fca-461e-8aa0-2e9ed3611517" />


## 5. Настройка и проверка cron

```bash
crontab -e
```

Добавлено:
```cron
* * * * * /home/ego/weatherperm.sh Perm > /dev/null 2>&1
```

```bash
service cron status
```
<img width="907" height="298" alt="image" src="https://github.com/user-attachments/assets/dd80793d-db1e-40a4-88a7-42dc9263f4b0" />

<img width="1280" height="660" alt="telegram-cloud-photo-size-2-5308004533734680118-y" src="https://github.com/user-attachments/assets/7e6362e7-5a67-48aa-ae81-c16cf78bee6c" />


## 6. Доказательство работы cron

Через 1–2 минуты после настройки cron:

```bash
curl 127.0.0.1 и 127..0.0.1:8080 в браузере
```
<img width="1020" height="356" alt="telegram-cloud-photo-size-2-5308004533734680113-y" src="https://github.com/user-attachments/assets/a65ff93b-0f8d-460f-9e75-86bd34138bac" />
<img width="741" height="88" alt="image" src="https://github.com/user-attachments/assets/a6e6ac51-ca3c-46af-93eb-423e5c925858" />
<img width="986" height="458" alt="image" src="https://github.com/user-attachments/assets/197e4fe8-c69e-4078-8362-d49621aeafcb" />


# Вывод
Освоены: bash-скрипты (аргументы, перенаправление потоков), парсинг JSON через jq, настройка nginx и прав доступа, планировщик cron в Linux. Все требования задания выполнены.
