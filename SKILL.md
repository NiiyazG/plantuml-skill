---
name: plantuml-skill
description: Create UML diagrams and visual models using PlantUML syntax. Use when the user needs to draw sequence diagrams, class diagrams, use case diagrams, activity diagrams, component diagrams, state diagrams, deployment diagrams, Gantt charts, or BPMN-style process flows. Can generate diagrams via online editor (plant-uml-editor.vercel.app) or local PlantUML.
---

# PlantUML Skill

Create professional UML diagrams from text descriptions using PlantUML syntax.

## Workflow

1. Ask the user what type of diagram they need
2. Identify the key elements (actors, objects, states, etc.)
3. Generate PlantUML code using the templates in references/
4. Output the code — the user can paste into https://plant-uml-editor.vercel.app/ to render
5. Export as PNG/SVG

## Из ТЗ → Activity Diagram

Если пользователь даёт **текстовое описание ТЗ** с нумерованными пунктами, можешь:

1. Разобрать структуру:
   — **Простая последовательность** → шаги идут друг за другом
   — **Ветвления** (if/else) → пункты 1.2.1/1.2.2/1.2.3/1.2.4
   — **Параллельные опции** (fork) → пункты 1.3.1–1.3.4
   — **Циклы** (повтор до выбора) → пункт 1.4 (выбор конвектора из таблицы)
   — **Точки принятия решения** → экспорт, вопросы к ТП

2. Сгенерировать PlantUML код Activity Diagram с:
   — `start` / `stop`
   — `:действие;` для шагов
   — `if/else/endif` для ветвлений
   — `fork/fork again/end fork` для параллельных процессов
   — `repeat/while` для циклов
   — `note right/left` для пояснений
   — `title` — общее название из описания

3. Выдать готовый код с пояснением, куда вставить (plant-uml-editor.vercel.app)

### Пример структуры из ТЗ

Из такого ТЗ:
```
1.1. Выбор цвета
1.2. Тип боковины
  1.2.1. Стандарт
  1.2.2. Стыковочная слева
  1.2.3. Стыковочная справа
1.3. Опции
  1.3.1. Нержавейка
  1.3.2. Влажные помещения
1.4. Таблица подбора
```

Получается:
- 1.1 → последовательный шаг
- 1.2 → ветвление (choice)
- 1.3 → параллельные опции (fork)
- 1.4 → цикл подбора (repeat/while)

## Quick Reference

### Diagram Types

| Diagram | Start Tag | Best For |
|---------|-----------|----------|
| Sequence | `@startuml` | API calls, user interactions, time-based flows |
| Use Case | `@startuml` | Stakeholders + system boundaries |
| Class | `@startuml` | OOP structures, database schemas |
| Activity | `@startuml` | Business processes, algorithms |
| Component | `@startuml` | System architecture, modules |
| State | `@startuml` | Object lifecycle, status transitions |
| Deployment | `@startuml` | Physical/infrastructure layout |
| Gantt | `@startgantt` | Project timelines |

### Basic Syntax Rules

- Lines starting with `'` are comments
- `@startuml` / `@enduml` wrap every diagram
- Use `skinparam` for styling
- `note left/right of` for annotations
- `title` for diagram title

For detailed syntax, see `references/syntax.md`.

## Online Editor

The easiest way to render is https://plant-uml-editor.vercel.app/ — paste code, see live preview, export PNG/SVG.

## Example: Quick Sequence

```
@startuml
actor User
participant "Web App" as Web
participant "API" as API
database DB

User -> Web: POST /order
Web -> API: validate()
API -> DB: insert order
DB --> API: OK
API --> Web: 200
Web --> User: Success
@enduml
```
