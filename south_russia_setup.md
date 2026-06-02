# Настройка MeshCore репитеров — Юг России

## Карта регионов

```
* F                                  (корень, разрешает всё по умолчанию)
  ru F                               (Россия — суперрегион)
    ru-kda F                         (Краснодарский край)
      ru-kda-krd F                   (Краснодар)
      ru-kda-gk F                    (Горячий Ключ)
    ru-sta F                         (Ставропольский край)
      ru-sta-stv F                   (Ставрополь)
      ru-sta-kmv F                   (КМВ)
```

## Именование

| Код   | Регион / Город          |
|-------|-------------------------|
| `ru`  | Россия (суперрегион)    |
| `ru-kda` | Краснодарский край   |
| `ru-kda-krd` | Краснодар        |
| `ru-kda-gk`  | Горячий Ключ     |
| `ru-sta` | Ставропольский край  |
| `ru-sta-stv` | Ставрополь       |
| `ru-sta-kmv` | КМВ             |

> Правило: коды краёв — трёхбуквенные (kda, sta), коды городов — трёхбуквенные (krd, gk, stv, kmv).

---

## Настройка репитеров

### Краснодар

```
region put ru
region put ru-kda ru
region put ru-kda-krd ru-kda
region home ru-kda-krd
region default ru-kda-krd
region save
```

### Горячий Ключ

```
region put ru
region put ru-kda ru
region put ru-kda-gk ru-kda
region home ru-kda-gk
region default ru-kda-gk
region save
```

### Ставрополь

```
region put ru
region put ru-sta ru
region put ru-sta-stv ru-sta
region home ru-sta-stv
region default ru-sta-stv
region save
```

### КМВ (Пятигорск / Ессентуки / Кисловодск)

```
region put ru
region put ru-sta ru
region put ru-sta-kmv ru-sta
region home ru-sta-kmv
region default ru-sta-kmv
region save
```

### Пограничный репитер (охватывает два края)

Если репитер физически стоит на границе и может обслуживать оба края —
добавь подрегионы обоих:

```
region put ru
region put ru-kda ru
region put ru-sta ru
region put ru-kda-krd ru-kda
region put ru-sta-kmv ru-sta
region home ru-kda-krd
region default ru-kda-krd
region save
```

---

## Фильтрация в клиентском приложении

1. Открой канал → три точки → **Set Region Scope**
2. Добавь нужные регионы из списка выше
3. Выбери scope для канала:

| Канал                 | Scope       |
|-----------------------|-------------|
| Общероссийский чат    | `ru`        |
| Чат Краснодарского края | `ru-kda`  |
| Чат Ставропольского края | `ru-sta` |
| Чат Краснодара        | `ru-kda-krd`|
| Чат Горячего Ключа    | `ru-kda-gk` |
| Чат Ставрополя        | `ru-sta-stv`|
| Чат КМВ               | `ru-sta-kmv`|

4. Приложение запомнит scope для каждого канала отдельно.

---

## Важные замечания

- Начиная с v1.15.0 команда `region put` автоматически выполняет `allowf` — отдельная команда не нужна.
- Имена регионов: только строчные латинские буквы, цифры и дефис, до 29 байт.
- DM-сообщения **не фильтруются** регионами — регионы работают только для каналов.
- Шаблон `* F` (по умолчанию) разрешает пересылку всех пакетов. Для жёсткой фильтрации: `region denyf *`, но только после того как все отправляют сообщения со scope.
- `region default` задаёт scope для исходящих сообщений без явного указания (v1.15.0+).

---

## Порядок развёртывания

1. Настроить все репитеры (`region put`, `home`, `default`, `save`)
2. Убедиться что репитеры видят друг друга
3. Попросить пользователей выставить scope в каналах
4. После проверки — при необходимости закрыть `*` через `region denyf *`
