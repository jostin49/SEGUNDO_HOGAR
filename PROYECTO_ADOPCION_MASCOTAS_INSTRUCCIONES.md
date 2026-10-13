# Proyecto: Adopción de Mascotas — Guía Técnica y Roles

> **Materia:** Desarrollo Web — Ingeniería de Software
> **Tema:** Adopción de mascotas
> **Alcance:** Registrar y consultar solicitudes de adopción con solicitante, mascota, fecha y especie.
> **Nombre del aplicativo:** `Segundo Hogar` (identidad visual y diseño en la sección 13)

---

## 0. INSTRUCCIONES PARA EL AGENTE DE IA

Este documento es la fuente de verdad del **aplicativo**. Al usarlo:

1. **Respeta el stack y los requisitos obligatorios** (sección 2). No agregues autenticación, pagos, base de datos SQL/Mongo ni nada fuera del alcance.
2. **Trabaja por fases y por rol** (secciones 5 y 6). Pregunta primero qué integrante eres y entrega solo lo de su rol, salvo que se pida otra cosa.
3. **Explica cada decisión en lenguaje simple.** Cada integrante debe entender el código que escribe y cómo encaja con el resto (frontend, backend e integración).
4. **Código claro y comentado**, con archivos de responsabilidad única. Prefiere simplicidad sobre sofisticación.
5. **Nunca subas** `node_modules`, `.env` con credenciales ni datos sensibles.
6. Avanza en **pasos pequeños y probables**, para que se pueda ir mostrando el código mientras se escribe.
7. Al terminar cada fase, entrega un **mini-resumen** (qué se hizo, archivos tocados, cómo probarlo).
8. **Sin emojis en el aplicativo.** Toda la interfaz (títulos, botones, mensajes, tarjetas, placeholders, favicon) usa **iconos vectoriales** de `lucide-react`, nunca emojis ni caracteres emoji (sección 13.4).
9. **Sigue la identidad visual** (nombre, paleta, tipografía, espaciados) de la sección 13 y los textos de la sección 15. No inventes colores ni estilos fuera de esa paleta.
10. Este documento describe **todo lo que debe tener el aplicativo**: pantallas (sección 14), textos (sección 15) y comportamiento. Si algo no está aquí, pregunta antes de agregarlo.
11. **Código de interfaz según la sección 13.5 a 13.8:** HTML5 semántico, CSS propio (sin frameworks de estilos), todas las decisiones visuales en CSS Custom Properties y diseño responsive para computadora, tablet y móvil sin desbordamiento horizontal.

---

## 1. EQUIPO Y ROLES (resumen)

| # | Integrante | Responsabilidad sobre el aplicativo |
|---|------------|--------------------------------------|
| 1 | **Aaron Bayas** | **Backend:** API Express, validación en servidor, almacenamiento en JSON |
| 2 | **Jostin Chalan** | **Frontend base:** estructura React, rutas, layout, navegación y estilos responsive |
| 3 | **Jefferson Zambrano** | **Formulario e integración:** formulario controlado, validaciones del cliente, conexión con la API |
| 4 | **Mayerly Pilozo** | **Visualización y calidad:** lista de solicitudes, componentes de tarjetas/mensajes, pruebas y README del repositorio |

> Todos contribuyen con código y deben entender el funcionamiento completo (frontend, backend e integración). Ver revisión cruzada en la sección 6.5.

---

## 2. REQUISITOS TÉCNICOS OBLIGATORIOS

**Flujo de extremo a extremo:**
`Formulario React → petición HTTP (fetch/axios) → API Express → archivo JSON → consulta (GET) → visualización en React`

**Frontend (React SPA)**
- Mínimo 3 rutas: `/` (bienvenida y navegación), `/registro` (formulario), `/lista` (registros desde la API).
- Enrutador (`react-router-dom`) y componentes reutilizables (menú, formulario, tarjeta, mensajes).
- Formulario controlado por estado de React con: **nombre, email, descripción, fecha, categoría** (+ datos propios del tema).
- Validaciones: campos obligatorios, formato de email, condiciones de fecha. Mensajes comprensibles; no enviar datos inválidos.
- `fetch` o `axios`: POST para crear, GET para listar. Estados de **carga, lista vacía, confirmación y error**; actualizar la vista tras registrar.
- Interfaz ordenada, legible y **responsive**. Navegación para volver al formulario o a la lista.

**Backend (Node.js + Express)**
- `GET /registros` y `POST /registros`. JSON en peticiones y respuestas.
- Validación también en servidor. **ID único** por registro. Códigos: `200` consulta, `201` creación, `400` datos inválidos (y `500` para errores internos controlados).
- Errores de lectura/escritura del archivo sin tumbar el servidor. **CORS** configurado.
- Rutas y lógica de lectura/escritura organizadas; documentar cómo React se conecta a la URL de la API.

**Almacenamiento**
- Archivo JSON persistente. Crear un registro sin perder los anteriores. Si el archivo no existe, inicializarlo con `[]`.
- Verificar: creación, consulta, rechazo de datos inválidos y **persistencia tras reiniciar el backend**.

**Repositorio Git**
- Un solo repo con carpetas separadas `frontend/` y `backend/`.
- `package.json`, `package-lock.json`, `.gitignore`, `README.md` (requisitos, instalación, comandos, puertos, ejemplos de uso de la API).
- Commits con mensajes claros y aportes identificables de los **4** integrantes.

---

## 3. ADAPTACIÓN DEL TEMA: ADOPCIÓN DE MASCOTAS

**Problema/necesidad:** Los refugios y personas que rescatan mascotas necesitan recibir y consultar solicitudes de adopción de forma ordenada.

**Proceso principal:** una persona interesada registra una **solicitud de adopción** para una mascota concreta y luego las solicitudes se consultan en una lista.

### 3.1 Modelo de datos (registro)

| Campo (JSON) | Tipo | Obligatorio | Descripción |
|--------------|------|-------------|-------------|
| `id` | string | auto | ID único generado en el servidor (`crypto.randomUUID()`) |
| `nombre` | string | sí | Nombre del solicitante (2–60 caracteres) |
| `email` | string | sí | Email del solicitante (formato válido) |
| `mascota` | string | sí | Nombre o identificador de la mascota a adoptar (2–60) |
| `especie` | string | sí | **Categoría:** `Perro`, `Gato`, `Conejo`, `Ave`, `Otro` |
| `descripcion` | string | sí | Motivo de la adopción y condiciones del hogar (10–500) |
| `fecha` | string `YYYY-MM-DD` | sí | Fecha propuesta para la visita/entrega de la mascota |
| `creadoEn` | string ISO | auto | Fecha y hora de creación (servidor) |

**Reglas de fecha:** no puede ser anterior a hoy. (Opcional: no más de 1 año a futuro.)

**Ejemplo de registro:**
```json
{
  "id": "b7f0c2e4-5a1d-4a73-9e4f-2c6d9d1a8e10",
  "nombre": "María López",
  "email": "maria@correo.com",
  "mascota": "Max",
  "especie": "Perro",
  "descripcion": "Tengo casa con patio y experiencia con perros medianos.",
  "fecha": "2026-11-05",
  "creadoEn": "2026-10-12T15:30:00.000Z"
}
```

---

## 4. STACK, PUERTOS Y ESTRUCTURA

| Capa | Tecnología |
|------|------------|
| Frontend | React + Vite, `react-router-dom`, `lucide-react` (iconos), `@fontsource/source-sans-3` y `@fontsource/source-serif-4` (tipografías), fetch (o axios), CSS plano con variables |
| Backend | Node.js 18+, Express, `cors` (opcional `nodemon`) |
| Datos | `backend/data/registros.json` |
| Versionado | Git + GitHub |

- **Backend:** `http://localhost:4000`
- **Frontend:** `http://localhost:5173`
- URL de la API en el frontend vía `VITE_API_URL=http://localhost:4000` (`frontend/.env`, con `.env.example` en el repo).

```
segundo-hogar/
├── README.md
├── .gitignore
├── docs/
│   └── PLAN_PRUEBAS.md
├── backend/
│   ├── package.json
│   ├── package-lock.json
│   ├── .env.example              # PORT=4000
│   ├── data/
│   │   └── registros.json        # se crea con [] si no existe
│   └── src/
│       ├── server.js             # arranque y puerto
│       ├── app.js                # express, cors, json, rutas, manejador de errores
│       ├── routes/
│       │   └── registros.routes.js
│       ├── controllers/
│       │   └── registros.controller.js
│       ├── services/
│       │   └── almacenamiento.service.js
│       ├── validators/
│       │   └── registro.validator.js
│       └── middlewares/
│           └── errorHandler.js
└── frontend/
    ├── package.json
    ├── package-lock.json
    ├── .env.example              # VITE_API_URL=http://localhost:4000
    ├── index.html
    ├── public/
    │   └── favicon.svg       # icono vectorial (sin emojis)
    └── src/
        ├── main.jsx              # BrowserRouter
        ├── App.jsx               # rutas + layout
        ├── pages/
        │   ├── Inicio.jsx        # /
        │   ├── Registro.jsx      # /registro
        │   └── Lista.jsx         # /lista
        ├── components/
        │   ├── Navbar.jsx
        │   ├── FormularioAdopcion.jsx
        │   ├── TarjetaSolicitud.jsx
        │   ├── Mensaje.jsx       # éxito / error / info
        │   └── Cargando.jsx
        ├── services/
        │   └── api.js            # obtenerRegistros(), crearRegistro()
        ├── utils/
        │   └── validaciones.js
        └── styles/
            ├── variables.css     # CSS Custom Properties (única fuente de decisiones visuales)
            ├── base.css          # reset, tipografía y estilos de elementos HTML
            ├── layout.css        # contenedor, navbar, footer, grillas
            ├── componentes.css   # botones, formularios, tarjetas, mensajes
            └── paginas.css       # ajustes específicos de cada página
```

---

## 5. PLAN DE TRABAJO POR FASES

| Fase | Qué se hace | Responsables |
|------|-------------|--------------|
| 0. Preparación | Repo, ramas, README inicial, acuerdo del contrato de la API (sección 7) | Aaron (repo), todos (contrato) |
| 1. Esqueletos | Backend base corriendo + frontend base con rutas | Aaron / Jostin |
| 2. Backend funcional | Validador, servicio JSON, GET/POST | Aaron |
| 3. Frontend funcional | Formulario + validaciones + lista + estilos | Jefferson / Mayerly / Jostin |
| 4. Integración | Conectar React ↔ API, CORS, estados de carga/error | Jefferson + Aaron |
| 5. Pruebas | Plan de pruebas, persistencia tras reinicio | Mayerly (todos apoyan) |
| 6. Cierre | README final, revisión del código, grabación del video | Todos |

> Hagan la fase 1 juntos para que todos conozcan la estructura inicial.

---

## 6. ROLES Y RESPONSABILIDADES DETALLADAS

### 6.1 Aaron Bayas — Backend (API y almacenamiento)

**Se hace cargo de:** todo lo que ocurre en el servidor, desde que llega la petición hasta que se guarda o se lee el archivo JSON. Además actúa como líder técnico del repositorio.

**Tareas:**
- [ ] Crear el repo en GitHub, ramas y `.gitignore`; dar acceso al resto del equipo.
- [ ] Inicializar `backend/` (`npm init`, instalar `express`, `cors`, opcional `nodemon`; usar `"type": "module"`).
- [ ] `app.js` / `server.js`: parser JSON, CORS (origen `http://localhost:5173`), rutas, manejador global de errores.
- [ ] `almacenamiento.service.js`: leer y escribir `registros.json` con `fs/promises`; crear carpeta/archivo con `[]` si no existe; capturar errores sin detener el servidor.
- [ ] `registro.validator.js`: validación en servidor (reglas de la sección 7).
- [ ] `registros.controller.js` + `registros.routes.js`: `GET /registros` (200) y `POST /registros` (201 / 400 / 500); generar `id` con `crypto.randomUUID()` y `creadoEn`.
- [ ] Probar la API con Postman / Thunder Client / `curl`.

**Entiende y puede explicar:** cómo se genera el ID, qué pasa si el archivo no existe o está corrupto, por qué se valida también en el servidor, qué es CORS.

### 6.2 Jostin Chalan — Frontend base (estructura, rutas, layout, estilos)

**Se hace cargo de:** el esqueleto de la aplicación React y de toda la apariencia y navegación.

**Tareas:**
- [ ] Crear `frontend/` con Vite + React; instalar `react-router-dom`.
- [ ] `main.jsx` con `BrowserRouter`; `App.jsx` con `Routes`: `/`, `/registro`, `/lista` (+ ruta 404 opcional).
- [ ] `Navbar.jsx` con enlaces a Inicio, Registro y Lista (`NavLink` para marcar la ruta activa).
- [ ] `pages/Inicio.jsx`: bienvenida, breve explicación del proceso y botones hacia `/registro` y `/lista`.
- [ ] Estructura HTML semántica del layout (`header`, `nav`, `main`, `footer`) y `index.html` con `lang="es"`, `meta viewport` y `title` (sección 13.5).
- [ ] Crear los archivos de `styles/` (`variables.css`, `base.css`, `layout.css`, `componentes.css`, `paginas.css`) con **CSS propio**, sin frameworks, e importarlos en `main.jsx` en ese orden (sección 13.6).
- [ ] Declarar **todas las CSS Custom Properties** de la sección 13.2 y usarlas en cada regla: ningún color, tamaño de letra, espaciado, radio o sombra "suelto" (sección 13.7).
- [ ] Diseño **responsive** mobile-first con Flexbox, CSS Grid, Media Queries y unidades relativas, sin desbordamiento horizontal (sección 13.8).
- [ ] Instalar `lucide-react` y las fuentes; crear `favicon.svg`; poner el título de cada ruta (`document.title`).
- [ ] `Cargando.jsx` y estructura de layout (header / main / footer).

**Entiende y puede explicar:** cómo funciona el enrutado en una SPA, cómo agregar una nueva ruta, cómo se logró el diseño responsive.

### 6.3 Jefferson Zambrano — Formulario e integración con la API

**Se hace cargo de:** capturar los datos del usuario, validarlos y enviarlos al backend; es el puente entre React y la API.

**Tareas:**
- [ ] `FormularioAdopcion.jsx`: formulario **controlado** con `useState` y marcado semántico (`form`, `label`, `fieldset`; sección 13.5) (nombre, email, mascota, especie, descripcion, fecha).
- [ ] `utils/validaciones.js`: obligatorios, email, longitud mínima, fecha no pasada; mensajes claros por campo; **bloquear el envío** si hay errores.
- [ ] `services/api.js`: `obtenerRegistros()` (GET) y `crearRegistro(datos)` (POST) usando `import.meta.env.VITE_API_URL`; manejo de `response.ok` y errores de red.
- [ ] `pages/Registro.jsx`: estados `enviando`, `exito`, `error`; limpiar el formulario tras el éxito y ofrecer ir a `/lista`.
- [ ] Acordar con Aaron el **contrato de la API** (campos y códigos) y verificar que CORS funcione.

**Entiende y puede explicar:** cómo se controla el estado del formulario, dónde se define la URL de la API, qué pasa si el backend está apagado o responde 400.

### 6.4 Mayerly Pilozo — Visualización, pruebas y README

**Se hace cargo de:** mostrar los registros obtenidos de la API, de asegurar que todo funcione y de dejar el proyecto ejecutable por cualquiera.

**Tareas:**
- [ ] `TarjetaSolicitud.jsx` y `Mensaje.jsx` (componentes reutilizables) con **iconos de `lucide-react`**, los textos de la sección 15 y marcado semántico (`article`, `dl`, `time`; sección 13.5). Sus estilos van en `componentes.css` usando solo variables CSS.
- [ ] `pages/Lista.jsx`: `useEffect` para el GET al cargar; estados **cargando / lista vacía / error / lista con datos**; mostrar los más recientes primero; botón "Reintentar".
- [ ] `docs/PLAN_PRUEBAS.md` y ejecución de las pruebas (sección 9).
- [ ] `README.md` del repositorio (sección 11) y comprobar que el proyecto arranca siguiéndolo desde cero.

**Entiende y puede explicar:** cómo la lista consume el GET, cómo se actualiza tras registrar, qué casos de prueba se hicieron, cómo se ejecuta todo desde cero.

### 6.5 Revisión cruzada (todos entienden el sistema completo)

| Revisor | Revisa el trabajo de | Debe poder explicar |
|---------|---------------------|---------------------|
| Aaron | Jefferson | Cómo el formulario llega al POST |
| Jostin | Aaron | Qué hace cada ruta y archivo del backend |
| Jefferson | Mayerly | Cómo la lista consume el GET |
| Mayerly | Jostin | Cómo se definen las rutas y el layout |

Cada Pull Request debe ser aprobado por su revisor antes del merge.

---

## 7. CONTRATO DE LA API

### `GET /registros`
- **200 OK** → arreglo JSON (puede ser `[]`).

### `POST /registros`
**Body (JSON):**
```json
{
  "nombre": "María López",
  "email": "maria@correo.com",
  "mascota": "Max",
  "especie": "Perro",
  "descripcion": "Tengo casa con patio y experiencia con perros medianos.",
  "fecha": "2026-11-05"
}
```
- **201 Created** → el registro creado (con `id` y `creadoEn`).
- **400 Bad Request** → `{ "error": "Datos inválidos", "detalles": ["El email no tiene un formato válido"] }`
- **500 Internal Server Error** → `{ "error": "No se pudo guardar el registro" }` (sin detener el servidor).

### Reglas de validación (cliente **y** servidor, mismas reglas)
| Campo | Regla |
|-------|-------|
| `nombre` | obligatorio, 2–60 caracteres (trim) |
| `email` | obligatorio, formato válido (`/^[^\s@]+@[^\s@]+\.[^\s@]+$/`) |
| `mascota` | obligatorio, 2–60 caracteres |
| `especie` | obligatorio, uno de: Perro, Gato, Conejo, Ave, Otro |
| `descripcion` | obligatorio, 10–500 caracteres |
| `fecha` | obligatorio, formato `YYYY-MM-DD`, fecha válida, **no anterior a hoy** |

> El servidor **ignora campos extra**, guarda solo los campos del modelo y nunca confía en un `id` enviado por el cliente.

### Esqueleto sugerido del servicio de almacenamiento
```js
// backend/src/services/almacenamiento.service.js
import fs from 'fs/promises';
import path from 'path';
import { fileURLToPath } from 'url';

const __dirname = path.dirname(fileURLToPath(import.meta.url));
const RUTA = path.join(__dirname, '../../data/registros.json');

async function asegurarArchivo() {
  try { await fs.access(RUTA); }
  catch {
    await fs.mkdir(path.dirname(RUTA), { recursive: true });
    await fs.writeFile(RUTA, '[]', 'utf-8');
  }
}

export async function leerRegistros() {
  await asegurarArchivo();
  const contenido = await fs.readFile(RUTA, 'utf-8');
  return JSON.parse(contenido); // si falla, el controlador responde 500 sin tumbar el servidor
}

export async function guardarRegistro(nuevo) {
  const registros = await leerRegistros();
  registros.push(nuevo);
  await fs.writeFile(RUTA, JSON.stringify(registros, null, 2), 'utf-8');
  return nuevo;
}
```

---

## 8. REQUISITOS DE INTERFAZ (UX)

> Resumen general. El detalle completo de cada pantalla está en la sección 14 y los textos en la sección 15.

- `/` Inicio: título, descripción del proceso, dos botones (Registrar solicitud / Ver solicitudes).
- `/registro`: etiquetas visibles, errores bajo cada campo, botón deshabilitado mientras envía, mensaje de éxito con enlace a `/lista`.
- `/lista`: tarjetas con mascota, especie, solicitante, email, fecha y descripción; spinner al cargar; "Aún no hay solicitudes" si está vacía; mensaje de error con "Reintentar" si la API falla.
- Navbar siempre visible; diseño legible en móvil (≥ 320 px).

---

## 9. PLAN DE PRUEBAS

| # | Prueba | Pasos | Resultado esperado |
|---|--------|-------|--------------------|
| 1 | Registro válido | Llenar el formulario correctamente | Mensaje de éxito; aparece en `/lista`; 201 en la API |
| 2 | Campos vacíos | Enviar vacío | Errores por campo; no se hace la petición |
| 3 | Email inválido | `maria@` | Mensaje de formato de email |
| 4 | Fecha pasada | Fecha de ayer | Mensaje de fecha inválida |
| 5 | Rechazo en servidor | POST con Postman/curl con datos inválidos | 400 con detalles |
| 6 | Consulta | GET `/registros` | 200 con arreglo |
| 7 | Lista vacía | `registros.json` = `[]` | "Aún no hay solicitudes" |
| 8 | Error de red | Apagar backend y abrir `/lista` | Mensaje de error + Reintentar |
| 9 | Persistencia | Crear registros → reiniciar backend → consultar | Los registros siguen ahí |
| 10 | Archivo inexistente | Borrar `registros.json` y arrancar | Se crea con `[]` |
| 11 | Responsive | Probar en 320, 375, 768, 1024 y 1440 px | Diseño adaptado a móvil, tablet y computadora; sin desplazamiento horizontal |
| 12 | HTML semántico | Revisar la estructura de las tres pantallas | Un `h1` por página, `header`/`nav`/`main`/`footer`, formulario con `label` |
| 13 | Variables CSS | Cambiar `--color-primario` en `variables.css` | El color cambia en todo el aplicativo; no quedan colores literales en los demás archivos |

---

## 10. FLUJO DE GIT

- Ramas: `main` (estable), `dev` (integración) y una rama por tarea: `feature/backend-api`, `feature/frontend-rutas`, `feature/formulario-integracion`, `feature/lista-pruebas`.
- Pull Requests con revisión cruzada (6.5).
- Mensajes de commit claros: `feat:`, `fix:`, `docs:`, `style:`, `test:`, `chore:`.
  - Ej.: `feat(backend): agrega validación de solicitud de adopción`
  - Ej.: `feat(frontend): crea formulario controlado con validaciones`
- **Cada integrante hace commits propios** con su usuario de Git (`git config user.name` y `user.email`).
- `.gitignore` mínimo:
```
node_modules/
.env
dist/
*.log
.DS_Store
```

---

## 11. PLANTILLA DEL README.md

```markdown
# Segundo Hogar
Aplicación web para registrar y consultar solicitudes de adopción de mascotas.

## Integrantes y aportes
- Aaron Bayas — Backend y API
- Jostin Chalan — Frontend base (rutas, layout, estilos)
- Jefferson Zambrano — Formulario e integración con la API
- Mayerly Pilozo — Visualización, pruebas y documentación

## Requisitos
- Node.js 18 o superior, npm, Git

## Instalación y ejecución
### Backend (puerto 4000)
cd backend
npm install
npm run dev      # o: npm start

### Frontend (puerto 5173)
cd frontend
cp .env.example .env
npm install
npm run dev

## Conexión React ↔ API
El frontend lee la URL desde `VITE_API_URL` (por defecto http://localhost:4000).

## API
GET  /registros  → 200 lista de solicitudes
POST /registros  → 201 creada | 400 datos inválidos

## Flujo de datos
Formulario → fetch POST → Express → registros.json → fetch GET → Lista

## Pruebas realizadas
(resumen o enlace a docs/PLAN_PRUEBAS.md)
```

**Ejemplos curl para el README:**
```bash
curl http://localhost:4000/registros

curl -X POST http://localhost:4000/registros \
  -H "Content-Type: application/json" \
  -d '{"nombre":"María López","email":"maria@correo.com","mascota":"Max","especie":"Perro","descripcion":"Tengo casa con patio y experiencia.","fecha":"2026-11-05"}'
```

---

## 12. CHECKLIST DE LA APLICACIÓN

- [ ] 3 rutas: `/`, `/registro`, `/lista`
- [ ] Formulario controlado con validaciones y mensajes
- [ ] POST y GET con fetch/axios; estados de carga, vacío, éxito y error
- [ ] API con GET/POST, validación en servidor, ID único, códigos 200/201/400
- [ ] CORS configurado; errores de archivo controlados
- [ ] JSON persistente; init con `[]`; persistencia comprobada tras reinicio
- [ ] Interfaz responsive
- [ ] Paleta, tipografías y espaciados de la sección 13 aplicados; sin colores fuera de la paleta
- [ ] HTML5 semántico (`header`, `nav`, `main`, `section`, `article`, `footer`, `form`, `label`, `time`) y un solo `h1` por pantalla
- [ ] CSS propio, sin frameworks de estilos, organizado en `variables.css`, `base.css`, `layout.css`, `componentes.css` y `paginas.css`
- [ ] Custom Properties para colores, tipografía, espaciados, bordes, radios, sombras y tamaños, usadas en todos los componentes
- [ ] Responsive para computadora, tablet y móvil con Flexbox, Grid, Media Queries y unidades relativas; sin desbordamiento horizontal
- [ ] Cero emojis en el aplicativo; solo iconos `lucide-react`
- [ ] Textos de la interfaz según la sección 15
- [ ] `frontend/` y `backend/` separados
- [ ] `package.json`, `package-lock.json`, `.gitignore` y README completo
- [ ] Sin `node_modules` ni credenciales en el repo
- [ ] Commits claros de los 4 integrantes
- [ ] README probado desde cero

---

## 13. IDENTIDAD VISUAL Y DISEÑO

### 13.1 Nombre del aplicativo

**Nombre elegido: Segundo Hogar**

- Significado: cada adopción es la oportunidad de que una mascota tenga un segundo hogar. Es corto, claro y adecuado para el tema.
- Descriptor (subtítulo): *Solicitudes de adopción de mascotas*.
- Logo: solo texto "Segundo Hogar" en la tipografía de títulos, acompañado del icono `PawPrint` de `lucide-react` en color primario. No se usan imágenes ni emojis.
- Alternativas por si el nombre se repite en el curso: *Casa Huella*, *Lazo Animal*, *Hogar y Huella*. Si se cambia, reemplazar en todo el proyecto (navbar, títulos, README, `package.json`).

### 13.2 Paleta de colores

Criterio: tonos sobrios y cálidos (verde petróleo + terracota sobre fondos hueso), con contraste accesible. Se evitan los degradados, los morados/índigos intensos, los neones y los azules eléctricos, que dan aspecto de plantilla genérica.

| Rol | Nombre | HEX | Uso |
|-----|--------|-----|-----|
| Primario | Verde petróleo | `#1F4E4A` | Navbar, botones principales, títulos, enlaces |
| Primario (hover) | Verde petróleo oscuro | `#173B38` | Hover/activo del primario |
| Primario suave | Verde niebla | `#E3EDEB` | Fondos de iconos, badges, filas destacadas |
| Acento | Terracota | `#A9532E` | Llamadas a la acción secundarias, detalles, números de pasos |
| Acento (hover) | Terracota oscuro | `#8E4324` | Hover del acento |
| Fondo | Hueso | `#F6F3EE` | Fondo general de la página |
| Superficie | Blanco | `#FFFFFF` | Tarjetas, formularios, inputs |
| Borde | Arena | `#E1DBD0` | Bordes de tarjetas e inputs, separadores |
| Texto | Carbón verdoso | `#1E2926` | Texto principal |
| Texto secundario | Gris piedra | `#5E6A66` | Etiquetas, ayudas, fechas |
| Éxito | Verde bosque | `#2F7D4F` | Mensaje de registro correcto |
| Error | Rojo ladrillo | `#B3392F` | Errores de validación y de conexión |
| Advertencia | Ocre | `#B7791F` | Avisos (si se usan) |
| Información | Azul acero | `#2C6A8A` | Mensajes informativos, anillo de foco |

**Reglas de uso:**
- Proporción aproximada: 60 % neutros (hueso, blanco, arena), 30 % verde petróleo, 10 % terracota.
- Texto sobre primario y acento: blanco `#FFFFFF` (contraste mayor a 4.5:1 en ambos).
- Los colores de estado (éxito, error, advertencia, información) se usan siempre junto con un icono y un texto, nunca solo el color.
- Fondos de mensajes: usar el color de estado al 10 % de opacidad con borde del color de estado.
- Prohibido: degradados, sombras con brillo de color, vidrio esmerilado (glassmorphism), colores neón y cualquier color fuera de esta tabla.

**Archivo `styles/variables.css` completo (copiar tal cual y usar con `var(--nombre)`):**
```css
:root {
  /* Colores */
  --color-primario: #1F4E4A;
  --color-primario-hover: #173B38;
  --color-primario-suave: #E3EDEB;
  --color-acento: #A9532E;
  --color-acento-hover: #8E4324;
  --color-fondo: #F6F3EE;
  --color-superficie: #FFFFFF;
  --color-borde: #E1DBD0;
  --color-texto: #1E2926;
  --color-texto-suave: #5E6A66;
  --color-texto-sobre-color: #FFFFFF;
  --color-exito: #2F7D4F;
  --color-error: #B3392F;
  --color-advertencia: #B7791F;
  --color-info: #2C6A8A;

  /* Tipografía */
  --fuente-titulos: 'Source Serif 4', Georgia, 'Times New Roman', serif;
  --fuente-texto: 'Source Sans 3', 'Segoe UI', Arial, sans-serif;
  --texto-xs: 0.8125rem;
  --texto-sm: 0.875rem;
  --texto-base: 1rem;
  --texto-lg: 1.25rem;
  --texto-xl: 1.5rem;
  --texto-2xl: clamp(2rem, 1.5rem + 2vw, 2.25rem);
  --peso-normal: 400;
  --peso-medio: 500;
  --peso-semibold: 600;
  --peso-negrita: 700;
  --interlineado-texto: 1.5;
  --interlineado-titulo: 1.25;

  /* Espaciados */
  --espacio-xs: 0.25rem;
  --espacio-sm: 0.5rem;
  --espacio-md: 1rem;
  --espacio-lg: 1.5rem;
  --espacio-xl: 2rem;
  --espacio-2xl: 3rem;

  /* Bordes y border radius */
  --borde-ancho: 1px;
  --borde-estandar: var(--borde-ancho) solid var(--color-borde);
  --radio-sm: 0.375rem;
  --radio-md: 0.5rem;
  --radio-pildora: 62.5rem;

  /* Sombras */
  --sombra-tarjeta: 0 1px 2px rgb(30 41 38 / 0.06);
  --sombra-foco: 0 0 0 0.125rem var(--color-info);

  /* Tamaños relevantes */
  --ancho-contenedor: 67.5rem;
  --ancho-formulario: 40rem;
  --alto-control: 2.75rem;
  --tamano-icono: 1.25rem;
  --tamano-icono-lg: 1.5rem;

  /* Transiciones */
  --transicion-rapida: 150ms ease;
}
```

### 13.3 Tipografía y estilo general

- **Títulos:** Source Serif 4 (pesos 600 y 700). **Texto e interfaz:** Source Sans 3 (pesos 400, 500 y 600). Se instalan con `@fontsource` (sin depender de internet en ejecución) con tipografías de respaldo del sistema.
- Tamaños: texto base 16 px; h1 32–36 px; h2 24 px; h3 18–20 px; etiquetas 14 px. Interlineado 1.5 en texto.
- Contenedor principal con ancho máximo de 1080 px, centrado, con márgenes laterales de 16 px en móvil.
- Bordes redondeados moderados (6 px campos, 8 px tarjetas); sombra mínima; los bordes (1 px, color arena) separan más que las sombras.
- Botón primario: fondo verde petróleo, texto blanco, altura mínima 44 px. Botón secundario: borde verde petróleo, fondo transparente, texto verde petróleo. Botón de acento: fondo terracota, texto blanco.
- Inputs: fondo blanco, borde arena, altura mínima 44 px, borde primario al enfocar con anillo visible (2 px, `--color-info`). En error: borde `--color-error` y mensaje debajo con icono.
- Sin animaciones llamativas: solo transiciones de 150 ms en color y borde, y un spinner simple para la carga.
- **Evitar el aspecto genérico:** sin héroes gigantes con degradado, sin tarjetas con efecto "flotante", sin textos grandilocuentes, sin decoraciones innecesarias; priorizar alineación, espacios consistentes (escala `--espacio-xs` a `--espacio-2xl`) y jerarquía tipográfica.

### 13.4 Iconos (sin emojis)

**Regla estricta:** el aplicativo no contiene emojis en ningún lugar (interfaz, mensajes, placeholders, atributos `alt`, favicon, datos de ejemplo). Se usan únicamente iconos SVG de la librería `lucide-react`.

```bash
npm install lucide-react
```

- Tamaño por defecto: 20 px (24 px en el logo y en iconos de pasos); `strokeWidth={1.75}`; color heredado (`currentColor`).
- Los iconos decorativos llevan `aria-hidden="true"`; los iconos que son el único contenido de un botón llevan `aria-label`.
- Un icono siempre acompaña a un texto salvo en botones compactos con `aria-label`.

| Elemento | Icono (`lucide-react`) |
|----------|------------------------|
| Logo | `PawPrint` |
| Navegación: Inicio / Registro / Lista | `House` / `FilePlus2` / `ClipboardList` |
| Paso 1, 2, 3 en Inicio | `FilePenLine`, `Save`, `Search` |
| Campo nombre del solicitante | `User` |
| Campo correo electrónico | `Mail` |
| Campo mascota | `PawPrint` |
| Campo fecha | `CalendarDays` |
| Campo motivo | `MessageSquareText` |
| Especie: Perro / Gato / Conejo / Ave / Otro | `Dog` / `Cat` / `Rabbit` / `Bird` / `PawPrint` |
| Mensaje de éxito | `CircleCheck` |
| Mensaje de error | `CircleAlert` |
| Mensaje informativo | `Info` |
| Cargando | `LoaderCircle` (con giro CSS) |
| Lista vacía | `Inbox` |
| Reintentar | `RefreshCw` |
| Enviar solicitud | `Send` |
| Limpiar formulario | `Eraser` |
| Volver / ir a la lista | `ArrowLeft` / `ArrowRight` |

> Si algún nombre de icono no existe en la versión instalada de `lucide-react`, usar el equivalente más cercano de la misma librería; no sustituir por emojis.

- **Favicon:** `public/favicon.svg` con el trazo del icono `PawPrint` en `#1F4E4A` sobre fondo transparente.

### 13.5 HTML5 semántico

La estructura de cada pantalla se construye con elementos HTML5 que describan su significado, no con `div` genéricos. En React (JSX) se usan las mismas etiquetas.

- `index.html`: `<html lang="es">`, `<meta charset="UTF-8">`, `<meta name="viewport" content="width=device-width, initial-scale=1">` y `<title>`.
- Estructura de página: un solo `<header>` (con `<nav>`), un solo `<main>` y un `<footer>`.
- Cada pantalla tiene **un único `<h1>`**; los demás títulos siguen la jerarquía (`h2`, `h3`) sin saltarse niveles.
- Contenido temático agrupado en `<section>` con su título (por ejemplo "Cómo funciona").

| Parte del aplicativo | Elementos semánticos a usar |
|----------------------|-----------------------------|
| Menú de navegación | `<nav aria-label="Principal">` con `<ul>` / `<li>` y enlaces `<a>` (`NavLink`) |
| Pasos de "Cómo funciona" | `<section>` con `<ol>` / `<li>`, cada paso con su `<h3>` |
| Formulario | `<form>`, `<label for>`, `<input>`, `<select>`, `<textarea>`, `<button type="submit">`; agrupar con `<fieldset>` y `<legend>` si se divide en bloques |
| Mensajes de validación y estado | `<p>` o `<div role="alert">` / `role="status"` ligados al campo con `aria-describedby` |
| Lista de solicitudes | `<section>` con contenedor `<ul>` y cada tarjeta como `<li><article>` |
| Tarjeta de solicitud | `<article>` con `<header>` (mascota y especie), datos en `<dl>` / `<dt>` / `<dd>` y fechas en `<time datetime="...">` |
| Pie de página | `<footer>` |

- Usar `<button>` para acciones y `<a>` para navegación; **nunca** `div` o `span` con `onClick`.
- Las imágenes o SVG decorativos llevan `aria-hidden="true"`; los informativos llevan texto alternativo.

### 13.6 CSS propio y arquitectura de estilos

El diseño se desarrolla con **CSS propio**. Se evalúan la organización y la mantenibilidad de los estilos, no solo el aspecto.

- **Prohibido** usar frameworks o librerías de estilos (Bootstrap, Tailwind, Material UI, Chakra, etc.). Solo se permiten `lucide-react` (iconos) y `@fontsource` (tipografías).
- **Organización en archivos** dentro de `src/styles/`, importados en `main.jsx` en este orden:
  1. `variables.css`: únicamente Custom Properties.
  2. `base.css`: reset mínimo (`box-sizing: border-box`, márgenes), estilos de `body`, `h1`–`h3`, `a`, `button`, formularios y foco visible.
  3. `layout.css`: contenedor, navbar, footer, cuadrículas y estructura de página.
  4. `componentes.css`: botones, campos de formulario, tarjetas, mensajes, badges, spinner.
  5. `paginas.css`: ajustes que solo aplican a una página.
- **Nomenclatura** de clases en español, `kebab-case` y estilo BEM simplificado: bloque `.tarjeta`, elemento `.tarjeta__titulo`, modificador `.tarjeta--destacada`. Un componente React usa las clases de su propio bloque.
- Selectores simples (clases), sin `id` ni selectores anidados profundos; **sin `!important`**.
- Sin estilos en línea (`style={{}}`), salvo valores realmente dinámicos.
- Estilos **mobile-first**: la regla base es para móvil y las media queries amplían para pantallas mayores.
- Cada archivo organizado con comentarios de sección (`/* ===== Botones ===== */`) y propiedades en un orden consistente (posición, caja, tipografía, color, otros).
- No repetir código: si un patrón aparece dos o más veces (botón, campo, tarjeta), se convierte en una clase reutilizable.

### 13.7 CSS Custom Properties

Las principales decisiones visuales se definen una sola vez en `:root` (archivo `variables.css`, sección 13.2) y se **usan en todos los componentes** mediante `var(--nombre)`.

| Categoría | Variables |
|-----------|-----------|
| Colores | `--color-primario`, `--color-acento`, `--color-fondo`, `--color-superficie`, `--color-texto`, `--color-texto-suave`, `--color-borde`, estados (`--color-exito`, `--color-error`, ...) |
| Tipografía | `--fuente-titulos`, `--fuente-texto`, `--texto-xs` a `--texto-2xl`, `--peso-*`, `--interlineado-*` |
| Espaciados | `--espacio-xs` a `--espacio-2xl` |
| Bordes | `--borde-ancho`, `--borde-estandar` |
| Border radius | `--radio-sm`, `--radio-md`, `--radio-pildora` |
| Sombras | `--sombra-tarjeta`, `--sombra-foco` |
| Tamaños relevantes | `--ancho-contenedor`, `--ancho-formulario`, `--alto-control`, `--tamano-icono`, `--tamano-icono-lg` |
| Transiciones | `--transicion-rapida` |

**Reglas:**
- En `base.css`, `layout.css`, `componentes.css` y `paginas.css` **no se escriben valores literales** de color (`#...`), tamaño de letra, espaciado, radio ni sombra: siempre se usa una variable. Si falta una, se agrega primero a `variables.css`.
- Ejemplo de uso:
```css
.tarjeta {
  background: var(--color-superficie);
  border: var(--borde-estandar);
  border-radius: var(--radio-md);
  box-shadow: var(--sombra-tarjeta);
  padding: var(--espacio-lg);
}
```
- Las Custom Properties no funcionan dentro de la condición de `@media`; los puntos de quiebre se escriben en `rem` (sección 13.8).
- Cambiar la apariencia (por ejemplo, el color primario) debe ser posible modificando solo `variables.css`.

### 13.8 Diseño responsive

El aplicativo debe adaptarse correctamente como mínimo a **computadoras, tablets y teléfonos móviles**, sin desbordamiento horizontal y sin perder funcionalidad en el móvil.

**Puntos de quiebre (mobile-first, en `rem`):**
| Dispositivo | Rango | Regla CSS |
|-------------|-------|-----------|
| Teléfono móvil | desde 20rem (320 px) | estilos base, sin media query |
| Tablet | desde 40rem (640 px) | `@media (min-width: 40rem)` |
| Computadora | desde 60rem (960 px) | `@media (min-width: 60rem)` |

**Técnicas modernas a utilizar:**
- **Flexbox:** navbar, filas de icono + texto, grupos de botones, pie de tarjeta.
- **CSS Grid:** cuadrícula de tarjetas de la lista, pasos de "Cómo funciona" y columnas del formulario (por ejemplo `grid-template-columns: repeat(auto-fit, minmax(16rem, 1fr))`).
- **Media Queries** para ampliar el diseño en tablet y computadora.
- **Unidades relativas:** `rem` para tamaños y espaciados, `%` y `fr` para anchos, `ch` para limitar la longitud de línea de texto, `vw` y `clamp()` para títulos fluidos (`--texto-2xl`).
- Anchos fluidos con `max-width` (por ejemplo `max-width: var(--ancho-contenedor); width: 100%;`), sin anchos fijos en píxeles para contenedores.
- `img` y `svg` con `max-width: 100%`; hijos de grid/flex con `min-width: 0` si contienen texto largo; `overflow-wrap: anywhere` en correos y textos largos de las tarjetas.
- Áreas táctiles de al menos `--alto-control` (2.75rem) en botones y campos.

**Comportamiento esperado por dispositivo:**
| Elemento | Móvil | Tablet | Computadora |
|----------|-------|--------|-------------|
| Navbar | Logo arriba y enlaces en una fila debajo | Logo y enlaces en la misma fila | Igual que tablet con más espacio |
| Pasos de Inicio | 1 columna | 3 columnas | 3 columnas |
| Formulario | 1 columna, campos a todo el ancho | Nombre/correo y mascota/especie en 2 columnas | Igual que tablet, ancho máximo `--ancho-formulario` |
| Lista de solicitudes | 1 columna | 2 columnas | 3 columnas |
| Contenedor | Márgenes laterales `--espacio-md` | Márgenes laterales `--espacio-lg` | Centrado, máximo `--ancho-contenedor` |

**Verificación:** probar en el navegador (herramientas de desarrollo) con anchos de 320, 375, 768, 1024 y 1440 px. En ningún caso debe aparecer barra de desplazamiento horizontal (comprobable con `document.documentElement.scrollWidth <= document.documentElement.clientWidth`), y registrar y consultar solicitudes debe funcionar igual en el móvil.

---

## 14. ESPECIFICACIÓN DE LAS PANTALLAS

### 14.1 Estructura común (layout)
- **Cabecera (Navbar):** fondo verde petróleo, texto blanco. Izquierda: icono `PawPrint` + "Segundo Hogar". Derecha: enlaces **Inicio**, **Registrar**, **Solicitudes** (cada uno con icono). Enlace activo con subrayado terracota de 2 px. En móvil los enlaces ocupan una fila debajo del logo (sin menú hamburguesa) y mantienen área táctil de 44 px.
- **Contenido (main):** contenedor de máximo 1080 px sobre fondo hueso.
- **Pie (footer):** texto secundario centrado: "Segundo Hogar — Proyecto académico de Desarrollo Web".
- Título del documento por ruta: `Inicio | Segundo Hogar`, `Registrar solicitud | Segundo Hogar`, `Solicitudes | Segundo Hogar`.

### 14.2 Ruta `/` — Inicio
1. **Presentación:** título h1, párrafo descriptivo y dos botones: "Registrar solicitud" (primario, icono `FilePlus2`) y "Ver solicitudes" (secundario, icono `ClipboardList`).
2. **Sección "Cómo funciona":** tres tarjetas en fila (columna en móvil), cada una con icono en círculo verde niebla, número en terracota, título y una frase (textos en la sección 15).
3. Sin imágenes ni ilustraciones externas; el peso visual lo llevan la tipografía y los iconos.

### 14.3 Ruta `/registro` — Registrar solicitud
- Título "Registrar solicitud de adopción" y breve texto de ayuda.
- Formulario en una tarjeta blanca de ancho máximo 640 px. En pantallas ≥ 640 px, nombre y correo en dos columnas; mascota y especie en dos columnas; fecha y motivo a todo el ancho.
- Campos (todos obligatorios), en este orden:

| Campo | Control | Etiqueta | Icono | Detalles |
|-------|---------|----------|-------|----------|
| `nombre` | `input text` | Nombre completo del solicitante | `User` | 2–60 caracteres |
| `email` | `input email` | Correo electrónico | `Mail` | formato válido |
| `mascota` | `input text` | Nombre de la mascota | `PawPrint` | 2–60 caracteres |
| `especie` | `select` | Especie | `Dog` | opciones: Perro, Gato, Conejo, Ave, Otro |
| `fecha` | `input date` | Fecha propuesta de visita | `CalendarDays` | atributo `min` = hoy |
| `descripcion` | `textarea` (5 filas) | Motivo de la adopción | `MessageSquareText` | 10–500 caracteres, contador "0/500" |

- **Comportamiento:**
  - Validar al enviar y también al salir de cada campo (`onBlur`); el error de un campo desaparece al corregirlo.
  - Si hay errores, no se hace la petición y el foco va al primer campo con error.
  - Al enviar: botón deshabilitado con `LoaderCircle` y texto "Enviando...".
  - Éxito: limpiar el formulario y mostrar mensaje de éxito con enlace "Ver solicitudes".
  - Error del servidor (400/500) o de red: mostrar mensaje de error sobre el formulario sin borrar lo escrito.
- Botones: "Enviar solicitud" (primario, `Send`) y "Limpiar formulario" (secundario, `Eraser`).

### 14.4 Ruta `/lista` — Solicitudes
- Encabezado: título "Solicitudes registradas", contador ("3 solicitudes") y botón secundario "Actualizar" con `RefreshCw`.
- Carga automática al entrar (`useEffect` + GET). Orden: más recientes primero (`creadoEn` descendente).
- **Tarjeta de solicitud** (`TarjetaSolicitud`), con borde arena y fondo blanco:
  - Cabecera: icono de la especie en círculo verde niebla, nombre de la mascota (título h3) y etiqueta (badge) con la especie.
  - Cuerpo: filas con icono + dato: solicitante (`User`), correo (`Mail`), fecha de visita (`CalendarDays`, formato `dd/mm/aaaa` con `toLocaleDateString('es-EC')`).
  - Motivo (texto del campo `descripcion`, máximo 4 líneas con corte).
  - Pie: "Registrada el dd/mm/aaaa HH:mm" en texto secundario.
- Cuadrícula (CSS Grid): 1 columna en móvil, 2 columnas desde 40rem (tablet) y 3 columnas desde 60rem (escritorio).
- **Estados:** cargando (spinner + texto), vacío (icono `Inbox`, texto y botón para registrar), error (mensaje con `CircleAlert` y botón "Reintentar") y con datos.
- Opcional (si queda tiempo): filtro por especie con un `select` sobre la lista ya cargada.

### 14.5 Ruta `*` — Página no encontrada
Icono `CircleAlert`, título "Página no encontrada", texto breve y botón "Volver al inicio".

### 14.6 Accesibilidad y responsive
- Cada campo con `<label htmlFor>`; errores asociados con `aria-describedby`; `aria-invalid` en campos con error.
- Contenedores de mensajes con `role="status"` (éxito/info) o `role="alert"` (errores).
- Anillo de foco visible en todos los elementos interactivos; navegación completa con teclado.
- Diseño mobile-first, probado desde 320 px de ancho, sin desplazamiento horizontal.

---

## 15. TEXTOS DE LA INTERFAZ (en español, sin emojis)

**Inicio**
- h1: `Solicitudes de adopción de mascotas`
- Párrafo: `Registra tu interés por adoptar una mascota y consulta las solicitudes recibidas. Un proceso simple y ordenado para que cada animal encuentre un hogar responsable.`
- Título de sección: `Cómo funciona`
- Paso 1: `Completa el formulario` — `Indica tus datos, la mascota de interés y el motivo de la adopción.`
- Paso 2: `Guarda tu solicitud` — `La solicitud queda registrada en el sistema de forma permanente.`
- Paso 3: `Consulta el listado` — `Revisa en cualquier momento las solicitudes registradas.`

**Formulario (`/registro`)**
- Título: `Registrar solicitud de adopción`
- Ayuda: `Todos los campos son obligatorios.`
- Placeholders: nombre `Ej.: María López` · correo `nombre@correo.com` · mascota `Ej.: Max` · especie (opción inicial) `Selecciona una especie` · motivo `Cuéntanos por qué deseas adoptar y cómo es tu hogar`
- Éxito: `Solicitud registrada correctamente.`
- Error general del servidor: `No se pudo registrar la solicitud. Revisa los datos e inténtalo de nuevo.`
- Error de conexión: `No se pudo conectar con el servidor. Verifica que esté en ejecución e inténtalo de nuevo.`

**Mensajes de validación (cliente y servidor con el mismo sentido)**
| Campo | Mensaje |
|-------|---------|
| Cualquiera vacío | `Este campo es obligatorio.` |
| `nombre` | `Ingresa un nombre de entre 2 y 60 caracteres.` |
| `email` | `Ingresa un correo electrónico válido, por ejemplo nombre@correo.com.` |
| `mascota` | `Ingresa un nombre de mascota de entre 2 y 60 caracteres.` |
| `especie` | `Selecciona una especie de la lista.` |
| `descripcion` | `El motivo debe tener entre 10 y 500 caracteres.` |
| `fecha` (vacía o inválida) | `Selecciona una fecha válida.` |
| `fecha` (pasada) | `La fecha no puede ser anterior a hoy.` |

**Lista (`/lista`)**
- Título: `Solicitudes registradas` · contador: `N solicitudes` (singular: `1 solicitud`)
- Cargando: `Cargando solicitudes...`
- Vacía: `Aún no hay solicitudes registradas.` + botón `Registrar la primera solicitud`
- Error: `No se pudieron cargar las solicitudes. Verifica la conexión con el servidor.` + botón `Reintentar`
- Pie de tarjeta: `Registrada el dd/mm/aaaa HH:mm`

**Navegación y varios**
- Enlaces: `Inicio`, `Registrar`, `Solicitudes`
- 404: título `Página no encontrada`, texto `La dirección que buscas no existe.`, botón `Volver al inicio`
- Pie de página: `Segundo Hogar — Proyecto académico de Desarrollo Web`

**Respuestas de la API (mensajes de error en JSON, en español)**
- 400: `{ "error": "Datos inválidos", "detalles": ["..."] }` usando los mismos mensajes de validación de la tabla anterior.
- 500: `{ "error": "No se pudo guardar el registro" }` / `{ "error": "No se pudieron leer los registros" }`

---

## 16. INFORMACIÓN BASE PARA LA DOCUMENTACIÓN

Texto reutilizable para informes, README y explicaciones del proyecto.

**Descripción del aplicativo.** Segundo Hogar es una aplicación web de página única (SPA) que permite registrar y consultar solicitudes de adopción de mascotas. Una persona interesada completa un formulario con sus datos, la mascota de su interés, la especie, la fecha propuesta de visita y el motivo; la solicitud se guarda en un archivo JSON mediante una API REST y puede consultarse en un listado.

**Problema.** Quienes rescatan o cuidan mascotas necesitan recibir las solicitudes de adopción de forma ordenada, sin depender de mensajes dispersos o registros manuales.

**Objetivo general.** Desarrollar una aplicación web full stack (React + Express + JSON) para registrar y consultar solicitudes de adopción de mascotas.

**Objetivos específicos.**
1. Construir una interfaz en React con tres rutas, formulario validado y listado de solicitudes.
2. Implementar una API REST en Express con los endpoints `GET /registros` y `POST /registros` y validación de datos.
3. Almacenar los registros de forma persistente en un archivo JSON.
4. Integrar frontend y backend mediante peticiones HTTP y verificar su funcionamiento con pruebas.

**Alcance y límites.** Incluye crear y consultar solicitudes. No incluye autenticación, pagos, agenda automática, edición ni eliminación de registros, ni gestión administrativa del refugio.

**Flujo de datos (descripción).**
1. La persona completa el formulario en `/registro` y el estado de React controla los valores.
2. El cliente valida los campos; si son correctos, `api.js` envía un `POST /registros` con el cuerpo en JSON.
3. Express recibe la petición, valida de nuevo, genera el `id` y `creadoEn`, y el servicio de almacenamiento agrega el registro a `registros.json` sin perder los anteriores.
4. La API responde `201` con el registro creado (o `400`/`500` con el error).
5. React muestra la confirmación; al abrir `/lista`, `api.js` hace `GET /registros` y renderiza las tarjetas con los datos guardados.

**Decisiones técnicas y su justificación.**
| Decisión | Justificación |
|----------|---------------|
| React + Vite | Desarrollo rápido de una SPA con componentes reutilizables |
| `react-router-dom` | Navegación entre `/`, `/registro` y `/lista` sin recargar la página |
| Validación en cliente y servidor | El cliente mejora la experiencia; el servidor protege los datos aunque se omita la interfaz |
| Archivo JSON | Persistencia simple exigida por el proyecto, sin instalar un motor de base de datos |
| `crypto.randomUUID()` | Genera identificadores únicos sin dependencias externas |
| CORS | Permite que el frontend (puerto 5173) consulte la API (puerto 4000) |
| Variable `VITE_API_URL` | Evita escribir la dirección de la API en el código y facilita cambiarla |
| `lucide-react` | Iconos vectoriales consistentes, ligeros y sin uso de emojis |
| Variables CSS (Custom Properties) | Centralizan colores, tipografía, espaciados, bordes, radios, sombras y tamaños; facilitan mantener un diseño coherente |
| CSS propio organizado en archivos | Estilos mantenibles y sin depender de frameworks |
| HTML5 semántico | Estructura accesible, comprensible y mejor organizada |
| Diseño mobile-first con Flexbox, Grid y media queries | Adaptación a móvil, tablet y computadora sin desbordamientos |

**Trazabilidad de requisitos.**
| Requisito del proyecto | Dónde se cumple |
|------------------------|-----------------|
| 3 rutas con enrutador | `App.jsx`, secciones 4 y 14 |
| Componentes reutilizables | `Navbar`, `FormularioAdopcion`, `TarjetaSolicitud`, `Mensaje`, `Cargando` |
| Formulario con estado y validaciones | `FormularioAdopcion.jsx`, `validaciones.js`, sección 14.3 |
| POST y GET con estados de carga, vacío, éxito y error | `api.js`, `Registro.jsx`, `Lista.jsx` |
| API REST con códigos HTTP y validación | Backend, sección 7 |
| ID único y persistencia en JSON | `almacenamiento.service.js`, sección 7 |
| CORS y manejo de errores de archivo | `app.js`, `errorHandler.js` |
| HTML5 semántico | Sección 13.5 |
| CSS propio y Custom Properties | `styles/variables.css` y secciones 13.6 y 13.7 |
| Interfaz adaptable (computadora, tablet, móvil) | Secciones 13.8 y 14.6 |

---

## 17. PROMPTS SUGERIDOS PARA EL AGENTE DE IA

- *"Soy **Aaron**. Guíame paso a paso para construir el backend (fase 2) según las secciones 6.1 y 7, explicándome cada archivo."*
- *"Soy **Jostin**. Ayúdame a crear la estructura del frontend con Vite, rutas y estilos responsive (sección 6.2)."*
- *"Soy **Jefferson**. Ayúdame con el formulario controlado, las validaciones y `api.js` respetando el contrato de la API."*
- *"Soy **Mayerly**. Ayúdame con la página Lista, el plan de pruebas y el README."*
- *"Revisa mi código contra el checklist de la sección 12 y las secciones 13 a 15 (identidad visual, pantallas y textos) y dime qué falta."*
