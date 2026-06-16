# network-practice

Практика настройки сети через GitHub Actions: два эфемерных раннера пингуют друг друга через Tailscale mesh-VPN.

## Что здесь происходит

- Workflow поднимает **два параллельных джоба** — каждый это отдельная VM в облаке GitHub.
- Оба устанавливают [Tailscale](https://tailscale.com) и подключаются к твоей tailnet.
- После этого пингуют друг друга по Tailscale-IP (100.x.y.z) и видят `traceroute`.

По сути это **две VPS, соединённые зашифрованным туннелем**, на время выполнения джоба (макс 6 часов).

## Установка

### 1. Зарегай Tailscale
- Зайди на [login.tailscale.com](https://login.tailscale.com) (вход через GitHub).
- Создай tailnet (создаётся автоматически).

### 2. Сгенерируй auth key
- **Settings → Personal → Keys → Generate auth key**
- Reusable: **on** (чтобы можно было использовать в нескольких джобах)
- Ephemeral: **on** (чтобы ноды автоматически удалялись после отключения)
- Tags: по желанию
- Скопируй ключ (начинается с `tskey-...`).

### 3. Добавь ключ в секреты репо
- В репо: **Settings → Secrets and variables → Actions → New repository secret**
- Name: `TAILSCALE_KEY`
- Value: вставь свой `tskey-...`

### 4. Запусти workflow
- Вкладка **Actions** → **network-ping-practice** → **Run workflow**

## Что смотреть
- В **Actions** оба джоба выполняются параллельно.
- В логах каждого — вывод `tailscale status`, `ping`, `traceroute`.
- В [login.tailscale.com/admin/machines](https://login.tailscale.com/admin/machines) увидишь обе ноды с их 100.x IP.
