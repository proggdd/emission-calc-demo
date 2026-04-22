# Как выложить этот демо‑репо на GitHub

Короткий план — такой же, как для `proggdd/proggdd`, только имя репо другое.

## 1. Создать репо на GitHub

Через браузер: https://github.com/new
- **Repository name:** `emission-calc-demo`
- **Description:** `Sanitized demo of an industrial-emissions lab calculator: sampling act → live Excel calculation → protocol (docx/pdf)`
- **Visibility:** `Public`
- Галочку «Initialize this repository with a README» **СНЯТЬ**
- `.gitignore`: None (у меня свой уже лежит)
- License: MIT

Жми **Create repository**.

## 2. Открыть в VS Code ИМЕННО эту папку

VS Code → File → Open Folder… → выбери `поиск работы/emission-calc-demo`

⚠️ Не открывай `поиск работы/` целиком — тогда git init попадёт не туда.

## 3. Пуш через терминал VS Code

Ctrl+` (открыть терминал) — ты уже в нужной папке. Выполни:

```bash
# 1. инициализация
git init
git branch -M main

# 2. remote (замени URL если GitHub дал другой на шаге 1)
git remote add origin https://github.com/proggdd/emission-calc-demo.git

# 3. добавить всё (.gitignore отрежет временные файлы LibreOffice)
git add .

# 4. коммит
git commit -m "Sanitized emission-calculator portfolio demo"

# 5. пуш
git push -u origin main
```

Если `gh` уже авторизован — можно одной командой вместо шагов 2 и 5:

```bash
gh repo create emission-calc-demo --public --source=. --remote=origin --push
```

## 4. Добавить ссылку в главный README профиля

В `proggdd/proggdd/README.md` уже есть секция «What I'm building right now» — добавь туда пятый пункт:

```markdown
🧪 **[Emission-calc demo](https://github.com/proggdd/emission-calc-demo)** — sanitized Excel → Word pipeline from my lab years. GOST 33007, uncertainty at P=0.95, live formulas.
```

После этого профиль показывает три связанных артефакта: production-бот, его Python-миграцию, и лабораторный расчётный пример. Три независимые системы, три разных стека — по ним видно, что человек делает.

## 5. Проверь

Открой https://github.com/proggdd/emission-calc-demo — README должен показываться, файлы `calculator.xlsx`, `sampling_act.xlsx`, `protocol.docx`, `protocol.pdf` должны быть кликабельны и открываться предпросмотром прямо в GitHub.

---

**Этот файл (`GITHUB_SETUP.md`) пушить не надо** — шпаргалка только для тебя. Если хочешь, чтобы он не попал в пуш, добавь строку `GITHUB_SETUP.md` в `.gitignore` перед `git add .`.
