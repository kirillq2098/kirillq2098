# Achievements

Только подтверждённые или аккуратно сформулированные достижения. Если факт нельзя доказать репозиторием, runtime evidence или документированной метрикой, он сюда не добавляется.

## Engineering

- Спроектирован и реализован backend-контур сервисных заявок на FastAPI + PostgreSQL + SQLAlchemy 2 async + Alembic.
- Реализована DB-backed idempotency для входящих integration events.
- Реализована транзакционная обработка событий с разделением бизнес-изменений и технических failure states.
- Реализована retry-модель для временных интеграционных ошибок.
- Реализован LeadRouter с LLM-классификацией и маршрутизацией обращений.
- Интеграция с Bitrix24 REST оформлена с отдельным read-only discovery и safety constraints.
- GitHub Actions CI используется для main service и LeadRouter.

## Test evidence

- **531 тест** основного сервиса.
- **590 тестов** LeadRouter.

> Число тестов — это evidence масштаба проверки, а не самостоятельная метрика качества продукта.

## Product

### FlyPingAvia

- Публичный продуктовый репозиторий с Telegram-ботом и Mini App.
- Реализована signed Telegram initData authentication.
- Реализованы фоновые проверки цен и price alerts.
- Добавлены deep-link attribution и affiliate-механика.
- Добавлен эксплуатационный health monitoring.
- Подготовлена отдельная документация по Product Vision, Strategy, Architecture, Security, KPI, Monetization и Roadmap.

## Architecture & process

- В проектной документации разделяются `PRODUCTION CONFIRMED`, `IMPLEMENTED`, `SCAFFOLD / INCOMPLETE` и `UNKNOWN`.
- Для AI-assisted разработки используется evidence-first подход: агент не должен повышать статус функции только на основании наличия кода.
- Для внутренних проектов поддерживаются архитектурные документы, правила для AI-агентов и воспроизводимые development/CI workflows.

## Metrics to earn next

Следующие достижения должны появиться не как формулировки, а как измеренные показатели:

- accuracy / precision / recall AI-классификатора на размеченном наборе;
- доля лидов, обработанных без участия человека;
- средняя стоимость LLM-классификации одного обращения;
- время обработки заявки до/после автоматизации;
- снижение количества ручных операций;
- production error rate и availability;
- число активных пользователей FlyPing;
- conversion alert → purchase / affiliate click, если измерение доступно;
- экономический эффект внутренних автоматизаций.
