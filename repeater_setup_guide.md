> [!NOTE]
> Сгенерировано ИИ (Hermes Agent)

# Пошаговая настройка репитера MeshCore

Инструкция по настройке региональной фильтрации с нуля. Подходит для чистого репитера и для переконфигурации существующего.

---

## Принцип: только свои регионы

Репитер создаёт и пересылает только те регионы, в которых он физически находится + общий суперрегион (`ru`). Это не наносит ущерба сети — каждый репитер пересылает только свой трафик.

Пример: репитеру в Краснодаре НЕ нужно знать про `ru-sta-stv`. Пакеты со scope `ru-sta-stv` через него не пойдут — и это правильно, зачем гнать ставропольский городской трафик через Краснодар?

Для межкраевой связи используется общий суперрегион `ru` — он есть на всех репитерах.

---

## Шаг 1. Очистка

Удалить все существующие регионы через `region remove` снизу вверх: сначала дочерние, потом родительские.

Для репитера в Краснодарском крае:

```
region remove ru-kda-krd
region remove ru-kda-gk
region remove ru-kda
region remove ru
```

Для репитера в Ставропольском крае:

```
region remove ru-sta-stv
region remove ru-sta-kmv
region remove ru-sta
region remove ru
```

Если какие-то регионы не существуют — команда выдаст ошибку, это нормально.

Шаблонный регион `*` удалить нельзя — он всегда присутствует.

Проверить что осталось:

```
region
```

Должен показать только:
```
* F
```

---

## Шаг 2. Создание регионов

Команда `region put <имя> [родитель]` создаёт регион и автоматически разрешает его пересылку (`allowf`).

Порядок важен: сначала родитель, потом дочерние.

---

## Шаг 3. Назначение home и default

- `region home <регион>` — объявляет местоположение репитера. Влияет на маршрутизацию.
- `region default <регион>` — scope по умолчанию для исходящих сообщений (v1.15.0+).

Обе команды выполняются после создания иерархии.

---

## Шаг 4. Сохранение

```
region save
```

Без этого после перезагрузки все настройки пропадут.

---

## Шаг 5. Проверка

```
region
```

Убедиться что регионы созданы с флагом `F`, `home` и `default` указаны верно.

---

# Примеры для каждого города

## Краснодар

```
region remove ru-kda-krd
region remove ru-kda-gk
region remove ru-kda
region remove ru
region put ru
region put ru-kda ru
region put ru-kda-krd ru-kda
region home ru-kda-krd
region default ru-kda
region save
region
```

Ожидаемый вывод `region`:

```
* F
ru F
  ru-kda F
    ru-kda-krd F [HOME] [DEFAULT]
```

---

## Горячий Ключ

```
region remove ru-kda-krd
region remove ru-kda-gk
region remove ru-kda
region remove ru
region put ru
region put ru-kda ru
region put ru-kda-gk ru-kda
region home ru-kda-gk
region default ru-kda
region save
region
```

Ожидаемый вывод:

```
* F
ru F
  ru-kda F
    ru-kda-gk F [HOME] [DEFAULT]
```

---

## Ставрополь

```
region remove ru-sta-stv
region remove ru-sta-kmv
region remove ru-sta
region remove ru
region put ru
region put ru-sta ru
region put ru-sta-stv ru-sta
region home ru-sta-stv
region default ru-sta
region save
region
```

Ожидаемый вывод:

```
* F
ru F
  ru-sta F
    ru-sta-stv F [HOME] [DEFAULT]
```

---

## КМВ (Пятигорск / Ессентуки / Кисловодск)

```
region remove ru-sta-stv
region remove ru-sta-kmv
region remove ru-sta
region remove ru
region put ru
region put ru-sta ru
region put ru-sta-kmv ru-sta
region home ru-sta-kmv
region default ru-sta
region save
region
```

Ожидаемый вывод:

```
* F
ru F
  ru-sta F
    ru-sta-kmv F [HOME] [DEFAULT]
```

---

## Пограничный репитер (между Краснодарским и Ставропольским краем)

Репитер стоит на границе и обслуживает оба края. Home назначаем на ближайший город.

```
region remove ru-kda-krd
region remove ru-kda-gk
region remove ru-kda
region remove ru-sta-stv
region remove ru-sta-kmv
region remove ru-sta
region remove ru
region put ru
region put ru-kda ru
region put ru-kda-krd ru-kda
region put ru-sta ru
region put ru-sta-kmv ru-sta
region home ru-kda-krd
region default ru-kda
region save
region
```

Ожидаемый вывод:

```
* F
ru F
  ru-kda F
    ru-kda-krd F [HOME] [DEFAULT]
  ru-sta F
    ru-sta-kmv F
```

---

# Шпаргалка по командам

| Команда | Описание |
|---|---|
| `region remove <имя>` | Удалить регион (сначала дочерние) |
| `region put <имя> [родитель]` | Создать регион + автоматически allowf |
| `region home <регион>` | Объявить местоположение репитера |
| `region default <регион>` | Scope по умолчанию для исходящих (v1.15.0+) |
| `region allowf <регион>` | Разрешить пересылку региона (обычно не нужен — put делает это сам) |
| `region denyf <регион>` | Запретить пересылку региона |
| `region` | Показать все регионы, флаги и иерархию |
| `region list allowed` | Показать только разрешённые регионы |
| `region list denied` | Показать только заблокированные регионы |
| `region save` | Сохранить в энергонезависимую память |
| `region denyf *` | Закрыть шаблон — пакеты без scope отбрасываются |

---

# Типичные ошибки

1. **Забыли `region save`** — после перезагрузки всё пропадёт.
2. **Не задали `region home`** — сеть не знает маршрута до региона, пакеты теряются.
3. **`region denyf *` до того как пользователи настроили scope** — все пакеты без scope перестанут пересылаться, связь пропадёт. Сначала настройте все репитеры и убедитесь что пользователи выставили scope в каналах.
4. **Удаляете родительский регион до дочерних** — `region remove` требует удаления снизу вверх. Сначала города, потом край, потом страна.
5. **`region default` указан как город, а не край** — DM без scope дойдёт только до одного города. Обычно нужно указывать край.
