# Ansible роль: k8s_cluster

## Описание
Основной репозиторий [kube_project]<https://github.com/Alvald1/kube_project>
Роль `k8s_cluster` предназначена для автоматизированного развертывания Kubernetes-кластера на базе Debian 12 (bookworm) с поддержкой различных контейнерных движков (CRI-O, containerd, Docker+cri-dockerd), а также с возможностью настройки High Availability (HA) control-plane с помощью keepalived и haproxy. Роль поддерживает деплой как HA-кластера, так и одновузлового кластера, а также установку ingress-контроллера и тестового nginx-приложения.

## Возможности

- Установка и настройка Kubernetes (kubeadm, kubelet, kubectl)
- Выбор контейнерного движка: CRI-O, containerd, Docker+cri-dockerd
- Настройка HA control-plane (keepalived, haproxy)
- Автоматическая инициализация кластера и join-нод
- Установка ingress-nginx через Helm
- Деплой CNI (Flannel)
- Поддержка приватных реестров и кастомных сетевых настроек

## Структура роли

- `tasks/` — основные задачи по установке и настройке кластера
- `defaults/main.yml` — переменные по умолчанию (IP, версии, параметры сети)
- `vars/main.yml` — дополнительные переменные (репозитории, пакеты)
- `templates/` — шаблоны конфигураций (если есть)
- `handlers/` — обработчики (если есть)

## Основные переменные

- `branch_mode`: выбор контейнерного движка (`0` — cri-o, `1` — containerd, `2` — Docker+cri-dockerd)
- `network_prefix`, `ip_base`, `ip_base_virtual`: параметры адресации
- `k8s_version`, `crio_version`, `containerd_version`, `runc_version`, `CNI_version`, `cri_docker_version`: версии компонентов
- `pod_network`: CIDR для pod-сети
- `api_port`, `control_panel_port`: порты API и control-plane

Все переменные можно переопределять через `group_vars` или при запуске playbook.

## Пример использования

1. Подготовьте инвентарь (`inventory.ini`) с группами `control_panel` и `workers`.
2. Укажите нужные переменные в `defaults/main.yml` или через `branch_var.yml`.
3. Запустите роль через playbook:

```yaml
- name: Deploy Kubernetes cluster
  hosts: all
  become: true
  vars_files:
    - vault.yml
  roles:
    - k8s_cluster
```

4. Запуск playbook:

```bash
ansible-playbook -i inventory.ini playbook.yml
```

## Требования

- Ansible 2.10+
- Debian 12 (bookworm) на всех узлах
- Доступ по SSH с правами sudo

## Примечания

- Для HA-кластера используйте `branch_mode: 2` и настройте группу `control_panel` из нескольких узлов.
- Для одновузлового кластера используйте `branch_mode: 0` или `1`.
- Все действия выполняются автоматически, ручное вмешательство не требуется.

## Авторы

- alvald1
