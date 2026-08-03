# 🏋️ M-Team

> Plataforma web para la gestión integral de gimnasios.

M-Team es una plataforma web desarrollada como proyecto académico para la materia **Construcción de Software** de la carrera **Ingeniería en Informática**.

Su objetivo es centralizar la administración de un gimnasio, permitiendo gestionar socios, entrenadores, cuotas, pagos, aptos médicos, sedes, clases y accesos mediante códigos QR desde una única plataforma. :contentReference[oaicite:2]{index=2}

---

## ✨ Características

- 👥 Gestión de usuarios con distintos roles:
  - Socios
  - Entrenadores
  - Administradores

- 💳 Administración de cuotas mensuales e historial de pagos.

- 📄 Gestión de aptos médicos.

- 📷 Validación de acceso mediante escaneo de códigos QR.

- 🏢 Administración de múltiples sedes.

- 📅 Cronograma semanal de clases.

- 🏋️ Gestión de entrenadores.

- 📢 Publicación de eventos, novedades y notificaciones.

- 📊 Panel administrativo con indicadores e información relevante.

---

## 🏛 Arquitectura

El proyecto sigue una arquitectura de **tres capas**:

```
Frontend
     │
 REST API
     │
 Backend
     │
 Prisma ORM
     │
 PostgreSQL
```

La comunicación entre el frontend y la base de datos se realiza exclusivamente mediante una API REST. :contentReference[oaicite:3]{index=3}

---

## 🛠 Tecnologías

### Frontend

- React
- Vite
- TypeScript

### Backend

- Node.js
- Express
- TypeScript

### Base de datos

- PostgreSQL
- Prisma ORM
- Supabase

### Autenticación

- JWT
- bcrypt

### Almacenamiento

- Supabase Storage

### Servicios externos

- Google Maps API

### Testing y documentación

- Swagger / OpenAPI
- Jest
- Supertest

### DevOps

- Git
- GitHub
- Vercel
- Render
- Supabase

:contentReference[oaicite:4]{index=4}

---

## 📁 Estructura del proyecto

```
m-team/
├── frontend/
├── backend/
├── docs/
└── README.md
```

La estructura interna del backend seguirá el patrón:

```
Routes
    ↓
Controllers
    ↓
Services
    ↓
Repositories (Prisma)
    ↓
PostgreSQL
```

---

## 🚀 Puesta en marcha

### Clonar el repositorio

```bash
git clone https://github.com/usuario/m-team.git
cd m-team
```

### Backend

```bash
cd backend
npm install
npm run dev
```

### Frontend

```bash
cd frontend
npm install
npm run dev
```

---

## 📌 Funcionalidades principales

- Registro e inicio de sesión
- Gestión de usuarios y roles
- Administración de socios
- Gestión de cuotas y pagos
- Aprobación de aptos médicos
- Control de acceso mediante QR
- Gestión de sedes
- Administración del cronograma semanal
- Gestión de entrenadores
- Gestión de eventos
- Publicación de novedades
- Notificaciones internas
- Panel administrativo

:contentReference[oaicite:5]{index=5}

---

## 🚧 Funcionalidades fuera del alcance

El proyecto **no contempla**:

- Pagos online
- Facturación electrónica
- Aplicación móvil nativa
- Control de asistencia
- Reservas de clases
- Cupos y listas de espera
- Chat o videollamadas
- Inteligencia Artificial
- Funcionamiento offline

:contentReference[oaicite:6]{index=6}

---

## 👨‍💻 Equipo

- Pablo Cannizzaro
- Lara Frenkel
- Franco Dalla Via
- Leandro Callizaya

:contentReference[oaicite:7]{index=7}

---

## 📚 Proyecto académico

Este proyecto fue desarrollado para la materia **Construcción de Software** de la carrera **Ingeniería en Informática**.

Su propósito es aplicar buenas prácticas de desarrollo de software, arquitectura en capas, diseño de APIs REST, gestión de bases de datos y trabajo colaborativo mediante Git y GitHub. :contentReference[oaicite:8]{index=8}

---

## 📄 Licencia

Proyecto desarrollado con fines exclusivamente académicos.
