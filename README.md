# Домашнее задание к занятию «Kubernetes. Причины появления. Команда kubectl» - Скрыпников Илья

## Задание 1. Установка MicroK8S

### 1. Установка MicroK8S
1. Обновление пакетов и установка `snapd`:
   ```bash
   sudo apt update
   sudo apt install snapd
   ```

2. Установка MicroK8S через snap:
   ```bash
   sudo snap install microk8s --classic
   ```

3. Добавление текущего пользователя в группу `microk8s`:
   ```bash
   sudo usermod -a -G microk8s $USER
   ```

4. Изменение прав на папку с конфигурацией Kubernetes:
   ```bash
   sudo chown -f -R $USER ~/.kube
   ```

5. Перезагрузка сессии (или выход и вход снова).

6. Проверка статуса MicroK8S:
   ```bash
   microk8s status --wait-ready
   ```

### 2. Установка Dashboard
1. Включение аддона Dashboard:
   ```bash
   microk8s enable dashboard
   ```

2. Проверка, что Dashboard работает:
   ```bash
   microk8s kubectl get all -n kube-system
   ```

### 3. Генерация сертификата для внешнего IP
1. Редактирование файла конфигурации:
   ```bash
   sudo nano /var/snap/microk8s/current/certs/csr.conf.template
   ```

2. Добавление внешнего IP-адреса в раздел `[ alt_names ]`:
   ```
   [ alt_names ]
   IP.4 = <ваш_внешний_IP>
   ```

3. Обновление сертификатов:
   ```bash
   sudo microk8s refresh-certs --cert front-proxy-client.crt
   ```

---

## Задание 2. Установка и настройка локального kubectl

### 1. Установка kubectl
1. Скачивание и установка `kubectl`:
   ```bash
   curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
   chmod +x ./kubectl
   sudo mv ./kubectl /usr/local/bin/kubectl
   ```

2. Проверка установки:
   ```bash
   kubectl version --client
   ```

### 2. Настройка подключения к кластеру
1. Получение конфигурации MicroK8S:
   ```bash
   microk8s config > ~/.kube/config
   ```

2. Проверка подключения:
   ```bash
   kubectl get nodes
   ```

### 3. Подключение к Dashboard через port-forward
1. Запуск проброса портов для Dashboard:
   ```bash
   kubectl port-forward -n kube-system service/kubernetes-dashboard 10443:443
   ```

2. Открытие Dashboard в браузере:
   ```
   https://localhost:10443
   ```

3. Использование токена для входа. Получение токена:
   ```bash
   kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep default-token | awk '{print $1}')
   ```

---

## Полезные команды
- Проверка статуса MicroK8S: `microk8s status --wait-ready`
- Включение аддонов: `microk8s enable <addon>`
- Список аддонов: `microk8s status`
- Получение конфигурации: `microk8s config`
```
