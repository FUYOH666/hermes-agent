<p align="center">
  <img src="assets/banner.png" alt="Hermes Agent" width="100%">
</p>

# Hermes Agent ☤

<p align="center">
  <a href="https://hermes-agent.nousresearch.com/docs/"><img src="https://img.shields.io/badge/Docs-hermes--agent.nousresearch.com-FFD700?style=for-the-badge" alt="Документация"></a>
  <a href="https://github.com/NousResearch/hermes-agent/blob/main/LICENSE"><img src="https://img.shields.io/badge/License-MIT-green?style=for-the-badge" alt="Лицензия: MIT"></a>
</p>

**Агент с самообучением от [Nous Research](https://nousresearch.com).** Создаёт навыки из опыта, улучшает их в процессе работы, сохраняет память, ищет по прошлым диалогам. Работает с любыми OpenAI-совместимыми моделями.

**Наша конфигурация:** LLM на Linux GPU сервере (порт 8005, TailScale).

---

## Быстрый старт

```bash
cd /Users/aleksandrmordvinov/devs/hermes-agent
source .venv/bin/activate
hermes
```

Или установка с нуля:

```bash
curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash
source ~/.bashrc    # или source ~/.zshrc
hermes
```

---

## Конфигурация

### LLM (Custom endpoint, порт 8005)

На порту 8005 может работать любой OpenAI-совместимый сервис (vLLM, llama.cpp и т.д.). Модель настраивается в config.

| Параметр | Значение |
|----------|----------|
| **URL** | `http://<hostname>:8005/v1` (TailScale или localhost) |
| **Модель** | Задаётся в `~/.hermes/config.yaml` (пример: Qwen3.5-27B, до 256k токенов) |

Конфиг: `~/.hermes/.env` и `~/.hermes/config.yaml`

```bash
# ~/.hermes/.env
OPENAI_BASE_URL=http://<hostname>:8005/v1
OPENAI_API_KEY=EMPTY
LLM_MODEL=<имя_модели_из_сервера>
```

```yaml
# ~/.hermes/config.yaml  (model section)
model:
  default: "<имя_модели>"
  base_url: "http://<hostname>:8005/v1"
  provider: "custom"
```

### Тема (светлый терминал)

Используется скин **light-terminal** — тёмный текст на светлом фоне. Файл: `~/.hermes/skins/light-terminal.yaml`

Сменить тему в чате: `/skin light-terminal` или `/skin default`

---

## Основные команды

| Команда | Описание |
|---------|----------|
| `hermes` | Интерактивный чат |
| `hermes chat -q "вопрос"` | Один запрос без интерактива |
| `hermes model` | Смена провайдера/модели |
| `hermes gateway` | Шлюз (Telegram, Discord и т.п.) |
| `hermes doctor` | Проверка конфигурации |
| `hermes setup` | Мастер настройки |

---

## CLI vs Messaging Quick Reference

| Action | CLI | Messaging platforms |
|---------|-----|---------------------|
| Start chatting | `hermes` | Run `hermes gateway setup` + `hermes gateway start`, then send the bot a message |
| Start fresh conversation | `/new` or `/reset` | `/new` or `/reset` |
| Change model | `/model [provider:model]` | `/model [provider:model]` |
| Set a personality | `/personality [name]` | `/personality [name]` |
| Retry or undo the last turn | `/retry`, `/undo` | `/retry`, `/undo` |
| Compress context / check usage | `/compress`, `/usage`, `/insights [--days N]` | `/compress`, `/usage`, `/insights [days]` |
| Browse skills | `/skills` or `/<skill-name>` | `/skills` or `/<skill-name>` |
| Interrupt current work | `Ctrl+C` or send a new message | `/stop` or send a new message |
| Platform-specific status | `/platforms` | `/status`, `/sethome` |

---

## Горячие клавиши ввода

| Комбинация | Действие |
|------------|----------|
| **Ctrl+U** | Удалить от курсора до начала строки |
| **Ctrl+K** | Удалить от курсора до конца строки |
| **Ctrl+W** | Удалить слово слева |
| **Ctrl+A** | В начало строки |
| **Ctrl+E** | В конец строки |

---

## Запуск (без активации venv)

```bash
cd /Users/aleksandrmordvinov/devs/hermes-agent
.venv/bin/hermes
```

Или добавить в `~/.zshrc`:

```bash
alias hermes='/Users/aleksandrmordvinov/devs/hermes-agent/.venv/bin/hermes'
```

---

## Требования

- **TailScale** — для доступа к LLM (порт 8005)
- **Python 3.11+** с uv
- Проверка: `curl http://<hostname>:8005/health`

---

## Migrating from OpenClaw

If you're coming from OpenClaw, Hermes can automatically import your settings, memories, skills, and API keys.

```bash
hermes claw migrate              # Interactive migration (full preset)
hermes claw migrate --dry-run    # Preview what would be migrated
hermes claw migrate --preset user-data   # Migrate without secrets
hermes claw migrate --overwrite  # Overwrite existing conflicts
```

---

## Документация

Полная документация: [hermes-agent.nousresearch.com/docs](https://hermes-agent.nousresearch.com/docs/)

| Раздел | Содержание |
|--------|------------|
| [Configuration](https://hermes-agent.nousresearch.com/docs/user-guide/configuration) | Провайдеры, модели, custom endpoint |
| [CLI](https://hermes-agent.nousresearch.com/docs/user-guide/cli) | Команды, скины, сессии |
| [Messaging](https://hermes-agent.nousresearch.com/docs/user-guide/messaging) | Telegram, Discord, Slack |
| [Tools](https://hermes-agent.nousresearch.com/docs/user-guide/features/tools) | 40+ инструментов |
| [Skills](https://hermes-agent.nousresearch.com/docs/user-guide/features/skills) | Навыки и память |

---

## Разработка

```bash
git clone https://github.com/NousResearch/hermes-agent.git
cd hermes-agent
curl -LsSf https://astral.sh/uv/install.sh | sh
uv venv .venv --python 3.11
source .venv/bin/activate
uv pip install -e ".[all,dev]"
python -m pytest tests/ -q
```

> **RL Training (optional):** To work on the RL/Tinker-Atropos integration:
> ```bash
> git submodule update --init tinker-atropos
> uv pip install -e "./tinker-atropos"
> ```

---

## Лицензия

MIT — см. [LICENSE](LICENSE).

Оригинал: [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent)
