## Описание задания

Вариант 6.   
Тема: Скачивание файла с проверкой через curl   
Требования к скрипту:
* Скачивает файл с URL (например, JSON или текстовый файл) через curl
* Проверяет, что файл успешно загружен (статус 200)
* Сохраняет файл с датой в имени: downloaded-file-YYYY-MM-DD-HH-MM-SS.json
* Если ошибка — записывает ее в лог
## Решения задания

``` bash
#!/bin/bash
URL="https://schedule.uust.ru/"
LOG_FILE="./download.log"
TIMESTAMP=$(date '+%Y-%m-%d-%H-%M-%S')
FILENAME="downloaded-file-${TIMESTAMP}.json"

echo "Download from $URL"
echo "$(date '+%Y-%m-%d-%H-%M-%S') Download start: $URL" >> "$LOG_FILE"

http_code=$(curl -s -w "%{http_code}" -o "$FILENAME" "$URL")

if [ "$http_code" -eq 200 ]; then
		echo "$(date '+%Y-%m-%d-%H-%M-%S') - File downloaded: $FILENAME" >> "$LOG_FILE"
		echo "File downloaded: $FILENAME"
		exit 0
else
		echo "$(date '+%Y-%m-%d-%H-%M-%S') - Download failed: HTTP $http_code" >> "$LOG_FILE"
		echo "Download failed: HTTP $http_code"
		exit 1
fi
```

В данном коде:
* `URL="https://schedule.uust.ru/"` - ссылка на сайт, с которого мы желаем скачать файл
* `LOG_FILE="./download.log"` - файл, в которую будут записываться все действия, совершенные скриптом
* `TIMESTAMP=$(date '+%Y-%m-%d-%H-%M-%S')` - время запуска скрипта
* `FILENAME="downloaded-file-${TIMESTAMP}.json"` - название файла, в который будет сохраняться скачанные данные
* `echo "Download from $URL"` - вывод в командную строку о начале скачивания файла из указанного сайта
* `echo "$(date '+%Y-%m-%d-%H-%M-%S') Download start: $URL" >> "$LOG_FILE"` - запись в логах о начале скачивания файла из указанного сайта
* `http_code=$(curl -s -w "%{http_code}" -o "$FILENAME" "$URL")` - получение статус кода ответа на запрос
* `if [ "$http_code" -eq 200 ]; then` - условие на получение ответа на запроса со статус кодом 200
* `echo "$(date '+%Y-%m-%d-%H-%M-%S') - File downloaded: $FILENAME" >> "$LOG_FILE"` - запись в логах об успешной загрузки
* `echo "File downloaded: $FILENAME"` - вывод в командую строку об успешной загрузке
* `exit 0` - выход из скрипта
* `else` - блок, который запускается при какой-либо ошибки при скачивании
* `echo "$(date '+%Y-%m-%d-%H-%M-%S') - Download failed: HTTP $http_code" >> "$LOG_FILE"` - запись в логах об ошибке при скачивании
* `echo "Download failed: HTTP $http_code"` - вывод в командную строку об ошибке при скачивании
* `exit 1` - выход из скрипта 
* `fi` - конец условной конструкции
