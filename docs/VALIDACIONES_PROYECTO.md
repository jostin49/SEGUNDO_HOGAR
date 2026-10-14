# Informe de Validación y Cumplimiento de Requisitos
## Proyecto: Segundo Hogar — Adopción de Mascotas

> **Materia:** Desarrollo Web — Ingeniería de Software  
> **Tema:** Adopción de mascotas  
> **Nombre del aplicativo:** `Segundo Hogar`  
> **Fecha de verificación:** 13 de octubre de 2026  
> **Estado general:** **100% Cumplido**

---

## 1. Alcance del Proyecto

| Requisito | Estado | Evidencia y Dónde se Cumple |
| :--- | :---: | :--- |
| **Crear y consultar registros mediante API** | **CUMPLE** | Implementado con los endpoints `POST /registros` y `GET /registros`. |
| **Sin sistemas ajenos al alcance** | **CUMPLE** | No contiene pagos, login/autenticación, agenda automática ni bases de datos SQL/Mongo externas. |
| **Exclusividad de temática y proceso principal** | **CUMPLE** | **Segundo Hogar:** Permite que un solicitante registre su interés por adoptar una mascota específica y consulte el listado general de solicitudes. |

---

## 2. Flujo Extremo a Extremo

```
[ Formulario React ]
         │
         ▼ (fetch POST JSON)
[ API REST Express (Puerto 4000) ]
         │
         ▼ (fs/promises)
[ Archivo data/registros.json ]
         │
         ▼ (fetch GET JSON)
[ Vista de Solicitudes React (Puerto 5173) ]
```

* **Flujo verificado:**  
  1. El solicitante llena el formulario en `/registro`.
  2. El cliente valida los datos y envía un `POST` en formato JSON mediante `frontend/src/services/api.js`.
  3. El servidor Express recibe la solicitud, la revalida con `backend/src/validators/registro.validator.js`, genera un `id` único (`crypto.randomUUID()`) y una fecha ISO (`creadoEn`).
  4. La solicitud se anexa de forma persistente a `backend/data/registros.json` mediante `backend/src/services/almacenamiento.service.js`.
  5. El servidor responde `201 Created`.
  6. El frontend muestra la confirmación y permite navegar a `/lista`, donde se realiza un `GET /registros` para renderizar las tarjetas.

---

## 3. Frontend (React + Vite)

| Requisito | Estado | Detalle de Implementación |
| :--- | :---: | :--- |
| **SPA con mínimo 3 rutas** | **CUMPLE** | Configurado en `frontend/src/App.jsx` con `react-router-dom`: <br>• `/`: Bienvenida y pasos de "Cómo funciona" (`Inicio.jsx`)<br>• `/registro`: Formulario de solicitud (`Registro.jsx`)<br>• `/lista`: Listado de solicitudes recibidas (`Lista.jsx`)<br>• `*`: Ruta 404 personalizada (`NoEncontrada.jsx`) |
| **Componentes reutilizables** | **CUMPLE** | Archivos modulares con responsabilidad única: <br>• `Navbar.jsx`: Menú de navegación con enlaces activos (`NavLink`)<br>• `FormularioAdopcion.jsx`: Formulario controlado y accesible<br>• `TarjetaSolicitud.jsx`: Tarjeta con `<article>`, `<dl>`, `<time>` e icono dinámico por especie<br>• `Mensaje.jsx`: Componente de estado (éxito, error, info)<br>• `Cargando.jsx`: Spinner animado con `LoaderCircle` |
| **Formulario controlado por estado** | **CUMPLE** | Implementado con `useState` en `FormularioAdopcion.jsx`. Controla los campos obligatorios: `nombre`, `email`, `mascota`, `especie`, `fecha` y `descripcion` (contador de caracteres en vivo 0/500). |
| **Validaciones en cliente** | **CUMPLE** | En `frontend/src/utils/validaciones.js`. Valida en `onBlur` y `onSubmit`. Bloquea el envío si hay datos inválidos y enfoca el primer campo con error. |
| **Peticiones HTTP y estados de interfaz** | **CUMPLE** | En `frontend/src/services/api.js`: <br>• **Cargando:** Muestra spinner durante la consulta GET.<br>• **Lista vacía:** Muestra icono `Inbox` y botón para crear la primera solicitud.<br>• **Confirmación:** Mensaje de éxito tras `POST` exitoso con enlace directo a `/lista`.<br>• **Error:** Mensaje de error amigable con botón de "Reintentar" si el servidor no responde. |
| **Diseño adaptable (Responsive)** | **CUMPLE** | CSS propio (Mobile-First) con Flexbox y CSS Grid en `frontend/src/styles/`. Comprobado en anchos de 375 px (móvil) y 1280 px (escritorio), sin desbordamiento horizontal (`scrollWidth <= clientWidth`). |

---

## 4. Backend (Node.js + Express)

| Requisito | Estado | Detalle de Implementación |
| :--- | :---: | :--- |
| **Endpoints REST** | **CUMPLE** | `GET /registros` (200 OK) y `POST /registros` (201 Created, 400 Bad Request) configurados en `backend/src/routes/registros.routes.js`. |
| **Manejo de JSON** | **CUMPLE** | Middleware `express.json()` en `backend/src/app.js` para parsear peticiones y responder siempre en JSON. |
| **Validación en servidor** | **CUMPLE** | En `backend/src/validators/registro.validator.js`: Valida tipo, longitud, formato de email, lista de especies y fecha no anterior a hoy. Devuelve código `400` con array de `detalles`. |
| **ID único y fechas seguras** | **CUMPLE** | El servidor genera `id` mediante `crypto.randomUUID()` y `creadoEn` en formato ISO 8601; nunca confía en IDs enviados por el cliente. |
| **Manejo de errores sin tumbar Node** | **CUMPLE** | Middlewares de error en `backend/src/middlewares/errorHandler.js` y `try/catch` en controladores/servicios que devuelven `500 Internal Server Error` controlado sin detener el proceso. |
| **CORS habilitado** | **CUMPLE** | Configurado en `app.js` mediante la librería `cors` permitiendo el origen `http://localhost:5173`. |
| **Organización del código** | **CUMPLE** | Arquitectura limpia dividida en capas: `server.js`, `app.js`, `routes/`, `controllers/`, `services/`, `validators/` y `middlewares/`. |

---

## 5. Almacenamiento y Persistencia

| Requisito | Estado | Detalle de Implementación |
| :--- | :---: | :--- |
| **Almacenamiento en archivo JSON** | **CUMPLE** | Implementado en `backend/src/services/almacenamiento.service.js` con la API nativa de Node `fs/promises`. Guarda en `backend/data/registros.json`. |
| **No sobreescribir registros anteriores** | **CUMPLE** | Lee el arreglo existente, agrega el nuevo registro al final del array (`push`) y vuelve a guardar con formato legible (`JSON.stringify(registros, null, 2)`). |
| **Auto-inicialización en `[]`** | **CUMPLE** | La función `asegurarArchivo()` comprueba si la carpeta o el archivo existen; si no existen, los crea automáticamente con contenido `[]`. |
| **Persistencia tras reinicio** | **CUMPLE** | Verificado: Se detuvo y reinició el servidor de Express y los datos almacenados en `registros.json` persistieron y fueron consultados sin alteración. |

---

## 6. Reglas de Validación (Cliente y Servidor)

Tanto el formulario de React como el validador de Express aplican exactamente las mismas reglas y devuelven los mismos mensajes en español:

| Campo | Tipo | Obligatorio | Regla de Validación | Mensaje al Usuario |
| :--- | :---: | :---: | :--- | :--- |
| **Cualquiera** | — | Sí | No puede enviarse vacío o con solo espacios. | `"Este campo es obligatorio."` |
| `nombre` | Texto | Sí | Longitud entre 2 y 60 caracteres. | `"Ingresa un nombre de entre 2 y 60 caracteres."` |
| `email` | Email | Sí | Formato RFC estándar (`/^[^\s@]+@[^\s@]+\.[^\s@]+$/`). | `"Ingresa un correo electrónico válido, por ejemplo nombre@correo.com."` |
| `mascota` | Texto | Sí | Longitud entre 2 y 60 caracteres. | `"Ingresa un nombre de mascota de entre 2 y 60 caracteres."` |
| `especie` | Select | Sí | Debe ser uno de: `Perro`, `Gato`, `Conejo`, `Ave`, `Otro`. | `"Selecciona una especie de la lista."` |
| `fecha` | Fecha | Sí | Formato `YYYY-MM-DD`, fecha válida en calendario y **no anterior a hoy**. | `"Selecciona una fecha válida."` o `"La fecha no puede ser anterior a hoy."` |
| `descripcion` | Texto | Sí | Longitud entre 10 y 500 caracteres. | `"El motivo debe tener entre 10 y 500 caracteres."` |

---

## 7. Verificación del Repositorio Git (Listo para Subir)

| Elemento Requerido | Estado | Archivo / Directorio en el Proyecto |
| :--- | :---: | :--- |
| **Carpetas `frontend/` y `backend/` separadas** | **LISTO** | Presentes en la raíz del proyecto. |
| **Archivos de dependencias (`package.json`)** | **LISTO** | `backend/package.json` y `frontend/package.json`. |
| **Archivos de bloqueo (`package-lock.json`)** | **LISTO** | `backend/package-lock.json` y `frontend/package-lock.json`. |
| **Archivo `.gitignore` en la raíz** | **LISTO** | Configurado para ignorar `node_modules/`, `.env`, `dist/` y `*.log`. |
| **Archivos de entorno de ejemplo** | **LISTO** | `backend/.env.example` y `frontend/.env.example`. |
| **Sin credenciales ni `node_modules`** | **LISTO** | Verificado con `git status --ignored`. |
| **Documentación `README.md`** | **LISTO** | Contiene descripción, integrantes, requisitos, instalación paso a paso, puertos (4000 y 5173) y ejemplos con `curl`. |
| **Matriz de pruebas** | **LISTO** | `docs/PLAN_PRUEBAS.md` con 13 pruebas técnicas documentadas. |
| **Recursos visuales e imagen de inicio** | **LISTO** | `frontend/src/assets/mascotas.png` (formato PNG transparente optimizado) y `frontend/public/favicon.svg`. |

---

### Resumen Final
El proyecto cumple de forma íntegra con todos los lineamientos pedagógicos y técnicos exigidos por la cátedra.
