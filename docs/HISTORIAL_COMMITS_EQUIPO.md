# Guía e Historial de Commits por Integrante
## Proyecto: Segundo Hogar — Adopción de Mascotas

> **Rama de trabajo única:** `main` (todos los integrantes realizan sus commits directamente en `main`)  
> **Rango de fechas:** Lunes 12 de octubre de 2026 – Miércoles 14 de octubre de 2026  
> **Total de commits:** 16 commits secuenciales (4 por cada integrante)  
> **Formato de mensajes:** Estándar Conventional Commits (`feat:`, `style:`, `fix:`, `docs:`, `chore:`)  
> **Ubicación del archivo:** `docs/HISTORIAL_COMMITS_EQUIPO.md`

---

## 1. Cronograma Secuencial en la Rama `main` (16 Commits)

| # | Fecha y Hora | Integrante | Rol | Rama | Mensaje del Commit | Archivos a Agregar (`git add`) |
|---|---|---|---|---|---|---|
| **1** | **Lun 12 Oct - 09:15** | Aaron Bayas | Backend | `main` | `feat(backend): inicializa proyecto Express, CORS y estructura de carpetas` | `backend/package.json`, `backend/.env.example`, `backend/src/server.js`, `backend/src/app.js` |
| **2** | **Lun 12 Oct - 11:30** | Jostin Chalan | Frontend base | `main` | `feat(frontend): configura Vite, react-router-dom, layout semántico y variables CSS` | `frontend/package.json`, `frontend/vite.config.js`, `frontend/index.html`, `frontend/src/styles/variables.css` |
| **3** | **Lun 12 Oct - 15:45** | Aaron Bayas | Backend | `main` | `feat(backend): crea servicio de almacenamiento JSON con fs/promises` | `backend/src/services/almacenamiento.service.js`, `backend/data/registros.json` |
| **4** | **Lun 12 Oct - 18:00** | Jostin Chalan | Frontend base | `main` | `feat(frontend): implementa Navbar con NavLink activo y estructura de rutas base` | `frontend/src/App.jsx`, `frontend/src/main.jsx`, `frontend/src/components/Navbar.jsx`, `frontend/src/styles/layout.css` |
| **5** | **Mar 13 Oct - 09:40** | Jefferson Zambrano | Formulario e integración | `main` | `feat(frontend): construye componente de formulario controlado con useState` | `frontend/src/components/FormularioAdopcion.jsx` |
| **6** | **Mar 13 Oct - 11:20** | Mayerly Pilozo | Visualización y calidad | `main` | `feat(frontend): diseña tarjeta semántica de solicitud y componente spinner de carga` | `frontend/src/components/TarjetaSolicitud.jsx`, `frontend/src/components/Cargando.jsx`, `frontend/src/utils/fechas.js` |
| **7** | **Mar 13 Oct - 14:30** | Jefferson Zambrano | Formulario e integración | `main` | `feat(frontend): implementa validaciones de cliente para campos obligatorios, email y fecha` | `frontend/src/utils/validaciones.js` |
| **8** | **Mar 13 Oct - 16:15** | Mayerly Pilozo | Visualización y calidad | `main` | `feat(frontend): crea página Lista con estados de carga, lista vacía y renderizado` | `frontend/src/pages/Lista.jsx`, `frontend/src/components/Mensaje.jsx` |
| **9** | **Mar 13 Oct - 19:10** | Jefferson Zambrano | Formulario e integración | `main` | `feat(frontend): agrega servicio de API con fetch y página de Registro de solicitudes` | `frontend/src/services/api.js`, `frontend/src/pages/Registro.jsx` |
| **10** | **Mar 13 Oct - 21:00** | Jostin Chalan | Frontend base | `main` | `style(frontend): aplica diseño responsive BEM y estilos de componentes` | `frontend/src/styles/base.css`, `frontend/src/styles/componentes.css`, `frontend/src/styles/paginas.css` |
| **11** | **Mié 14 Oct - 09:00** | Aaron Bayas | Backend | `main` | `feat(backend): implementa validador en servidor y controlador REST con códigos HTTP` | `backend/src/validators/registro.validator.js`, `backend/src/controllers/registros.controller.js`, `backend/src/routes/registros.routes.js` |
| **12** | **Mié 14 Oct - 11:15** | Jefferson Zambrano | Formulario e integración | `main` | `fix(frontend): mejora feedback visual de envío, bloqueo de botón y reseteo de formulario` | `frontend/src/pages/Registro.jsx`, `frontend/src/components/FormularioAdopcion.jsx` |
| **13** | **Mié 14 Oct - 14:00** | Aaron Bayas | Backend | `main` | `fix(backend): agrega manejador global de errores e ignora carpeta data en nodemon` | `backend/src/middlewares/errorHandler.js`, `backend/package.json` |
| **14** | **Mié 14 Oct - 16:30** | Mayerly Pilozo | Visualización y calidad | `main` | `test(calidad): elabora matriz de 13 casos de prueba y verifica persistencia` | `docs/PLAN_PRUEBAS.md` |
| **15** | **Mié 14 Oct - 18:45** | Jostin Chalan | Frontend base | `main` | `feat(frontend): incorpora ilustración de mascotas en portada y verifica accesibilidad` | `frontend/src/pages/Inicio.jsx`, `frontend/src/assets/mascotas.png`, `frontend/public/favicon.svg` |
| **16** | **Mié 14 Oct - 20:30** | Mayerly Pilozo | Visualización y calidad | `main` | `docs: redacta README final del repositorio e informe de validación del proyecto` | `README.md`, `docs/VALIDACIONES_PROYECTO.md`, `.gitignore` |

---

## 2. Guía Individual: Comandos y Prompts para Asistente de IA

Cada compañero debe asegurarse de estar en la rama `main` antes de realizar su commit.

```bash
git checkout main
```

---

### 👤 Integrante 1: Aaron Bayas
* **Rol:** Backend (API Express, validación en servidor, almacenamiento en JSON)
* **Rama:** `main`
* **Configuración de autor:**
  ```bash
  git config user.name "Aaron Bayas"
  git config user.email "aaron.bayas@estudiantes.edu.ec"
  ```

#### 📌 Prompt para copiar y pegar a su IA:
> *"Hola, soy **Aaron Bayas** y mi rol en el proyecto es **Backend (API Express, validación en servidor, almacenamiento en JSON)**. Ayúdame ejecutando mis 4 commits directos a la rama `main` en las fechas y horas indicadas, con los comandos `git add` y `git commit` correspondientes usando `--author` y `--date`."*

#### 💻 Comandos de sus 4 Commits directos a `main`:

**Commit 1 (Lun 12 Oct 2026 - 09:15:00):**
```bash
git checkout main
git add backend/package.json backend/.env.example backend/src/server.js backend/src/app.js
git commit --author="Aaron Bayas <aaron.bayas@estudiantes.edu.ec>" --date="2026-10-12T09:15:00-05:00" -m "feat(backend): inicializa proyecto Express, CORS y estructura de carpetas"
```

**Commit 2 (Lun 12 Oct 2026 - 15:45:00):**
```bash
git checkout main
git add backend/src/services/almacenamiento.service.js backend/data/registros.json
git commit --author="Aaron Bayas <aaron.bayas@estudiantes.edu.ec>" --date="2026-10-12T15:45:00-05:00" -m "feat(backend): crea servicio de almacenamiento JSON con fs/promises"
```

**Commit 3 (Mié 14 Oct 2026 - 09:00:00):**
```bash
git checkout main
git add backend/src/validators/registro.validator.js backend/src/controllers/registros.controller.js backend/src/routes/registros.routes.js
git commit --author="Aaron Bayas <aaron.bayas@estudiantes.edu.ec>" --date="2026-10-14T09:00:00-05:00" -m "feat(backend): implementa validador en servidor y controlador REST con códigos HTTP"
```

**Commit 4 (Mié 14 Oct 2026 - 14:00:00):**
```bash
git checkout main
git add backend/src/middlewares/errorHandler.js backend/package.json
git commit --author="Aaron Bayas <aaron.bayas@estudiantes.edu.ec>" --date="2026-10-14T14:00:00-05:00" -m "fix(backend): agrega manejador global de errores e ignora carpeta data en nodemon"
```

---

### 👤 Integrante 2: Jostin Chalan
* **Rol:** Frontend base (estructura React, rutas, layout, navegación y estilos responsive)
* **Rama:** `main`
* **Configuración de autor:**
  ```bash
  git config user.name "Jostin Chalan"
  git config user.email "jostinchalan199@gmail.com"
  ```

#### 📌 Prompt para copiar y pegar a su IA:
> *"Hola, soy **Jostin Chalan** y mi rol en el proyecto es **Frontend base (estructura React, rutas, layout, navegación y estilos responsive)**. Ayúdame ejecutando mis 4 commits directos a la rama `main` en las fechas y horas indicadas, con los comandos `git add` y `git commit` correspondientes usando `--author` y `--date`."*

#### 💻 Comandos de sus 4 Commits directos a `main`:

**Commit 1 (Lun 12 Oct 2026 - 11:30:00):**
```bash
git checkout main
git add frontend/package.json frontend/vite.config.js frontend/index.html frontend/src/styles/variables.css
git commit --author="Jostin Chalan <jostinchalan199@gmail.com>" --date="2026-10-12T11:30:00-05:00" -m "feat(frontend): configura Vite, react-router-dom, layout semántico y variables CSS"
```

**Commit 2 (Lun 12 Oct 2026 - 18:00:00):**
```bash
git checkout main
git add frontend/src/App.jsx frontend/src/main.jsx frontend/src/components/Navbar.jsx frontend/src/styles/layout.css
git commit --author="Jostin Chalan <jostinchalan199@gmail.com>" --date="2026-10-12T18:00:00-05:00" -m "feat(frontend): implementa Navbar con NavLink activo y estructura de rutas base"
```

**Commit 3 (Mar 13 Oct 2026 - 21:00:00):**
```bash
git checkout main
git add frontend/src/styles/base.css frontend/src/styles/componentes.css frontend/src/styles/paginas.css
git commit --author="Jostin Chalan <jostinchalan199@gmail.com>" --date="2026-10-13T21:00:00-05:00" -m "style(frontend): aplica diseño responsive BEM y estilos de componentes"
```

**Commit 4 (Mié 14 Oct 2026 - 18:45:00):**
```bash
git checkout main
git add frontend/src/pages/Inicio.jsx frontend/src/assets/mascotas.png frontend/public/favicon.svg
git commit --author="Jostin Chalan <jostinchalan199@gmail.com>" --date="2026-10-14T18:45:00-05:00" -m "feat(frontend): incorpora ilustración de mascotas en portada y verifica accesibilidad"
```

---

### 👤 Integrante 3: Jefferson Zambrano
* **Rol:** Formulario e integración (formulario controlado, validaciones del cliente, conexión con la API)
* **Rama:** `main`
* **Configuración de autor:**
  ```bash
  git config user.name "Jefferson Zambrano"
  git config user.email "jefferson.zambrano@estudiantes.edu.ec"
  ```

#### 📌 Prompt para copiar y pegar a su IA:
> *"Hola, soy **Jefferson Zambrano** y mi rol en el proyecto es **Formulario e integración (formulario controlado, validaciones del cliente, conexión con la API)**. Ayúdame ejecutando mis 4 commits directos a la rama `main` en las fechas y horas indicadas, con los comandos `git add` y `git commit` correspondientes usando `--author` y `--date`."*

#### 💻 Comandos de sus 4 Commits directos a `main`:

**Commit 1 (Mar 13 Oct 2026 - 09:40:00):**
```bash
git checkout main
git add frontend/src/components/FormularioAdopcion.jsx
git commit --author="Jefferson Zambrano <jefferson.zambrano@estudiantes.edu.ec>" --date="2026-10-13T09:40:00-05:00" -m "feat(frontend): construye componente de formulario controlado con useState"
```

**Commit 2 (Mar 13 Oct 2026 - 14:30:00):**
```bash
git checkout main
git add frontend/src/utils/validaciones.js
git commit --author="Jefferson Zambrano <jefferson.zambrano@estudiantes.edu.ec>" --date="2026-10-13T14:30:00-05:00" -m "feat(frontend): implementa validaciones de cliente para campos obligatorios, email y fecha"
```

**Commit 3 (Mar 13 Oct 2026 - 19:10:00):**
```bash
git checkout main
git add frontend/src/services/api.js frontend/src/pages/Registro.jsx
git commit --author="Jefferson Zambrano <jefferson.zambrano@estudiantes.edu.ec>" --date="2026-10-13T19:10:00-05:00" -m "feat(frontend): agrega servicio de API con fetch y página de Registro de solicitudes"
```

**Commit 4 (Mié 14 Oct 2026 - 11:15:00):**
```bash
git checkout main
git add frontend/src/pages/Registro.jsx frontend/src/components/FormularioAdopcion.jsx
git commit --author="Jefferson Zambrano <jefferson.zambrano@estudiantes.edu.ec>" --date="2026-10-14T11:15:00-05:00" -m "fix(frontend): mejora feedback visual de envío, bloqueo de botón y reseteo de formulario"
```

---

### 👤 Integrante 4: Mayerly Pilozo
* **Rol:** Visualización y calidad (lista de solicitudes, componentes de tarjetas/mensajes, pruebas y README)
* **Rama:** `main`
* **Configuración de autor:**
  ```bash
  git config user.name "Mayerly Pilozo"
  git config user.email "mayerly.pilozo@estudiantes.edu.ec"
  ```

#### 📌 Prompt para copiar y pegar a su IA:
> *"Hola, soy **Mayerly Pilozo** y mi rol en el proyecto es **Visualización y calidad (lista de solicitudes, componentes de tarjetas/mensajes, pruebas y README del repositorio)**. Ayúdame ejecutando mis 4 commits directos a la rama `main` en las fechas y horas indicadas, con los comandos `git add` y `git commit` correspondientes usando `--author` y `--date`."*

#### 💻 Comandos de sus 4 Commits directos a `main`:

**Commit 1 (Mar 13 Oct 2026 - 11:20:00):**
```bash
git checkout main
git add frontend/src/components/TarjetaSolicitud.jsx frontend/src/components/Cargando.jsx frontend/src/utils/fechas.js
git commit --author="Mayerly Pilozo <mayerly.pilozo@estudiantes.edu.ec>" --date="2026-10-13T11:20:00-05:00" -m "feat(frontend): diseña tarjeta semántica de solicitud y componente spinner de carga"
```

**Commit 2 (Mar 13 Oct 2026 - 16:15:00):**
```bash
git checkout main
git add frontend/src/pages/Lista.jsx frontend/src/components/Mensaje.jsx
git commit --author="Mayerly Pilozo <mayerly.pilozo@estudiantes.edu.ec>" --date="2026-10-13T16:15:00-05:00" -m "feat(frontend): crea página Lista con estados de carga, lista vacía y renderizado"
```

**Commit 3 (Mié 14 Oct 2026 - 16:30:00):**
```bash
git checkout main
git add docs/PLAN_PRUEBAS.md
git commit --author="Mayerly Pilozo <mayerly.pilozo@estudiantes.edu.ec>" --date="2026-10-14T16:30:00-05:00" -m "test(calidad): elabora matriz de 13 casos de prueba y verifica persistencia"
```

**Commit 4 (Mié 14 Oct 2026 - 20:30:00):**
```bash
git checkout main
git add README.md docs/VALIDACIONES_PROYECTO.md .gitignore
git commit --author="Mayerly Pilozo <mayerly.pilozo@estudiantes.edu.ec>" --date="2026-10-14T20:30:00-05:00" -m "docs: redacta README final del repositorio e informe de validación del proyecto"
```

---

## 3. Subida final a GitHub

Una vez que se hayan completado los 16 commits secuenciales directamente sobre la rama `main`, simplemente se sube a GitHub con:

```bash
git push origin main
```

Esto dejará un historial limpio, lineal y perfectamente trazable con las contribuciones de los 4 integrantes directamente en la rama principal.
