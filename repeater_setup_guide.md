# Пошаговая настройка репитера MeshCore

Инструкция по настройке региональной фильтрации с нуля. Подходит для чистого репитера и для переконфигурации существующего.

---

## Шаг 1. Очистка

Сбросить все региональные настройки:

```
region clear
```

После этого репитер вернётся к состоянию по умолчанию: единственный шаблонный регион `*` с флагом `F` (разрешена пересылка всего).

Проверить что очистка прошла:

```
region list
```

Должен показать только:
```
* F
```

---

## Шаг 2. Создание иерархии регионов

Команда `region put <имя> [родитель]` создаёт регион и автоматически разрешает его пересылку (`allowf`).

Порядок важен: сначала родитель, потом дочерние.

**Важно:** на каждом репитере нужно создать ВСЮ иерархию, а не только свой регион. Репитер должен знать все регионы сети, чтобы корректно обрабатывать пакеты с любым scope.

Общая иерархия для всех репитеров:

```
ru                          (Россия)
  ru-kda                    (Краснодарский край)
    ru-kda-krd              (Краснодар)
    ru-kda-gk               (Горячий Ключ)
  ru-sta                    (Ставропольский край)
    ru-sta-stv              (Ставрополь)
    ru-sta-kmv              (КМВ)
```

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
region list
```

Убедиться что все регионы созданы с флагом `F`, `home` и `default` указаны верно.

---

# Примеры для каждого города

## Краснодар

```
region clear
region put ru
region put ru-kda ru
region put ru-kda-krd ru-kda
region put ru-kda-gk ru-kda
region put ru-sta ru
region put ru-sta-stv ru-sta
region put ru-sta-kmv ru-sta
region home ru-kda-krd
region default ru-kda-krd
region save
region list
```

Ожидаемый вывод `region list`:

```
* F
ru F
ru-kda F (parent: ru)
ru-kda-krd F (parent: ru-kda) [HOME] [DEFAULT]
ru-kda-gk F (parent: ru-kda)
ru-sta F (parent: ru)
ru-sta-stv F (parent: ru-sta)
ru-sta-kmv F (parent: ru-sta)
```

---

## Горячий Ключ

```
region clear
region put ru
region put ru-kda ru
region put ru-kda-krd ru-kda
region put ru-kda-gk ru-kda
region put ru-sta ru
region put ru-sta-stv ru-sta
region put ru-sta-kmv ru-sta
region home ru-kda-gk
region default ru-kda-gk
region save
region list
```

Ожидаемый вывод:

```
* F
ru F
ru-kda F (parent: ru)
ru-kda-krd F (parent: ru-kda)
ru-kda-gk F (parent: ru-kda) [HOME] [DEFAULT]
ru-sta F (parent: ru)
ru-sta-stv F (parent: ru-sta)
ru-sta-kmv F (parent: ru-sta)
```

---

## Ставрополь

```
region clear
region put ru
region put ru-kda ru
region put ru-kda-krd ru-kda
region put ru-kda-gk ru-kda
region put ru-sta ru
region put ru-sta-stv ru-sta
region put ru-sta-kmv ru-sta
region home ru-sta-stv
region default ru-sta-stv
region save
region list
```

Ожидаемый вывод:

```
* F
ru F
ru-kda F (parent: ru)
ru-kda-krd F (parent: ru-kda)
ru-kda-gk F (parent: ru-kda)
ru-sta F (parent: ru)
ru-sta-stv F (parent: ru-sta) [HOME] [DEFAULT]
ru-sta-kmv F (parent: ru-sta)
```

---

## КМВ (Пятигорск / Ессентуки / Кисловодск)

```
region clear
region put ru
region put ru-kda ru
region put ru-kda-krd ru-kda
region put ru-kda-gk ru-kda
region put ru-sta ru
region put ru-sta-stv ru-sta
region put ru-sta-kmv ru-sta
region home ru-sta-kmv
region default ru-sta-kmv
region save
region list
```

Ожидаемый вывод:

```
* F
ru F
ru-kda F (parent: ru)
ru-kda-krd F (parent: ru-kda)
ru-kda-gk F (parent: ru-kda)
ru-sta F (parent: ru)
ru-sta-stv F (parent: ru-sta)
ru-sta-kmv F (parent: ru-sta) [HOME] [DEFAULT]
```

---

## Пограничный репитер (между Краснодарским и Ставропольским краем)

Репитер стоит на границе и обслуживает оба края. Home назначаем на ближайший город.

```
region clear
region put ru
region put ru-kda ru
region put ru-kda-krd ru-kda
region put ru-kda-gk ru-kda
region put ru-sta ru
region put ru-sta-stv ru-sta
region put ru-sta-kmv ru-sta
region home ru-kda-krd
region default ru-kda-krd
region save
region list
```

---

# Шпаргалка по командам

| Команда | Описание |
|---|---|
| `region clear` | Удалить все регионы и настройки |
| `region put <имя> [родитель]` | Создать регион + автоматически allowf |
| `region home <регион>` | Объявить местоположение репитера |
| `region default <регион>` | Scope по умолчанию для исходящих (v1.15.0+) |
| `region allowf <регион>` | Разрешить пересылку региона (обычно не нужен — put делает это сам) |
| `region denyf <регион>` | Запретить пересылку региона |
| `region list` | Показать все регионы и флаги |
| `region save` | Сохранить в энергонезависимую память |
| `region denyf *` | Закрыть шаблон — пакеты без scope отбрасываются |

---

# Типичные ошибки

1. **Забыли `region save`** — после перезагрузки всё пропадёт.
2. **Создали только свой регион** — репитер не знает другие регионы и не сможет их пересылать. Создавайте ВСЮ иерархию.
3. **Не задали `region home`** — сеть не знает маршрута до региона, пакеты теряются.
4. **`region denyf *` до того как пользователи настроили scope** — все пакеты без scope перестанут пересылаться, связь пропадёт. Сначала настройте все репитеры и убедитесь что пользователи выставили scope в каналах.
