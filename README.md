# Ansible infrastructure playbooks

Данные плейбуки устанавливают и включают Nginx и Docker, а также управляют явно заданными учетными записями пользователей на серверах под управлением Ubuntu и Astra Linux. Оба дистрибутива используют пакетный модуль Ansible для семейства Debian; Docker устанавливается из пакета `docker.io`, входящего в состав дистрибутива.

## Требования

- Ansible Core на управляющей машине
- SSH-доступ к целевым серверам
- Коллекция `ansible.posix` для управления авторизованными SSH-ключами:
  `ansible-galaxy collection install ansible.posix`
- Учетная запись на целевом сервере с правами sudo

## Настройка инвентаря

Отредактируйте `inventory/hosts.ini` и замените пример адреса и SSH-пользователя на свои. Адрес `192.0.2.10` зарезервирован для документации и не является реальным сервером.

## Настройка пользователей

Задайте `managed_users` в `group_vars/all.yml` (или в файле `group_vars` для конкретного окружения) — это список учетных записей, которые Ansible должен создать или проверить на наличие. Пример:

```yaml
managed_users:
  - name: deploy
    comment: Deployment account
    groups:
      - sudo
    ssh_public_key: "ssh-ed25519 AAAA... replace-with-your-public-key"
```

Здесь должны быть только публичные SSH-ключи; Поле groups можно опустить, если дополнительные группы не нужны. Переменная docker_users по умолчанию пуста. Членство в группе Docker дает доступ, эквивалентный правам root, поэтому добавляйте туда учетные записи только если это действительно необходимо.

## Run

Сначала проверьте подключение и конфигурацию:

```sh
ansible all -m ansible.builtin.ping
ansible-playbook --syntax-check playbooks/site.yml
```

Запуск всех плейбуков или отдельного плейбука из этой директории:

```sh
ansible-playbook playbooks/site.yml --ask-become-pass
ansible-playbook playbooks/nginx.yml --ask-become-pass
ansible-playbook playbooks/docker.yml --ask-become-pass
ansible-playbook playbooks/users.yml --ask-become-pass
```
