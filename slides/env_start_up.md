# Подготовка машины к работе

# Урок 1. Базовая настройка рабочей среды

Занятия будут проходить в ОС Ubuntu 18.04 LTS. Установка необходимых баз данных (Mongo, Postgres, Redis, etc.)
осуществляется с помощью утилиты виртуализации Docker, либо  прямо в систему Ubuntu. Доступны оба варианта, но докер более предпочтителен.

**Примечание:** Если у Вас MacOS - сойдёт, она похожа на Ubuntu и утилиты командной строки там примерно такие же, курс можно будет пройти.
Если у Вас Windows, то будет сложнее, но в целом тоже не критично - главное, чтобы было куда установить Postgres (обязательно!) и остальные дистрибутивы (Mongo, Redis), опционально. 

## Установка и настройка операционной системы Ubuntu

Ввесь курс можно проходить и без Ubuntu, но с трудностями винды или мака придётся разбираться при помощи коллег и интернетов, спикер курса не поможет.
Если ОС Ubuntu не является основной операционной системой, то можно установить её следующими способами 

* С помощью VirtualBox [по этой инструкции](http://profitraders.com/Ubuntu/VirtualBoxUbuntuInstall.html)
* с помощью ПО VMWare [по этой инструкции](https://www.quora.com/How-do-I-install-Ubuntu-using-VMware-on-Windows-10) или VirtualBox
* с помощью облачного сервиса Google Cloud [по этой инструкции](#ubuntu-google-cloud) или любого другого, например Яндекс.Облако.

После того, как убунта установлена (любым способом), нужно обновить список пакетов. Для этого запустим в консоли команду:

<pre>
sudo apt-get update && sudo apt-get -y upgrade;
</pre>

Эта команда обновит пакетный менеджер apt-get. После этого установить менеджер пакетов pip и вспомогательные утилиты (unzip, git):

<pre>
sudo apt-get install python-pip unzip git;
</pre>

Пакет pip - это менеджер пакетов python, его помощью можно будет устанавливать python библиотеки. Утилита unzip - программа для распаковки архивов.

Мы установили git не просто так - он нужен для того, чтобы скопировать учебный репозиторий с кодом этого курса.
Теперь скачиваем репозиторий курса - там хранятся материалы для домашних работ.

<pre>
git clone https://github.com/adzhumurat/data_management.git
</pre>

В рапозитории вы найдёте файл `data_management/data_store/movies_data.zip`, в котором хранятся `csv` и `json` файлы.
Для работы вам нужно извлечь эти файлы в директорию `data_management/data_store/raw_data` чтобы это сделать есть два способа

* умный и хороший способ: запустите команду `python3 data_tools/extract_zipped_data.py -s extract`
* **либо** создайте директорию `data_management/data_store/raw_data` в ручную и распакуйте туда данные с помощью любого распаковщика

Далее нужно отредактировать файл ~/.bashrc и установить там переменные среды. Для этого откроем файл с помощью редактора nano:
<pre>
nano ~/.bashrc
</pre>

**ВНИМАНИЕ** вам нужно указать **полный** путь до директории, куда вы распаковали файлы, не копируйте бездумно строчку ниже - поменяйте путь до директории data_store.

Нажимаем комбинацию **ctrl + V**, чтобы долистать до конца файла, затем копируем переменные и их значения и вставляем в открытый файл с помощью комбинации **ctrl + shift + V**
<pre>
export SOURCE_DATA="/home/adzhumurat/jupyter_notebooks/data_management/data_store"
</pre>

После этого закроем файл с помощью комбинации **ctrl + O**, нажать Enter, затем **ctrl + X**, подтвердить **Y**, затем **ctrl + X** и выполним в терминале команду source, чтобы применить изменения:
<pre>
source ~/.bashrc
</pre>

Эти действия мы совершили для того, чтобы более удобно настроить рабочую среду: скачать данные, залить их в Postgres и т.д.
Мы установили переменную среды **SOURCE_DATA** - туда, в эту директорию, будет распакован архив с данными, которые будем загружать в Postgres - это набор `csv` файлов.
Чтобы проверить, как применились изменения выполним в консоли команду 
<pre>
ls ${SOURCE_DATA}/raw_data
</pre>

Результат работы команды - должны увидеть в список файлов, которые только что распаковали
<pre>
dogs.json  links.csv  movies_metadata.csv  ratings.csv  tags.json
</pre> 

**Справка** команда *ls* "печатает" список файлов в директории, которая хранится в переменной среды *$SOURCE_DATA*, где значок *$* является служебным.

**Справка** для работы в консоли будем использовать базовые команды Linux

* Команда *sudo* позволяет запустить другие команды с правами Администратора системы
* Команда *mkdir* создаёт пустую директорию
* Команда *ls* печатает список файлов, которые находятся в директории.
* Команда *chmod 777* разрешает cоздание и удаление файлов из директории *$SOURCE_DATA* всем пользователям без исключения
* Команда *cd* позволяет сменить директорию.

## Работа с Docker

Для установки системы виртуализации Docker на официальном сайте есть прекрасные пошаговые инструкции для всех основных ОС

* [тут Linux](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
* [тут MacOS](https://docs.docker.com/docker-for-mac/install/)
* [тут Windows](https://docs.docker.com/docker-for-windows/install/)

Если у Windows, то для установки нужно использовать вот эту инструкцию:  https://docs.docker.com/toolbox/toolbox_install_windows/ 

Установим docker, согласно [инструкции для Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/).
Проверьте, что всё работает с помощью запуска команды
<pre>
docker run hello-world
</pre>

Если увидите ответное приветствие от Docker - готово, вы великолепны! Если не работает без  sudo - продолжайте настройку по инструкции.
Кроме докера поставим docker-compose

<pre>
sudo apt-get install docker-compose
</pre>

Подготовка завершена! Один раз проделав этот пункт, можно к нему больше не возвращаться

## Автоматизируем работу с Docker

Мы будем пользоваться СУБД Postgres и MongoDB, которые развернём в docker.
Для подробного знакомства с `docker` рекомендую пройти [мини-курс](https://github.com/adzhumurat/workshop_docker_beginner), по желанию.

### Автоматизация разворачивания среды с помощью docker-compose

Мы используем данные [The Movies Dataset](https://www.kaggle.com/rounakbanik/the-movies-dataset) c Kaggle - нужно стартовать среду, куда зальём "сырые" csv файлы с Kaggle.

* Проверьте директорию `data_store/pg_data` - она должна быть пустой
* запустите сборку контейнера для питоновского приложения с помощью команды `docker-compose --project-name data-prj -f docker-compose.yml build service-app`
* с помощью команды `docker images | grep data-prj_service-app` убедитесь, что был создан образ с именеи `data-prj_service-app`
* на всякий случай удалите все контейнеры, которые вы уже назапускали `docker rm -f $(docker container ls -q)`
* запустите загрузку данных в Postgres `docker-compose --project-name data-prj -f docker-compose.yml run --rm --name env-app service-app load`. Загрузка выполняется [скрипт для загрузки данных load_data.sh](../docker_compose/data_client/app/load_data.sh)  `docker-entrypint.sh`
* проверьте что данные в контейнер успешно загружены `docker-compose --project-name data-prj -f docker-compose.yml run --rm --name env-app service-app psql -h postgres_host -U postgres -c 'SELECT COUNT(*) as cnt FROM ratings'`
* Чтобы запустить python сервис запустите сборку python окружения `docker-compose --project-name data-prj -f docker-compose.yml run --rm --name env-app service-app pipenv`

### Запуск MongoDB

Монга будет запущена автоматически - это можно убедится, выполнив команду `docker ps | grep mongo`. Нужно только проверить её работоспособность, залив туда данные. 

* стартуем контейнер-клиент `docker-compose --project-name data-prj -f docker-compose.yml run --rm --name env-app service-app bash`
* запускаем импорт документов `/usr/bin/mongoimport --host $APP_MONGO_HOST --port $APP_MONGO_PORT --db movie --collection tags --file /usr/share/raw_data/tags.json` 

Чтобы "погасить" весь бэкенд, запустите команду
<pre>
docker-compose --project-name data-prj -f docker-compose.yml down
</pre>

### Бонус: устанавливаем jupyter notebook

Jupyter - это удобная визуальная среда для запуска Python приложений.

Чтобы установить jupyter в питоновское окружение, находясь в директории `docker_compose` выполните команду

<pre>
docker-compose --project-name data-prj -f docker-compose.yml run --rm --name env-app service-app pipenv
</pre> 

Должен запуститься процесс установки

<pre>
Installing dependencies from Pipfile.lock (8f86a9)…
  🐍   ▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉ 61/61 — 00:00:22
</pre>

Затем запустите команду старта jupyter ноутбука

<pre>
docker-compose --project-name data-prj -f docker-compose.yml run --rm -p 8888:8888 --name env-app service-app jupyter
</pre>

В консоли появится информация о запуке Юпитера
<pre>
[I 08:20:16.711 NotebookApp] Writing notebook server cookie secret to /root/.local/share/jupyter/runtime/notebook_cookie_secret
[I 08:20:16.950 NotebookApp] Serving notebooks from local directory: /www/app
[I 08:20:16.950 NotebookApp] 0 active kernels
[I 08:20:16.950 NotebookApp] The Jupyter Notebook is running at:
[I 08:20:16.950 NotebookApp] http://0.0.0.0:8888/?token=029e0ce949f5e7cad2d8be93f982f6f5fddb76c81df0353c
[I 08:20:16.950 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
[C 08:20:16.950 NotebookApp] 
    
    Copy/paste this URL into your browser when you connect for the first time,
    to login with a token:
        http://0.0.0.0:8888/?token=029e0ce949f5e7cad2d8be93f982f6f5fddb76c81df0353c
</pre>

Ссылку `http://0.0.0.0:8888/?token=029e0ce949f5e7cad2d8be93f982f6f5fddb76c81df0353c` нужно открыть в браузере и насладиться интерфейсом Jupyter.

## Решение проблем с docker

Если контейнер не стартует с ошибкой
<pre>
docker: Error response from daemon: Conflict. The container name "/some-postgres" is already in use by container "2a99cb6629b78e7b5b6747a9bd453263940127909d91c8517e9ee0b230e60768". You have to remove (or rename) that container to be able to reuse that name.
</pre>

То контейнер уже создан и можно стартовать его
<pre>
sudo docker start 2a99cb6629b78e7b5b6747a9bd453263940127909d91c8517e9ee0b230e60768
</pre>

Если не помогло - надо бы остановить все запущенные докер-контейнеры и удалить их

<pre>
sudo docker stop $(sudo docker ps -a -q)
sudo docker rm $(sudo docker ps -a -q)
</pre>

Удаление всех образов
<pre>
docker rmi $(docker images -q)
</pre>

# Ubuntu Google Cloud

Как установить - по инструкции отсюда: https://cloud.google.com/compute/docs/quickstart-linux

Внимание! В инструкции установка Debian, а нам нужна Ubuntu 18.04. Эта опция выбирается в меню Boot Disk

![выбор ОС](https://habrastorage.org/webt/vl/dt/3m/vldt3mgct8jq3n6n9oa3pmyug_a.png "boot disk")

После установики ваш инстанс можно будет найти на этой странице https://console.cloud.google.com/compute/instances

![страница с инстансами](https://habrastorage.org/webt/cb/fx/qz/cbfxqzxqcdo0atxs9eg_c-t3jby.png "Google cloud instances")
