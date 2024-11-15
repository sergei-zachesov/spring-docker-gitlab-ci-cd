# Gitlab CI-CD для Spring Boot приложения развернутого в Docker

Подход основан на [статье](https://habr.com/ru/articles/764568/).

## 1. Установка и регистрация Gitlab Runner

Производится с помощью [репозитория](https://github.com/sergei-zachesov/docker-compose-gitlab-runner).

## 2. Настройка SSH-ключа и сервера

На стороне целевого сервера приложения.

* Генерация SSH-ключа: `ssh-keygen -t rsa -b 4096`
* Добавление публичного ключа в `authorized_keys`: `cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys`
* Копирования секретного ключа `cat ~/.ssh/id_rsa` и копирование его в репозиторий gitlab в Settings->CI/CD->Variables
  `$SSH_PRIVATE_KEY_PROD` или `$SSH_PRIVATE_KEY_TEST`. Возможно убрать `Protect variable`
* Удаление ключей сгенерированных файлов `rm id_rsa.pub`, `rm id_rsa`
* Установить доступ к `authorized_keys`: `chmod 600 ~/.ssh/authorized_keys`
* Настройка не `root` пользователя, на работу с `docker` без `sudo`.
  Подробнее https://docs.docker.com/engine/install/linux-postinstall/, https://askubuntu.com/a/477554, https://stackoverflow.com/questions/48957195/how-to-fix-docker-got-permission-denied-issue
    * Добавление группы: `sudo groupadd  docker`
    * Помещение юзера в группу: `sudo usermod -aG docker $USER`
    * Доступ к сокетам docker: sudo chmod 666 /var/run/docker.sock

## 3. Переменные окружения CI/CD

Устанавливаются в Settings->CI/CD->Variables. Отдельно для разных контуров. Возможно убрать `Protect variable`.

* `$SSH_PRIVATE_KEY_PROD(_TEST)` - секретный SSH-ключ
* `$SERVICE_NAME` - наименование сервиса
* `$HOST_PROD(_TEST)` - хост сервера
* `$USER_PROD(_TEST)` - юзер сервера