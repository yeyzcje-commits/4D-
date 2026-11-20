# Lumen UI Library

**Languages:** [Русский](#-русский) · [English](#-english)

A Rayfield-style GUI library for Roblox script executors: you describe your functions in a few lines of Lua, and buttons, toggles, sliders, dropdowns, keybinds and colour pickers appear. Includes animated intro, glass blur behind the window only, notifications with typing text, saved settings, and an info bar.

Библиотека GUI в стиле Rayfield для исполнителей Roblox: ты описываешь функции в несколько строк Lua, и появляются кнопки, переключатели, слайдеры, выпадающие списки, кейбинды и выбор цвета.

| File / Файл | What it is / Что это |
|---|---|
| `Lumen.lua` | **The library (API).** Load this one from your own scripts. / **Библиотека (API).** Подключай её в своих скриптах. |
| `Example.lua` | Short example using every element. / Короткий пример со всеми элементами. |
| `Lumen_demo.lua` | Library + big test menu (every button and variant). / Библиотека + большое тестовое меню. |
| `Hub.lua` | Universal hub, loads `Lumen.lua` from a file or URL. / Универсальный хаб, грузит `Lumen.lua` из файла или по ссылке. |
| `Lumen_Hub.lua` | Same hub with the library built in (one paste). / Тот же хаб с вшитой библиотекой (вставил и запустил). |

---

# 🇷🇺 Русский

## Содержание
1. [Установка](#установка)
2. [Быстрый старт](#быстрый-старт)
3. [Окно: `CreateWindow`](#окно-createwindow)
4. [Вкладки и элементы](#вкладки-и-элементы)
5. [Уведомления](#уведомления)
6. [Как вызывать функции из кода](#как-вызывать-функции-из-кода)
7. [Flags и сохранение настроек](#flags-и-сохранение-настроек)
8. [Иконки и картинки](#иконки-и-картинки)
9. [Панели: боковые, верхние и нижние окна](#панели-боковые-верхние-и-нижние-окна)
10. [Виджеты: урон по телу, хил, таймеры, полоски](#виджеты-урон-по-телу-хил-таймеры-полоски)
11. [Встроенные настройки и память игрока](#встроенные-настройки-и-память-игрока)
12. [Хаб `Hub.lua`: свои функции под игру](#хаб-hublua-свои-функции-под-игру)
13. [Совместимость и решение проблем](#совместимость-и-решение-проблем)

## Установка

Выбери один из способов.

**1. Из файла** (положи `Lumen.lua` в папку `workspace` исполнителя):
```lua
local Lumen = loadstring(readfile("Lumen.lua"))()
```

**2. По ссылке** (выложи `Lumen.lua` на GitHub и возьми ссылку Raw):
```lua
local Lumen = loadstring(game:HttpGet("https://raw.githubusercontent.com/<ты>/<репозиторий>/main/Lumen.lua"))()
```

**3. Вставкой.** Вставь весь текст `Lumen.lua` в начало скрипта как `local Lumen = (function() ... end)()`, либо просто запусти `Lumen_demo.lua` / `Lumen_Hub.lua`, там библиотека уже внутри.

## Быстрый старт

```lua
local Lumen = loadstring(readfile("Lumen.lua"))()

local Window = Lumen:CreateWindow({
    Name = "Мой скрипт",
    ScriptName = "Мой скрипт",       -- показывается на плашке сверху
    Subtitle = "v1.0",
    ConfigurationSaving = { Enabled = true, FolderName = "MyScript", FileName = "config" },
})

local Tab = Window:CreateTab("Main", "home")     -- имя вкладки и иконка

Tab:CreateButton({
    Name = "Привет",
    Callback = function()
        Lumen:Notify({ Title = "Привет", Content = "Кнопка нажата", Type = "success" })
    end,
})

Tab:CreateToggle({
    Name = "Бесконечный прыжок", CurrentValue = false, Flag = "infjump",
    Callback = function(on) print("включено:", on) end,
})
```

Правило простое: **описал элемент с `Callback`, и он появился в окне.** Когда пользователь трогает элемент, вызывается твоя функция с новым значением.

## Окно: `CreateWindow`

`Lumen:CreateWindow(options)` возвращает объект окна.

| Параметр | Тип | По умолчанию | Описание |
|---|---|---|---|
| `Name` (или `Title`) | string | `"Lumen"` | Название. Первые две буквы попадают в логотип. |
| `ScriptName` | string | `Subtitle` или `Name` | Название скрипта на плашке сверху. |
| `Subtitle` | string | нет | Серый текст рядом с названием (версия, игра). |
| `Accent` | Color3 | белый | Акцентный цвет. |
| `ToggleKey` | KeyCode | `RightShift` | Клавиша скрыть/показать окно. |
| `Size` | UDim2 (offset) | `580x390` | Размер. На маленьких экранах уменьшается сам. |
| `Transparency` | number 0–1 | `0.1` | Прозрачность фона окна. |
| `Scale` | number | `1` | Масштаб интерфейса. |
| `Blur` | boolean | `true` | Блюр только под окном. |
| `Intro` | boolean | `true` | Анимация запуска (текст, потом линия, потом окно). |
| `IntroText` / `LoadingTitle` | string | `Name` | Текст интро. |
| `InfoBar` / `FPS` | boolean | `true` | Плашка сверху: аватар, ник, скрипт, инжектор, FPS, пинг, игроки. Клик по ней скрывает и показывает окно. |
| `TabStyle` | `"Expand"` | обычный | `"Expand"`: вкладки это иконки, выбранная показывает название. Нужен, если вкладок много. |
| `ConfigurationSaving` | table | выкл. | `{ Enabled = true, FolderName = "...", FileName = "..." }` |
| `SettingsTab` | boolean | `true` | Встроенная вкладка **Settings** (см. ниже). `false` убирает её. |
| `ReturningText` | string | авто | Текст интро для вернувшегося игрока. Подстановки: `{nick}`, `{intro}`. |
| `ReturningIntro` | boolean | `true` | `false` отключает приветствие «Hello again». |

### Методы окна

| Метод | Что делает |
|---|---|
| `Window:CreateTab(name, icon)` | Создаёт вкладку. Возвращает объект вкладки. |
| `Window:Notify(options)` | Уведомление (то же, что `Lumen:Notify`). |
| `Window:SetVisible(bool)` | Показать или скрыть окно (с анимацией). |
| `Window:SetAccent(Color3)` | Сменить акцентный цвет вживую. |
| `Window:SetBlur(bool)` | Включить или выключить блюр. |
| `Window:SetTransparency(0..1)` | Прозрачность окна. |
| `Window:SetScale(number)` | Масштаб (0.6–1.4). |
| `Window:SetFPS(bool)` | Показать или скрыть плашку сверху. |
| `Window:OnDestroy(function)` | Функция, которая вызовется при закрытии GUI (удобно для очистки). |
| `Window:Destroy()` | Закрыть GUI (анимация: окно сжимается, потом закрывается плашка). |
| `Window:CreatePanel(options)` | Боковое, верхнее или нижнее окно (см. «Панели»). |
| `Window:GetPanel(id)` | Найти уже созданную панель. |
| `Window:CreateBodyPanel(options)` | Панель с моделью тела и индикатором хила. |
| `Window:CreateTimer(options)` | Таймер раунда, обратный отсчёт или секундомер. |
| `Window:SetInfoBarSide(side)` | Плашка: `"Top"`, `"Bottom"`, `"Left"`, `"Right"` или `"Floating"` (отдельное окно). |
| `Window:SetToggleKey(key)` | Сменить клавишу скрытия окна. |
| `Window:SetNotifySide("Left"/"Right")` | Сторона уведомлений. |
| `Window:ResetLayout()` | Вернуть окно и панели на место. |

## Вкладки и элементы

Вкладка создаётся так: `local Tab = Window:CreateTab("Название", "иконка")`.
Иконка необязательна (список иконок ниже). У каждого элемента тоже можно указать `Icon = "..."`.

Все методы есть в двух вариантах: `CreateX` (как в Rayfield) и `AddX` (одно и то же).

### Общие поля
| Поле | Описание |
|---|---|
| `Name` | Подпись элемента. |
| `Icon` | Иконка слева (необязательно). |
| `Flag` | Уникальное имя. Включает сохранение значения и доступ через `Lumen:GetFlag`. |
| `Callback` | Твоя функция. Что в неё приходит, смотри в таблице ниже. |

### Таблица элементов

| Метод | Основные поля | Что получает `Callback` | Что возвращает |
|---|---|---|---|
| `CreateSection(text)` | текст | — | — |
| `CreateLabel(text)` | текст | — | `:Set(text)` |
| `CreateParagraph{Title, Content}` | заголовок, текст | — | `:Set{Title, Content}` |
| `CreateButton` | `Name`, `Callback` | ничего | `:Fire()` |
| `CreateToggle` | `CurrentValue` (bool) | `boolean` | `:Set(v, silent)`, `:Get()` |
| `CreateSlider` | `Range = {min, max}`, `Increment`, `Suffix`, `CurrentValue` | `number` | `:Set(v, silent)`, `:Get()` |
| `CreateDropdown` | `Options` (список), `CurrentOption` (список), `MultipleOptions` | **таблица** выбранных | `:Set(v, silent)`, `:Get()`, `:Refresh(options, keep)` |
| `CreateInput` | `CurrentValue`, `PlaceholderText`, `RemoveTextAfterFocusLost` | `text, enterPressed` | `:Set(text, silent)`, `:Get()` |
| `CreateKeybind` | `CurrentKeybind` (имя клавиши, например `"E"`), `HoldToInteract` | ничего; при `HoldToInteract = true` приходит `true` (зажал) и `false` (отпустил) | `:Set(key)`, `:Get()` |
| `CreateColorPicker` | `Color` (Color3) | `Color3` | `:Set(color, silent)`, `:Get()` |
| `CreateProfile` | `Title`, `Subtitle`, `Line`, `Image` или `Icon`, `Circle` | — | `:Set{...}` |

### Примеры

```lua
-- Слайдер с дробным шагом
Tab:CreateSlider({
    Name = "Громкость", Range = { 0, 1 }, Increment = 0.05, CurrentValue = 0.5,
    Callback = function(v) print(v) end,
})

-- Выпадающий список: в Callback приходит ТАБЛИЦА
Tab:CreateDropdown({
    Name = "Режим", Options = { "Лёгкий", "Обычный", "Сложный" }, CurrentOption = { "Обычный" },
    Callback = function(selected) print(selected[1]) end,
})

-- Множественный выбор
Tab:CreateDropdown({
    Name = "Цели", Options = { "Игроки", "NPC", "Боссы" }, CurrentOption = { "Игроки" },
    MultipleOptions = true,
    Callback = function(selected) print(table.concat(selected, ", ")) end,
})

-- Кейбинд, который работает пока зажат
Tab:CreateKeybind({
    Name = "Ускорение", CurrentKeybind = "LeftShift", HoldToInteract = true,
    Callback = function(held) print(held and "зажато" or "отпущено") end,
})

-- Карточка с картинкой (аватар игрока по id)
Tab:CreateProfile({
    Title = "Автор", Subtitle = "Создатель", Line = "discord.gg/invite", Circle = true,
    Image = "rbxthumb://type=AvatarHeadShot&id=1&w=150&h=150",
})
```

## Уведомления

```lua
Lumen:Notify({
    Title = "Заголовок",
    Content = "Текст уведомления",     -- печатается по буквам
    Duration = 4,                       -- секунды (по умолчанию 4)
    Type = "success",                   -- "info" | "success" | "warning" | "error"
    Icon = "eye",                       -- необязательно: своя иконка
})
```
Клик по карточке закрывает её раньше. Уведомления можно вызывать и до окончания интро: они подождут и появятся после него.

## Как вызывать функции из кода

Каждый элемент возвращает объект. Сохрани его в переменную и управляй им из кода.

```lua
local speed = Tab:CreateSlider({ Name = "Скорость", Range = {16, 200}, CurrentValue = 16,
    Flag = "speed", Callback = function(v) print("speed", v) end })
local hello = Tab:CreateButton({ Name = "Привет", Callback = function() print("привет") end })
local mode  = Tab:CreateDropdown({ Name = "Режим", Options = {"A", "B"}, CurrentOption = {"A"},
    Callback = function(s) print(s[1]) end })
```

| Что нужно | Как |
|---|---|
| Поставить значение **и вызвать `Callback`** | `speed:Set(100)` |
| Поставить значение **без** вызова `Callback` | `speed:Set(100, true)` (второй аргумент `silent`) |
| Прочитать значение | `speed:Get()` |
| «Нажать» кнопку из кода | `hello:Fire()` |
| Сменить список в выпадающем меню | `mode:Refresh({"X", "Y", "Z"}, false)` (`true` оставит текущий выбор) |
| Прочитать любой элемент по `Flag` | `Lumen:GetFlag("speed")` |
| Изменить любой элемент по `Flag` | `Lumen:SetFlag("speed", 120)` (третий аргумент `true` = без `Callback`) |
| Скрыть или показать окно | `Window:SetVisible(false)` |
| Сменить акцентный цвет | `Window:SetAccent(Color3.fromRGB(140, 110, 255))` |
| Закрыть GUI | `Lumen:Destroy()` или `Window:Destroy()` |

**Совет:** если одну и ту же функцию нужно вызывать и из кнопки, и из кода, объяви её отдельно:
```lua
local function resetCharacter()
    local h = game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
    if h then h.Health = 0 end
end
Tab:CreateButton({ Name = "Сброс", Callback = resetCharacter })
Tab:CreateKeybind({ Name = "Сброс по клавише", CurrentKeybind = "R", Callback = resetCharacter })
resetCharacter()   -- и просто вызвать из кода
```

## Flags и сохранение настроек

Если у элемента есть `Flag`, а в окне включено `ConfigurationSaving`, значение сохраняется в файл `workspace/<FolderName>/<FileName>.json` при каждом изменении и **загружается автоматически** после интро. Сохраняются: переключатели, слайдеры, поля ввода, выпадающие списки, кейбинды, цвета.

```lua
Lumen:LoadConfiguration()          -- загрузить вручную
local v = Lumen:GetFlag("speed")   -- прочитать
Lumen:SetFlag("speed", 80)         -- изменить (вызовет Callback)
Lumen.Flags                        -- таблица всех элементов с Flag
```
Для сохранения нужны `writefile`, `readfile`, `isfile`. Если их нет, всё работает, просто без сохранения.

## Иконки и картинки

Иконки вшиты в библиотеку (PNG в base64) и при запуске записываются в `workspace/Lumen/`, поэтому им нужны `writefile` и `getcustomasset`. Если их нет, вместо иконки показывается буква.

Встроенные: `close`, `check`, `chevron`, `home`, `settings`, `user`, `bolt`, `eye`, `sword`, `leaf`, `target`, `info`, `gamepad`, `copy`, `crown`, `shield`, `pin`, `sliders`, `globe`.

Свою картинку можно указать числом (`Icon = 123456789`), строкой `"rbxassetid://123456789"`. Для карточек `CreateProfile` в `Image` подходят ещё ссылки `rbxthumb://` (аватар, иконка игры).

## Панели: боковые, верхние и нижние окна

Панель это отдельное окно, пристыкованное к краю экрана. Оно выезжает, когда мышь касается этого края, и прячется, когда мышь уходит. Внутрь добавляются те же элементы, что и во вкладку.

```lua
local Panel = Window:CreatePanel({ Id = "stats", Title = "Stats", Side = "Right", Width = 260, Height = 300 })
Panel:CreateToggle({ Name = "Пример", Callback = function(on) print(on) end })
Panel:CreateSlider({ Name = "Скорость", Range = { 16, 200 }, CurrentValue = 16, Callback = function(v) end })
```

| Параметр | По умолчанию | Описание |
|---|---|---|
| `Id` | `Title` | Уникальное имя панели. Панель с тем же `Id` второй раз не создаётся: возвращается существующая и открывается. |
| `Title` | `Id` | Заголовок. |
| `Side` | `"Right"` | `"Left"`, `"Right"`, `"Top"`, `"Bottom"`. |
| `Width`, `Height` | 270×360 (боковые), 460×220 (верх/низ) | Размер. |
| `Pinned` | `false` | Не прятаться, когда мышь ушла. |
| `AutoHide` / `HideDelay` | `true` / `0.45` | Прятать после ухода мыши, с задержкой в секундах. |
| `HoverZone` | `14` | Толщина полосы у края, которая открывает панель. |
| `Open` | `false` | Открыть сразу после создания. |

**Методы панели:** `:Open()`, `:Close()`, `:Toggle()`, `:IsOpen()`, `:SetPinned(bool)`, `:Dock("Left"/"Top"/"Right"/"Bottom")`, `:Float(x, y)`, `:ResetLayout()`, `:Destroy()` и все методы элементов (`CreateButton`, `CreateToggle` и так далее).

**Что может игрок:**
- **Зажать заголовок и перетащить.** Если бросить окно у края (подсвечивается полоска), оно пристыкуется к нему. Если бросить в другом месте, оно останется свободным окном.
- Кнопка со стрелками переносит панель на следующий край (слева, сверху, справа, снизу).
- Булавка закрепляет панель открытой, крестик убирает её обратно к краю.
- Положение панелей и главного окна запоминается между запусками.

**Без дубликатов.** Одно и то же окно не создаётся дважды: повторный `CreatePanel` с тем же `Id`, повторный `CreateTab` с тем же именем и повторный `CreateTimer` с тем же `Id`/`Name` возвращают уже существующий объект. Повторный запуск скрипта заменяет старый GUI новым.

**Инфо-плашка сверху** тоже двигается: её можно скрыть, зажать и перетащить к любому краю (`Top`, `Bottom`, `Left`, `Right`; у боковых краёв она становится вертикальной) или оставить отдельным окном. Это же меняется в **Settings** и через `Window:SetInfoBarSide(...)`, `Window:SetFPS(false)`.

## Виджеты: урон по телу, хил, таймеры, полоски

Все виджеты можно класть и в панель, и во вкладку.

### Урон по телу (`CreateBodyDamage`)

Показывает модель персонажа (или простую фигуру), и часть тела, куда пришёл урон, загорается красным. Рядом полоска здоровья, цифры урона и список последних ударов.

```lua
local panel, body, heal = Window:CreateBodyPanel({ Side = "Left" })   -- панель сразу с моделью и хилом
-- или вручную:
local body = SomePanel:CreateBodyDamage({ Mode = "Model" })           -- "Model" или "Schematic"

body:Report("Head", 12)           -- показать удар в голову (из своего кода игры)
body:ReportHeal(8)                -- показать хил
body:SetTarget(somePlayer)        -- следить за другим игроком
```

Группы: `Head`, `Torso`, `LeftArm`, `RightArm`, `LeftLeg`, `RightLeg`. Модель можно вращать мышью, она сама вернётся лицом вперёд.

**Как определяется часть тела.** Roblox не сообщает клиенту, куда попал урон, поэтому есть три источника, и в списке ударов видно, какой сработал:
1. **Точно (красный).** Игра хранит здоровье конечностей (`NumberValue` / `IntValue` или атрибут с именем `Health`/`HP` у частей тела) или ты сам вызвал `body:Report(...)` из модуля игры.
2. **Примерно (оранжевый, «est.»).** Берётся игрок, который смотрит на тебя, и часть тела, ближайшая к линии его прицела. Это догадка, она работает для игр с попаданием «по лучу».
3. **Неизвестно (серый, «?»).** Урон пришёл, но куда, определить нельзя: слегка подсвечивается всё тело.

Если игра не раскрывает место попадания, самый надёжный способ это модуль игры: перехвати событие игры и вызови `body:Report("Head", урон)`.

### Хил (`CreateHealMeter`)

Показывает, лечится ли игрок прямо сейчас («Healing» / «Not healing»), скорость хила в HP/сек и полоску. Параметры: `Target` (игрок, по умолчанию ты), `MaxRate` (скорость, при которой полоска полная, по умолчанию 10).

### Полоска (`CreateBar`)

```lua
local stamina = Panel:CreateBar({ Name = "Stamina", Icon = "bolt", Max = 100, Color = Color3.fromRGB(90, 220, 140) })
stamina:Set(70)            -- значение
stamina:Set(70, 150)       -- значение и новый максимум
```
Необязательные поля: `Value`, `Format = function(value, max) return "..." end`. Методы: `:Set(value, max)`, `:Get()`, `:SetColor(Color3)`.

### Таймеры (`Window:CreateTimer`)

Таймеры показываются внизу или вверху экрана и складываются стопкой. Цвет меняется сам: янтарный в последние `Warning` секунд, красный и мигающий в последние 5.

```lua
local round = Window:CreateTimer({ Name = "Round", Duration = 120, Position = "Bottom",
    Callback = function() print("раунд закончился") end })
round:Pause(); round:Resume(); round:Add(30); round:Set(60); round:Reset(90)
print(round:GetTime(), round:IsRunning())

local watch = Window:CreateTimer({ Name = "Stopwatch", Mode = "Up", Position = "Top" })   -- секундомер
```

| Параметр | По умолчанию | Описание |
|---|---|---|
| `Name` / `Id` | `"Timer"` | Название. Таймер с тем же именем не создаётся второй раз. |
| `Duration` | `60` | Длина отсчёта (для секундомера цель, необязательно). |
| `Mode` | `"Down"` | `"Down"` отсчёт вниз, `"Up"` секундомер. |
| `Position` | `"Bottom"` | `"Bottom"` или `"Top"`. |
| `Warning` | `10` | За сколько секунд цвет становится янтарным. |
| `AutoStart` | `true` | Запускать сразу. |
| `Keep` | `false` | Не убирать после окончания (иначе исчезает через `HideDelay`, 3 секунды). |
| `Color`, `ShowBar` | акцент, `true` | Цвет и полоска прогресса. |
| `Callback`, `OnTick` | нет | Функция по окончании; функция каждую секунду (получает секунды). |
| `FinishAtGoal` | `false` | Для секундомера: закончить по достижении `Duration`. |

**Методы:** `:Start()`, `:Pause()`, `:Resume()`, `:Stop()`, `:Reset(seconds)`, `:Set(seconds)`, `:Add(seconds)`, `:GetTime()`, `:IsRunning()`, `:SetName(text)`, `:OnFinish(fn)`, `:Destroy()`.

**Привязка к таймеру самой игры** (время раунда, которое игра уже показывает):
```lua
round:BindValue(workspace.RoundTime)            -- NumberValue / IntValue
round:BindValue(workspace, "TimeLeft")          -- атрибут
round:BindText(player.PlayerGui.Hud.Timer)      -- TextLabel с текстом вида 02:35
round:Unbind()
```

## Встроенные настройки и память игрока

**Вкладка Settings** добавляется сама, после твоих вкладок, и не требует кода: акцентный цвет (набор и свой), прозрачность, масштаб, блюр, анимация запуска, клавиша скрытия, сторона уведомлений, инфо-плашка (показать и позиция), сброс положения окон, «запоминать мои настройки», сброс всех настроек. Если ты создал вкладку `Settings` сам, элементы добавятся в неё. Отключается через `SettingsTab = false`.

**Библиотека запоминает игрока.** Всё, что игрок поменял в Settings, а также положение главного окна, панелей и инфо-плашки сохраняется в `workspace/Lumen/Settings.json` отдельно для каждого аккаунта (по `UserId`) и загружается при каждом запуске. Значения игрока важнее значений из `CreateWindow`. Игрок может выключить запоминание в Settings. Нужны `writefile`/`readfile`/`isfile`, без них всё работает, но не запоминается.

**Приветствие.** Новому игроку показывается обычное интро (`IntroText`). Вернувшемуся: `Hello again <ник> 4D$ Present you`, где «4D$ Present» это твой `IntroText`. Свой текст: `ReturningText = "С возвращением, {nick}! {intro}"`. Отключить: `ReturningIntro = false`. Если анимация запуска выключена, вместо неё приходит уведомление с тем же приветствием.

Клавише в `CreateKeybind` можно добавить `OnChange = function(keyName) end`: она вызывается, когда игрок выбрал новую клавишу (а `Callback` вызывается, когда клавишу нажали).
## Хаб `Hub.lua`: свои функции под игру

Хаб это готовый скрипт на этой библиотеке: вкладки **Combat, Visuals, Player, Farm, Teleport, Misc, Info** и слой совместимости с исполнителями (Solara, Xeno, Volt, Potassium, Synapse, Delta, Codex, KRNL).

**Настройки в блоке `Config` вверху файла:**
```lua
local Config = {
    ScriptName = "4D Hub", Version = "1.0", Creator = "Yeyzcje",
    CreatorUserId = nil,                -- твой user id, чтобы показать аватар
    Discord = "https://discord.gg/HzzNDQerN",
    Description = "Universal script hub",
    LoadUrl = "",                       -- raw-ссылка на скрипт (для запуска после телепорта)
}
```

**Функции под конкретную игру** добавляются так, и появляется отдельная вкладка **Game**:
```lua
Hub.RegisterGame(1234567890, "Название игры", function(Tab, Window, Compat)
    Tab:CreateSection("Моя игра")
    Tab:CreateToggle({ Name = "Автоквест", Icon = "leaf", CurrentValue = false, Flag = "mg_quest",
        Callback = function(on) end })
end)
```
Вместо `1234567890` подставь `game.PlaceId` или `game.GameId` нужной игры. Регистрируй игру **до** создания окна (блок `GAME MODULES` в файле).

**Слой `Compat`** даёт одинаковые функции на всех исполнителях и сам выбирает запасной вариант, если нужной функции нет:
`Compat.copy(text)`, `Compat.httpGet(url)`, `Compat.click()`, `Compat.firePrompt(prompt)`, `Compat.touch(part)`, `Compat.setFpsCap(n)`, `Compat.queueTeleport(code)`, `Compat.deleteFile(path)`; данные: `Compat.ExecutorName`, `Compat.Family`, `Compat.IsMobile`, `Compat.Has.<Функция>`.

## Совместимость и решение проблем

| Проблема | Что делать |
|---|---|
| Вместо иконок буквы | В исполнителе нет `writefile` или `getcustomasset`. Всё работает, иконки просто текстовые. |
| Нет блюра | Блюр использует `DepthOfField` и стеклянную деталь, ему нужно высокое качество графики. Отключается через `Blur = false`. |
| Настройки не сохраняются | Нет `writefile`/`readfile`/`isfile`, или забыл `Flag` / `ConfigurationSaving`. |
| Окно пропало | Нажми `RightShift` (или свою `ToggleKey`) либо кликни по плашке сверху. |
| Ошибка в `Callback` | Она не ломает GUI: ошибка пишется в консоль (`[Lumen] callback error`). |
| Много вкладок не помещается | Библиотека сама переключается на вкладки-иконки, когда им не хватает места. Можно включить сразу: `TabStyle = "Expand"`. |

Реальной локации сервера Roblox клиенту не отдаёт, поэтому на плашке показывается количество игроков.

---

# 🇬🇧 English

## Contents
1. [Install](#install)
2. [Quick start](#quick-start)
3. [Window: `CreateWindow`](#window-createwindow)
4. [Tabs and elements](#tabs-and-elements)
5. [Notifications](#notifications)
6. [Calling functions from code](#calling-functions-from-code)
7. [Flags and saved settings](#flags-and-saved-settings)
8. [Icons and images](#icons-and-images)
9. [Panels: side, top and bottom windows](#panels-side-top-and-bottom-windows)
10. [Widgets: body damage, heal meter, timers, bars](#widgets-body-damage-heal-meter-timers-bars)
11. [Built-in settings and the player's memory](#built-in-settings-and-the-players-memory)
12. [The hub `Hub.lua`: game-specific functions](#the-hub-hublua-game-specific-functions)
13. [Compatibility and troubleshooting](#compatibility-and-troubleshooting)

## Install

Pick one.

**1. From a file** (put `Lumen.lua` into the executor's `workspace` folder):
```lua
local Lumen = loadstring(readfile("Lumen.lua"))()
```

**2. From a URL** (upload `Lumen.lua` to GitHub and use the Raw link):
```lua
local Lumen = loadstring(game:HttpGet("https://raw.githubusercontent.com/<you>/<repo>/main/Lumen.lua"))()
```

**3. Pasted.** Paste the whole `Lumen.lua` at the top of your script as `local Lumen = (function() ... end)()`, or just run `Lumen_demo.lua` / `Lumen_Hub.lua`, which already contain the library.

## Quick start

```lua
local Lumen = loadstring(readfile("Lumen.lua"))()

local Window = Lumen:CreateWindow({
    Name = "My Script",
    ScriptName = "My Script",        -- shown on the info bar at the top
    Subtitle = "v1.0",
    ConfigurationSaving = { Enabled = true, FolderName = "MyScript", FileName = "config" },
})

local Tab = Window:CreateTab("Main", "home")     -- tab name and icon

Tab:CreateButton({
    Name = "Hello",
    Callback = function()
        Lumen:Notify({ Title = "Hello", Content = "Button pressed", Type = "success" })
    end,
})

Tab:CreateToggle({
    Name = "Infinite jump", CurrentValue = false, Flag = "infjump",
    Callback = function(on) print("enabled:", on) end,
})
```

The rule is simple: **describe an element with a `Callback` and it appears in the window.** When the user touches the element, your function runs with the new value.

## Window: `CreateWindow`

`Lumen:CreateWindow(options)` returns the window object.

| Option | Type | Default | Description |
|---|---|---|---|
| `Name` (or `Title`) | string | `"Lumen"` | Title. The first two letters become the logo. |
| `ScriptName` | string | `Subtitle` or `Name` | Script name on the info bar. |
| `Subtitle` | string | none | Grey text next to the title (version, game). |
| `Accent` | Color3 | white | Accent colour. |
| `ToggleKey` | KeyCode | `RightShift` | Key that hides / shows the window. |
| `Size` | UDim2 (offset) | `580x390` | Size. Shrinks automatically on small screens. |
| `Transparency` | number 0–1 | `0.1` | Window background transparency. |
| `Scale` | number | `1` | Interface scale. |
| `Blur` | boolean | `true` | Blur behind the window only. |
| `Intro` | boolean | `true` | Start animation (text, then a line, then the window). |
| `IntroText` / `LoadingTitle` | string | `Name` | Intro text. |
| `InfoBar` / `FPS` | boolean | `true` | Top bar: avatar, nickname, script, injector, FPS, ping, players. Clicking it hides / shows the window. |
| `TabStyle` | `"Expand"` | normal | `"Expand"`: tabs are icons and the selected one shows its name. Use it with many tabs. |
| `ConfigurationSaving` | table | off | `{ Enabled = true, FolderName = "...", FileName = "..." }` |
| `SettingsTab` | boolean | `true` | Built-in **Settings** tab (see below). `false` removes it. |
| `ReturningText` | string | auto | Intro text for a returning player. Placeholders: `{nick}`, `{intro}`. |
| `ReturningIntro` | boolean | `true` | `false` turns the "Hello again" greeting off. |

### Window methods

| Method | What it does |
|---|---|
| `Window:CreateTab(name, icon)` | Creates a tab. Returns the tab object. |
| `Window:Notify(options)` | Notification (same as `Lumen:Notify`). |
| `Window:SetVisible(bool)` | Show or hide the window (animated). |
| `Window:SetAccent(Color3)` | Change the accent colour live. |
| `Window:SetBlur(bool)` | Turn the blur on or off. |
| `Window:SetTransparency(0..1)` | Window transparency. |
| `Window:SetScale(number)` | Scale (0.6–1.4). |
| `Window:SetFPS(bool)` | Show or hide the top info bar. |
| `Window:OnDestroy(function)` | Runs when the GUI closes (handy for cleanup). |
| `Window:Destroy()` | Close the GUI (the window squeezes in, then the info bar closes from the sides). |
| `Window:CreatePanel(options)` | A side, top or bottom window (see "Panels"). |
| `Window:GetPanel(id)` | Find a panel that was already created. |
| `Window:CreateBodyPanel(options)` | Panel with the body model and the heal meter. |
| `Window:CreateTimer(options)` | Round timer, countdown or stopwatch. |
| `Window:SetInfoBarSide(side)` | Info bar: `"Top"`, `"Bottom"`, `"Left"`, `"Right"` or `"Floating"` (its own window). |
| `Window:SetToggleKey(key)` | Change the key that hides the window. |
| `Window:SetNotifySide("Left"/"Right")` | Side of the notifications. |
| `Window:ResetLayout()` | Put the window and the panels back. |

## Tabs and elements

Create a tab with `local Tab = Window:CreateTab("Name", "icon")`.
The icon is optional (list below). Every element accepts `Icon = "..."` too.

Every method exists in two spellings: `CreateX` (like Rayfield) and `AddX` (identical).

### Common fields
| Field | Description |
|---|---|
| `Name` | Element label. |
| `Icon` | Icon on the left (optional). |
| `Flag` | Unique name. Enables saving and access through `Lumen:GetFlag`. |
| `Callback` | Your function. See the table below for its arguments. |

### Element table

| Method | Main fields | What `Callback` receives | Returns |
|---|---|---|---|
| `CreateSection(text)` | text | — | — |
| `CreateLabel(text)` | text | — | `:Set(text)` |
| `CreateParagraph{Title, Content}` | title, text | — | `:Set{Title, Content}` |
| `CreateButton` | `Name`, `Callback` | nothing | `:Fire()` |
| `CreateToggle` | `CurrentValue` (bool) | `boolean` | `:Set(v, silent)`, `:Get()` |
| `CreateSlider` | `Range = {min, max}`, `Increment`, `Suffix`, `CurrentValue` | `number` | `:Set(v, silent)`, `:Get()` |
| `CreateDropdown` | `Options` (list), `CurrentOption` (list), `MultipleOptions` | a **table** of selected options | `:Set(v, silent)`, `:Get()`, `:Refresh(options, keep)` |
| `CreateInput` | `CurrentValue`, `PlaceholderText`, `RemoveTextAfterFocusLost` | `text, enterPressed` | `:Set(text, silent)`, `:Get()` |
| `CreateKeybind` | `CurrentKeybind` (key name, e.g. `"E"`), `HoldToInteract` | nothing; with `HoldToInteract = true` it gets `true` (pressed) and `false` (released) | `:Set(key)`, `:Get()` |
| `CreateColorPicker` | `Color` (Color3) | `Color3` | `:Set(color, silent)`, `:Get()` |
| `CreateProfile` | `Title`, `Subtitle`, `Line`, `Image` or `Icon`, `Circle` | — | `:Set{...}` |

### Examples

```lua
-- Slider with a fractional step
Tab:CreateSlider({
    Name = "Volume", Range = { 0, 1 }, Increment = 0.05, CurrentValue = 0.5,
    Callback = function(v) print(v) end,
})

-- Dropdown: the Callback receives a TABLE
Tab:CreateDropdown({
    Name = "Mode", Options = { "Easy", "Normal", "Hard" }, CurrentOption = { "Normal" },
    Callback = function(selected) print(selected[1]) end,
})

-- Multi select
Tab:CreateDropdown({
    Name = "Targets", Options = { "Players", "NPCs", "Bosses" }, CurrentOption = { "Players" },
    MultipleOptions = true,
    Callback = function(selected) print(table.concat(selected, ", ")) end,
})

-- Keybind that works while held
Tab:CreateKeybind({
    Name = "Sprint", CurrentKeybind = "LeftShift", HoldToInteract = true,
    Callback = function(held) print(held and "held" or "released") end,
})

-- Card with a picture (player avatar by user id)
Tab:CreateProfile({
    Title = "Author", Subtitle = "Creator", Line = "discord.gg/invite", Circle = true,
    Image = "rbxthumb://type=AvatarHeadShot&id=1&w=150&h=150",
})
```

## Notifications

```lua
Lumen:Notify({
    Title = "Title",
    Content = "Notification text",     -- typed out letter by letter
    Duration = 4,                       -- seconds (default 4)
    Type = "success",                   -- "info" | "success" | "warning" | "error"
    Icon = "eye",                       -- optional: custom icon
})
```
Clicking a card closes it early. You can call `Notify` before the intro ends: it waits and shows up afterwards.

## Calling functions from code

Every element returns an object. Keep it in a variable and control it from code.

```lua
local speed = Tab:CreateSlider({ Name = "Speed", Range = {16, 200}, CurrentValue = 16,
    Flag = "speed", Callback = function(v) print("speed", v) end })
local hello = Tab:CreateButton({ Name = "Hello", Callback = function() print("hello") end })
local mode  = Tab:CreateDropdown({ Name = "Mode", Options = {"A", "B"}, CurrentOption = {"A"},
    Callback = function(s) print(s[1]) end })
```

| What you want | How |
|---|---|
| Set the value **and run the `Callback`** | `speed:Set(100)` |
| Set the value **without** running the `Callback` | `speed:Set(100, true)` (second argument is `silent`) |
| Read the value | `speed:Get()` |
| "Press" a button from code | `hello:Fire()` |
| Replace the dropdown list | `mode:Refresh({"X", "Y", "Z"}, false)` (`true` keeps the current choice) |
| Read any element by its `Flag` | `Lumen:GetFlag("speed")` |
| Change any element by its `Flag` | `Lumen:SetFlag("speed", 120)` (third argument `true` = no `Callback`) |
| Hide or show the window | `Window:SetVisible(false)` |
| Change the accent colour | `Window:SetAccent(Color3.fromRGB(140, 110, 255))` |
| Close the GUI | `Lumen:Destroy()` or `Window:Destroy()` |

**Tip:** if the same function must run from a button and from code, declare it separately:
```lua
local function resetCharacter()
    local h = game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
    if h then h.Health = 0 end
end
Tab:CreateButton({ Name = "Reset", Callback = resetCharacter })
Tab:CreateKeybind({ Name = "Reset key", CurrentKeybind = "R", Callback = resetCharacter })
resetCharacter()   -- or simply call it yourself
```

## Flags and saved settings

If an element has a `Flag` and the window has `ConfigurationSaving` enabled, its value is written to `workspace/<FolderName>/<FileName>.json` on every change and **loaded automatically** after the intro. Saved: toggles, sliders, inputs, dropdowns, keybinds, colours.

```lua
Lumen:LoadConfiguration()          -- load manually
local v = Lumen:GetFlag("speed")   -- read
Lumen:SetFlag("speed", 80)         -- change (runs the Callback)
Lumen.Flags                        -- table of every element that has a Flag
```
Saving needs `writefile`, `readfile`, `isfile`. Without them everything still works, just without saving.

## Icons and images

Icons are built into the library (base64 PNG) and written to `workspace/Lumen/` at startup, so they need `writefile` and `getcustomasset`. If those are missing, a letter is shown instead of the icon.

Built in: `close`, `check`, `chevron`, `home`, `settings`, `user`, `bolt`, `eye`, `sword`, `leaf`, `target`, `info`, `gamepad`, `copy`, `crown`, `shield`, `pin`, `sliders`, `globe`.

For your own image use a number (`Icon = 123456789`) or the string `"rbxassetid://123456789"`. `CreateProfile` also accepts `rbxthumb://` links in `Image` (avatars, game icons).

## Panels: side, top and bottom windows

A panel is a separate window docked to a screen edge. It slides out when the mouse touches that edge and hides when the mouse leaves. It takes the same elements as a tab.

```lua
local Panel = Window:CreatePanel({ Id = "stats", Title = "Stats", Side = "Right", Width = 260, Height = 300 })
Panel:CreateToggle({ Name = "Example", Callback = function(on) print(on) end })
Panel:CreateSlider({ Name = "Speed", Range = { 16, 200 }, CurrentValue = 16, Callback = function(v) end })
```

| Option | Default | Description |
|---|---|---|
| `Id` | `Title` | Unique name of the panel. A panel with the same `Id` is never created twice: the existing one is returned and opened. |
| `Title` | `Id` | Header text. |
| `Side` | `"Right"` | `"Left"`, `"Right"`, `"Top"`, `"Bottom"`. |
| `Width`, `Height` | 270×360 (sides), 460×220 (top/bottom) | Size. |
| `Pinned` | `false` | Do not hide when the mouse leaves. |
| `AutoHide` / `HideDelay` | `true` / `0.45` | Hide after the mouse left, with a delay in seconds. |
| `HoverZone` | `14` | Thickness of the strip at the edge that opens the panel. |
| `Open` | `false` | Open right after creation. |

**Panel methods:** `:Open()`, `:Close()`, `:Toggle()`, `:IsOpen()`, `:SetPinned(bool)`, `:Dock("Left"/"Top"/"Right"/"Bottom")`, `:Float(x, y)`, `:ResetLayout()`, `:Destroy()` and every element method (`CreateButton`, `CreateToggle`, ...).

**What the player can do:**
- **Hold the header and drag.** Drop the window near an edge (a strip lights up) and it docks there. Drop it anywhere else and it stays as a free floating window.
- The arrows button sends the panel to the next edge (left, top, right, bottom).
- The pin keeps the panel open, the cross puts it back at its edge.
- The position of the panels and of the main window is remembered between launches.

**No duplicates.** The same window is never created twice: a second `CreatePanel` with the same `Id`, a second `CreateTab` with the same name and a second `CreateTimer` with the same `Id`/`Name` return the object that already exists. Running the script again replaces the old GUI with the new one.

**The info bar at the top** can be moved too: hide it, hold it and drag it to any edge (`Top`, `Bottom`, `Left`, `Right`; at the side edges it becomes vertical) or leave it as its own window. The same is available in **Settings** and through `Window:SetInfoBarSide(...)`, `Window:SetFPS(false)`.

## Widgets: body damage, heal meter, timers, bars

Every widget can go into a panel or into a tab.

### Body damage (`CreateBodyDamage`)

Shows the character's model (or a simple figure) and the body part that took damage lights up red. Next to it: a health bar, floating damage numbers and a list of the latest hits.

```lua
local panel, body, heal = Window:CreateBodyPanel({ Side = "Left" })   -- a panel with the model and the heal meter
-- or by hand:
local body = SomePanel:CreateBodyDamage({ Mode = "Model" })           -- "Model" or "Schematic"

body:Report("Head", 12)           -- show a hit to the head (from your own game code)
body:ReportHeal(8)                -- show healing
body:SetTarget(somePlayer)        -- watch another player
```

Groups: `Head`, `Torso`, `LeftArm`, `RightArm`, `LeftLeg`, `RightLeg`. The model can be rotated with the mouse and turns back to face forward by itself.

**How the body part is found.** Roblox does not tell the client where damage landed, so there are three sources, and the hit list shows which one was used:
1. **Exact (red).** The game keeps limb health (`NumberValue` / `IntValue` or an attribute named `Health`/`HP` on the body parts), or you called `body:Report(...)` yourself from a game module.
2. **Estimated (orange, "est.").** The enemy who is looking at you is picked, and the body part closest to the line of his aim. It is a guess and works for hitscan-style games.
3. **Unknown (grey, "?").** Damage arrived but the place cannot be known: the whole body glows a little.

If a game does not reveal the hit location, the most reliable way is a game module: hook the game's own event and call `body:Report("Head", damage)`.

### Heal meter (`CreateHealMeter`)

Shows whether the player is healing right now ("Healing" / "Not healing"), the heal speed in HP/s and a bar. Options: `Target` (player, you by default), `MaxRate` (the speed that fills the bar, 10 by default).

### Bar (`CreateBar`)

```lua
local stamina = Panel:CreateBar({ Name = "Stamina", Icon = "bolt", Max = 100, Color = Color3.fromRGB(90, 220, 140) })
stamina:Set(70)            -- value
stamina:Set(70, 150)       -- value and a new maximum
```
Optional fields: `Value`, `Format = function(value, max) return "..." end`. Methods: `:Set(value, max)`, `:Get()`, `:SetColor(Color3)`.

### Timers (`Window:CreateTimer`)

Timers appear at the bottom or the top of the screen and stack. The colour changes by itself: amber for the last `Warning` seconds, red and blinking for the last 5.

```lua
local round = Window:CreateTimer({ Name = "Round", Duration = 120, Position = "Bottom",
    Callback = function() print("round over") end })
round:Pause(); round:Resume(); round:Add(30); round:Set(60); round:Reset(90)
print(round:GetTime(), round:IsRunning())

local watch = Window:CreateTimer({ Name = "Stopwatch", Mode = "Up", Position = "Top" })   -- stopwatch
```

| Option | Default | Description |
|---|---|---|
| `Name` / `Id` | `"Timer"` | Name. A timer with the same name is never created twice. |
| `Duration` | `60` | Countdown length (for a stopwatch an optional goal). |
| `Mode` | `"Down"` | `"Down"` countdown, `"Up"` stopwatch. |
| `Position` | `"Bottom"` | `"Bottom"` or `"Top"`. |
| `Warning` | `10` | Seconds before the end when the colour turns amber. |
| `AutoStart` | `true` | Start immediately. |
| `Keep` | `false` | Do not remove after it finished (otherwise it disappears after `HideDelay`, 3 seconds). |
| `Color`, `ShowBar` | accent, `true` | Colour and progress bar. |
| `Callback`, `OnTick` | none | Function when finished; function every second (gets the seconds). |
| `FinishAtGoal` | `false` | Stopwatch: finish when `Duration` is reached. |

**Methods:** `:Start()`, `:Pause()`, `:Resume()`, `:Stop()`, `:Reset(seconds)`, `:Set(seconds)`, `:Add(seconds)`, `:GetTime()`, `:IsRunning()`, `:SetName(text)`, `:OnFinish(fn)`, `:Destroy()`.

**Following the game's own timer** (the round time the game already shows):
```lua
round:BindValue(workspace.RoundTime)            -- NumberValue / IntValue
round:BindValue(workspace, "TimeLeft")          -- attribute
round:BindText(player.PlayerGui.Hud.Timer)      -- a TextLabel showing something like 02:35
round:Unbind()
```

## Built-in settings and the player's memory

The **Settings tab** is added by itself, after your own tabs, and needs no code: accent colour (presets and custom), transparency, scale, blur, start animation, hide key, notification side, info bar (show and position), reset window positions, "remember my settings", reset everything. If you created a `Settings` tab yourself, the elements are added to it. Turn it off with `SettingsTab = false`.

**The library remembers the player.** Everything the player changes in Settings, plus the position of the main window, the panels and the info bar, is saved to `workspace/Lumen/Settings.json` per account (by `UserId`) and loaded on every launch. The player's values win over the values in `CreateWindow`. The player can switch remembering off in Settings. It needs `writefile`/`readfile`/`isfile`; without them everything works but nothing is remembered.

**Greeting.** A new player sees the normal intro (`IntroText`). A returning one sees `Hello again <nick> 4D$ Present you`, where "4D$ Present" is your `IntroText`. Custom text: `ReturningText = "Welcome back, {nick}! {intro}"`. Turn it off with `ReturningIntro = false`. If the start animation is off, a notification with the same greeting appears instead.

`CreateKeybind` accepts `OnChange = function(keyName) end`: it runs when the player picks a new key (while `Callback` runs when the key is pressed).
## The hub `Hub.lua`: game-specific functions

The hub is a ready-made script built on this library: tabs **Combat, Visuals, Player, Farm, Teleport, Misc, Info** and an executor compatibility layer (Solara, Xeno, Volt, Potassium, Synapse, Delta, Codex, KRNL).

**Settings in the `Config` block at the top of the file:**
```lua
local Config = {
    ScriptName = "4D Hub", Version = "1.0", Creator = "Yeyzcje",
    CreatorUserId = nil,                -- your user id, to show your avatar
    Discord = "https://discord.gg/HzzNDQerN",
    Description = "Universal script hub",
    LoadUrl = "",                       -- raw link to this script (to re-run after teleport)
}
```

**Game-specific functions** are added like this, and a separate **Game** tab appears:
```lua
Hub.RegisterGame(1234567890, "Game name", function(Tab, Window, Compat)
    Tab:CreateSection("My game")
    Tab:CreateToggle({ Name = "Auto quest", Icon = "leaf", CurrentValue = false, Flag = "mg_quest",
        Callback = function(on) end })
end)
```
Replace `1234567890` with the `game.PlaceId` or `game.GameId` of the game. Register it **before** the window is created (the `GAME MODULES` block in the file).

**The `Compat` layer** gives the same functions on every executor and picks a fallback when the real function is missing:
`Compat.copy(text)`, `Compat.httpGet(url)`, `Compat.click()`, `Compat.firePrompt(prompt)`, `Compat.touch(part)`, `Compat.setFpsCap(n)`, `Compat.queueTeleport(code)`, `Compat.deleteFile(path)`; data: `Compat.ExecutorName`, `Compat.Family`, `Compat.IsMobile`, `Compat.Has.<Function>`.

## Compatibility and troubleshooting

| Problem | What to do |
|---|---|
| Letters instead of icons | The executor has no `writefile` or `getcustomasset`. Everything works, icons are just text. |
| No blur | Blur uses `DepthOfField` and a glass part and needs a high graphics quality. Turn it off with `Blur = false`. |
| Settings are not saved | Missing `writefile`/`readfile`/`isfile`, or you forgot `Flag` / `ConfigurationSaving`. |
| The window disappeared | Press `RightShift` (or your `ToggleKey`) or click the info bar at the top. |
| Error inside a `Callback` | It does not break the GUI: the error is printed to the console (`[Lumen] callback error`). |
| Too many tabs to fit | The library switches to icon-only tabs by itself when there is no room. You can force it with `TabStyle = "Expand"`. |

Roblox does not expose the real server location to the client, so the info bar shows the player count instead.
