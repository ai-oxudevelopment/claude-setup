# Архитектурное решение: Микросервисная архитектура

**Статус**: Accepted
**Дата**: 2026-03-27
**Автор**: Systems Analyst (SA)

## Контекст
Текущая монолитная архитектура не масштабируется под реальные проекты. Требуется переход к распределенной системе с четким разграничением зон ответственности.

## Решение
Реализовать микросервисную архитектуру с использованием Temporal в качестве orchestrator:

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   PM Domain     │    │ Processing Domain│    │ Reporting Domain │
│                 │    │                 │    │                 │
│ • Task Router   │    │ • Dev Workers    │    │ • Analytics      │
│ • Project Mgmt  │    │ • Build Queue    │    │ • Metrics        │
│ • Admin API     │    │ • Test Executor  │    │ • Reporting      │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │                       │
         └───────────────────────┼───────────────────────┘
                                 │
                    ┌─────────────────┐
                    │  Temporal Core │
                    │                 │
                    │ • Workflow SDK │
                    │ • State Mgmt   │
                    │ • Task Queue   │
                    │ • Event Bus    │
                    └─────────────────┘
```

## Альтернативы рассмотрены

### 1. AWS Step Functions
- **Плюсы**: managed service, интеграция с AWS ecosystem
- **Минусы**: vendor lock-in, сложность миграции, ограничения кастомизации
- **Причина отклонения**: Контроль над инфраструктурой важнее managed-решения

### 2. RabbitMQ + Manual Orchestration
- **Плюсы**: Простой в настройке, гибкость
- **Минусы**: Требует ручного управления состоянием, сложная логика восстановления
- **Причина отклонения**: Сложность поддержки при масштабировании

### 3. Kubernetes Operators
- **Плюсы**: Native orchestration, auto-scaling
- **Минусы**: Сложность настройки, steep learning curve
- **Причина отклонения**: Избыточно для текущих задач

## Обоснование выбора
1. **Temporal** обеспечивает state management out-of-the-box
2. **Event-driven архитектура** позволяет легко масштабировать
3. **Workflow-as-code** подход соответствует DevOps практикам
4. **Кросс-воркфлоады** позволяют координировать между сервисами

## Риски и mitigation
- **Сложность learning curve**: Training sessions, PoC implementation
- **State consistency**: Saga pattern с compensation transactions
- **Performance**: Load testing на каждом этапе

## Следующие шаги
1. Реализовать WP01: Project Structure Setup
2. Настроить Temporal кластер
3. Разработать базовые workflow templates