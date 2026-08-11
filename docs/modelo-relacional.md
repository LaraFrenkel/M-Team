# Modelo relacional de M-Team

Este modelo deriva del [modelo de dominio](./modelo-de-dominio.md) y de la [matriz de trazabilidad](./matriz-de-trazabilidad.md). Utiliza nombres de tablas y columnas en `snake_case`, tablas en plural y claves foráneas con el sufijo `_id`.

## Diagrama entidad-relación

```mermaid
erDiagram
    users {
        uuid id PK
        varchar first_name
        varchar last_name
        varchar document_number UK
        date birth_date
        varchar email UK
        varchar phone
        varchar password_hash
        varchar photo_url
        user_role role
        user_status status
        boolean must_change_password
        timestamptz created_at
        timestamptz updated_at
    }

    member_profiles {
        uuid id PK
        uuid user_id FK,UK
        varchar emergency_contact_name
        varchar emergency_contact_phone
    }

    trainer_profiles {
        uuid id PK
        uuid user_id FK,UK
        varchar specialty
        text description
    }

    membership_prices {
        uuid id PK
        numeric amount
        timestamptz effective_from
        uuid created_by_id FK
        timestamptz created_at
    }

    payments {
        uuid id PK
        uuid member_id FK
        numeric amount
        varchar method
        varchar receipt_number
        payment_status status
        uuid created_by_id FK
        timestamptz created_at
        uuid confirmed_by_id FK
        timestamptz accredited_at
        timestamptz expires_at
        uuid voided_by_id FK
        timestamptz voided_at
        text void_reason
    }

    medical_certificates {
        uuid id PK
        uuid member_id FK
        varchar file_url
        medical_certificate_status status
        timestamptz uploaded_at
        uuid reviewed_by_id FK
        timestamptz reviewed_at
        text review_comment
    }

    branches {
        uuid id PK
        varchar name UK
        text description
        varchar image_url
        varchar address UK
        varchar opening_hours
        varchar phone
        numeric latitude
        numeric longitude
        boolean is_active
    }

    access_points {
        uuid id PK
        uuid branch_id FK
        varchar name
        varchar qr_token UK
        boolean is_active
    }

    access_logs {
        uuid id PK
        uuid user_id FK
        user_role role_at_attempt
        uuid branch_id FK "nullable"
        uuid access_point_id FK "nullable"
        access_result result
        access_denial_reason denial_reason
        timestamptz attempted_at
    }

    weekly_schedules {
        uuid id PK
        date week_starts_on UK
        uuid copied_from_id FK
        timestamptz created_at
    }

    scheduled_classes {
        uuid id PK
        uuid schedule_id FK
        uuid branch_id FK
        uuid trainer_id FK
        varchar activity
        timestamptz starts_at
    }

    trainer_branches {
        uuid trainer_id PK,FK
        uuid branch_id PK,FK
    }

    user_audit_logs {
        uuid id PK
        uuid user_id FK
        uuid performed_by_id FK
        user_audit_action action
        text reason
        timestamptz occurred_at
    }

    events {
        uuid id PK
        varchar title
        text description
        timestamptz starts_at
        varchar location
        varchar image_url
        event_status status
        uuid created_by_id FK
    }

    news_posts {
        uuid id PK
        varchar title
        text content
        varchar image_url
        publication_audience audience
        publication_status status
        timestamptz published_at
        uuid created_by_id FK
    }

    notifications {
        uuid id PK
        uuid user_id FK
        varchar title
        text message
        notification_type type
        timestamptz created_at
        timestamptz read_at
    }

    users ||--o| member_profiles : "has"
    users ||--o| trainer_profiles : "has"
    member_profiles ||--o{ payments : "owns"
    member_profiles ||--o{ medical_certificates : "uploads"
    users ||--o{ membership_prices : "creates"
    users ||--o{ payments : "creates/confirms/voids"
    users ||--o{ medical_certificates : "reviews"
    branches ||--o{ access_points : "contains"
    users ||--o{ access_logs : "attempts"
    branches o|--o{ access_logs : "receives"
    access_points o|--o{ access_logs : "records"
    weekly_schedules o|--o{ weekly_schedules : "copied into"
    weekly_schedules ||--o{ scheduled_classes : "contains"
    branches ||--o{ scheduled_classes : "hosts"
    trainer_profiles o|--o{ scheduled_classes : "teaches"
    trainer_profiles ||--o{ trainer_branches : "works at"
    branches ||--o{ trainer_branches : "has trainers"
    users ||--o{ user_audit_logs : "is audited/performs"
    users ||--o{ events : "creates"
    users ||--o{ news_posts : "creates"
    users ||--o{ notifications : "receives"
```

## Enumeraciones

| Enumeración | Valores |
|---|---|
| `user_role` | `MEMBER`, `TRAINER`, `ADMIN` |
| `user_status` | `ACTIVE`, `INACTIVE` |
| `payment_status` | `ACCREDITED`, `VOIDED` |
| `medical_certificate_status` | `PENDING`, `APPROVED`, `REJECTED` |
| `access_result` | `ALLOWED`, `DENIED` |
| `access_denial_reason` | `INVALID_QR`, `INACTIVE_USER`, `INACTIVE_BRANCH`, `INACTIVE_ACCESS_POINT`, `EXPIRED_MEMBERSHIP`, `MEDICAL_CERTIFICATE_REQUIRED` |
| `user_audit_action` | `CREATED`, `UPDATED`, `ACTIVATED`, `DEACTIVATED`, `PASSWORD_RESET` |
| `event_status` | `DRAFT`, `PUBLISHED`, `CANCELLED` |
| `publication_audience` | `ALL`, `MEMBERS`, `TRAINERS` |
| `publication_status` | `DRAFT`, `PUBLISHED`, `INACTIVE` |
| `notification_type` | `MEMBERSHIP_PRICE_CHANGED`, `MEMBERSHIP_EXPIRING`, `MEMBERSHIP_EXPIRED`, `MEDICAL_CERTIFICATE_REVIEWED`, `CLASS_CHANGED`, `EVENT_CANCELLED`, `GENERAL` |

El medio de pago permanece como `varchar` validado por la aplicación hasta que M-Team confirme los valores aceptados. Luego podrá convertirse en una enumeración sin modificar las relaciones.

## Restricciones principales

- `users.email` y `users.document_number` son únicos, comparando el correo normalizado en minúsculas.
- Cada usuario puede tener como máximo un perfil de socio o de entrenador, coherente con su rol.
- `membership_prices.amount` y `payments.amount` deben ser mayores que cero.
- `payments.expires_at` equivale a `accredited_at + 30 días` para pagos acreditados.
- Un pago anulado requiere `voided_by_id`, `voided_at` y `void_reason`.
- Un apto rechazado requiere `reviewed_by_id`, `reviewed_at` y `review_comment`.
- Un apto aprobado requiere responsable y fecha de revisión, pero no fecha de vencimiento.
- `branches.name`, `branches.address` y `access_points.qr_token` son únicos.
- `access_logs.denial_reason` es obligatorio solamente cuando `result = DENIED`.
- `access_logs.branch_id` y `access_logs.access_point_id` pueden ser nulos solamente cuando el QR es inexistente o fue alterado y el motivo es `INVALID_QR`.
- `weekly_schedules.week_starts_on` debe representar siempre el comienzo acordado de una semana y ser único.
- `trainer_branches` utiliza una clave primaria compuesta para evitar asignaciones duplicadas.
- Los registros históricos no se eliminan al desactivar usuarios, entrenadores, sedes o puntos de acceso.

## Índices recomendados

| Tabla | Índice |
|---|---|
| `users` | `(role, status)`, `last_name`, `first_name` |
| `membership_prices` | `effective_from DESC` |
| `payments` | `(member_id, accredited_at DESC)`, `(status, accredited_at)`, `receipt_number` |
| `medical_certificates` | `(member_id, uploaded_at DESC)`, `(status, uploaded_at)` |
| `access_logs` | `(user_id, attempted_at DESC)`, `(branch_id, attempted_at DESC)`, `(result, attempted_at)` |
| `scheduled_classes` | `(schedule_id, starts_at)`, `(trainer_id, starts_at)`, `(branch_id, starts_at)` |
| `events` | `(status, starts_at)` |
| `news_posts` | `(status, audience, published_at DESC)` |
| `notifications` | `(user_id, created_at DESC)`, `(user_id, read_at)` |

## Decisiones para la traducción a Prisma

- Las claves primarias se generarán con UUID.
- Todos los instantes se almacenarán como `timestamptz`; las fechas sin hora utilizarán `date`.
- Las relaciones con datos históricos usarán `ON DELETE RESTRICT` o `NO ACTION`.
- Los campos opcionales incluyen fotografías, comprobantes, revisiones aún no realizadas, anulaciones, coordenadas, entrenador de una clase y fecha de lectura.
- Los estados de cuota y de finalización de eventos no se almacenarán porque se calculan con la fecha actual.
