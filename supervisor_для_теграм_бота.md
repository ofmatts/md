# Тут лежит небольшой гайд о том, как разместить своего бота на сервере с помощью supervisor
*Инструкция для тех, кто уже настроил сервер, написал бота и готов его выложить на ПРОМ*

## Клонирование репозитория

Перемещаемся в директорию, в которой будет лежать наш бот
```
cd <path_bots>
```
Клонируем репозиторий
```
git clone <link_you_repositoriy>
```

Переходим в директорию, в которой будет развернуто виртуальное окружение (в моем случае это сам бот)
```
cd <path_bots>/<path_bot>
```

Устанавливаем виртуальное окружение
```
sudo apt install python3.11-venv
```

Создаем виртуальное окружение
```
python3.11 -m venv .env
```

Активируем виртуальное окружение
```
source .env/bin/activate
```

Устанавливаем библиотеки для бота. Если у вас есть freeze файлы:
```
pip install -r requirements.txt
```
Если вы не делали freeze, то просто руками ставим нужные вам библиотеки

Далее запускаем вашего бота и тестируем, что все установилось корректно

##  Настройка supervisor

Устанавливаем supervisor, если он еще не установлен:
```
sudo apt-get install supervisor
```

Создаем конфиг, который будет стартовать и поддерживать нашего бота. Файл должен лежать в директории ```/etc/supervisor/``` и быть с расширением ```conf```
```
sudo vim /etc/supervisor/conf.d/start_bot.conf
```

###Прописываем сам конфиг

Название скрипта ```[program:my_bot]```

Команда запуска ```command=python script.py```

Пользователь от чьего имени будет запущен скрипт ```user=<user_name>```

Количество процессов, которые нужно стартовать ```numprocs=<num>```

Нужно ли запускать процесс после перезагрузки ```autostart=true```

Нужно ли запускать процесс после падения ```autorestart=true```

Нужно ли перенаправлять вывод ошибок скрипта в вывод supervisor ```redirect_stderr=true```

У меня получился такой конфиг
```
[program:my_bot]
command=/home/hubble_dj/programs/BotTelegram/.env/bin/python3.11 /home/hubble_dj/programs/BotTelegram/main.py
user=hubble_dj
numprocs=1
autostart=true
autorestart=true
redirect_stderr=true
```


Запускаем supervisor
```
sudo supervisord
```

Читаем конфиги
```
sudo supervisorctl reread
```

Проверяем статус процессов
```
sudo supervisorctl status
```

Мы должны увидеть наш процесс в одном из статусов ```STOPPED``` или ```RUNNING```

Если статус ```RUNNING``` - радуемся и тестим

Если статус ```STOPPED``` - запукаем процессы
```
sudo supervisorctl start all
```

Если нужно остановить процесс
```
sudo supervisorctl stop all
```

Кст, чтобы перезагрузить сервер пользуемся
 ```
sudo shutdown -r now
```
