# Stack Tecnológico

El stack tecnológico de **Convive+** ha sido seleccionado para garantizar un desarrollo rápido, mantenible y escalable, priorizando la relación costo-beneficio y la correcta integración de la inteligencia artificial requerida para el "Motor de Reparto" en su etapa de MVP.

---

## 1. Frontend (Interfaz de Usuario)
**Tecnologías:** ReactJS, Vite, Tailwind CSS, Zustand, TanStack Query

- **ReactJS + Vite:** Se utiliza **React** para construir una interfaz modular y dinámica. **Vite** permite un entorno de desarrollo ultra rápido y liviano, reduciendo drásticamente los tiempos de compilación.
- **Tailwind CSS:** Facilita la construcción de interfaces mediante clases utilitarias, estandarizando el diseño visual sin depender de pesados archivos CSS externos y agilizando el prototipado rápido.
- **Gestión de Estado (Zustand & TanStack Query):** Dado que la app maneja cálculos financieros de usuarios en tiempo real, **Zustand** administrará el estado global ligero de la app. Por otro lado, **TanStack Query** (React Query) se usará para consumir, cachear asíncronamente y sincronizar los datos desde el backend, asegurando que los saldos y las tareas mostradas no queden desactualizadas en la pantalla del usuario.

---

## 2. Backend (Lógica y API)
**Tecnologías:** Python, FastAPI, SQLAlchemy, Alembic

- **Python + FastAPI:** Se elige **Python** por su simplicidad y potencia en cálculo lógico y manejo de datos. **FastAPI** será el núcleo transaccional, exponiendo rutas asíncronas, eficientes y auto-documentadas (Swagger/OpenAPI nativo) para gestionar operaciones a gran velocidad.
- **SQLAlchemy + Alembic (ORM y Migraciones):** Se implementa **SQLAlchemy** como ORM intermedio para comunicar el ecosistema de Python con la base de datos de manera segura y orientada a objetos (previniendo inyección SQL directa). Las evoluciones de las tablas de la base de datos se manejan mediante esquemas y migraciones controladas en código con **Alembic**.

---

## 3. Inteligencia Artificial (Motor de Reparto)
**Tecnologías:** LLM de Razonamiento

- **Integración IA Cloud:** El aspecto más clave de la app, el "Motor de Reparto Asimétrico y Rotativo", interactúa mediante llamadas por API (REST) a un modelo de lenguaje extenso (LLM) de razonamiento de alto nivel. O en su debido caso se utilizara el chat integrado de Gemini o Copilot dentro de VS Code o Antigravity, dependiendo de cuál ofrezca mejor rendimiento y costo en el momento de la implementación.
- **Decisiones Complejas:** Su función es procesar y cruzar las variables multidimensionales que ingresa el usuario: ingresos, porcentajes de responsabilidad, ciclos rotativos de limpieza y compensaciones por dietas. El modelo Cloud evita los inmensos costos de servidores propios de IA garantizando la seguridad en el cálculo gracias al estricto contexto (prompt injection).

---
## 4. Mailpit para emails dentro del entorno de desarrollo
**Tecnología:** Mailpit (Servidor SMTP de pruebas)
- **Mailpit:** Para el desarrollo local, se utiliza Mailpit como servidor SMTP de pruebas. Esto permite simular el envío de correos electrónicos (como notificaciones de tareas o cambios en el estado de créditos) sin necesidad de configurar un servicio de correo real, facilitando la depuración y el desarrollo de funcionalidades relacionadas con la comunicación por email.

## 5. Base de Datos
**Tecnología:** PostgreSQL (Relacional)

- **PostgreSQL:** Actúa como la fuente única de la verdad. Su extrema robustez garantiza la **integridad de datos** mediante transacciones seguras (propiedades ACID), previniendo inconsistencias graves al cruzar múltiples tablas dependientes entre sí como Usuarios, Tareas, Gastos y Créditos Financieros.
- **Transacciones Atómicas:** Para operaciones críticas como el cálculo del reparto o la actualización de saldos, se implementarán transacciones atómicas que aseguren que todas las partes del proceso se completen correctamente o se reviertan en caso de error, manteniendo la base de datos siempre en un estado consistente.
---

## 6. Autenticación y Seguridad
**Tecnología:** JWT (JSON Web Tokens) + Hashing (Passlib/Bcrypt)

- **JWT:** Se adopta para mantener sesiones estandarizadas, seguras y *stateless* (sin guardar el estado en el servidor) entre los clientes web/móvil y la API de FastAPI. Es la solución ideal para un producto inicial, ya que valida identidades con firmas encriptadas sin sobrecargar consultas a la base de datos en cada click. Las contraseñas de las cuentas siempre deben almacenarse hasheadas y saladas.
- **CORS y Seguridad API:** Se configurarán políticas de CORS estrictas para limitar el acceso a la API solo desde los dominios autorizados, y se implementarán medidas de seguridad adicionales como rate limiting para proteger contra ataques de fuerza bruta.
- **Rate Limiting:** Para proteger la API de posibles abusos, se implementará un sistema de limitación de solicitudes (rate limiting) que restringirá el número de peticiones que un usuario o IP puede realizar en un período determinado.
- **Middleware de Seguridad:** Se integrará un middleware que valide los tokens JWT en cada solicitud a rutas protegidas, asegurando que solo usuarios autenticados puedan acceder a funcionalidades críticas como la gestión de gastos y tareas.
- **Encriptación de Datos Sensibles:** Además de hashear contraseñas, se evaluará la necesidad de encriptar otros datos sensibles almacenados en la base de datos, como información financiera o detalles personales, utilizando bibliotecas de encriptación robustas.
- **Verificación OTP:** Para acciones críticas como cambios de contraseñas,ajustes financieros y registro de un usuario en el sistema, se implementará un sistema de verificación de un solo uso (OTP) enviado por correo electrónico para añadir una capa extra de seguridad.
---

## 7. Testing (Pruebas Automatizadas)
**Tecnologías:** PyTest (Backend), Vitest / React Testing Library (Frontend)

- Garantizar que los cálculos del Motor de Gastos no tengan defectos es crítico para mitigar el "dolor" del usuario (peleas por dinero).
- **Backend:** Pruebas unitarias integrales con **PyTest** para certificar que las rutas en FastAPI y el reparto asimétrico de puntos respondan correctamente los montos y deudas.
- **Frontend:** **Vitest** confirmará que la renderización de los componentes no colapse al mostrar dichos balances en la pantalla.

---

## 8. Despliegue, Hosting e Integración (CI/CD)
**Infraestructura:** VPS, Contenedores Docker con Docker Compose, GitHub Actions

- **Migración a Contenedores (Docker):** Todos los sistemas (Frontend compilado, API Backend, y la BD PostgreSQL) se empaquetarán en contenedores estandarizados mediante `docker compose` desde el plugin instalado de docker compose una vez se instala docker, asegurando que la aplicación funcione en el servidor exactamente igual que en las máquinas de los desarrolladores. Los contenedores deben usar un SO interno basado en Ubuntu.

- **Hosting en VPS:** Servidor Virtual Privado Linux en Ubuntu debido a su accesibilidad financiera y completa libertad de control sobre los procesos de Background.

- **Flujos CI/CD Automáticos:** Haciendo uso de pipelines con **GitHub Actions**, al momento en el que el equipo une (*merge*) código verificado a la rama principal (Main), se ejecutan automáticamente las pruebas (*tests*) y, si el resultado es verde, la nueva versión de Convive+ se compila y se despliega sola en el VPS para que los usuarios puedan utilizarla.
- **Variables de entorno y secretos:** Se gestionarán de forma segura utilizando las herramientas de GitHub Actions para almacenar claves de API, credenciales de base de datos y otros secretos necesarios para el funcionamiento de la aplicación sin exponerlos en el código fuente. En local funciona a medida de archivos .env que no se suben al repositorio, pero se mantiene en el repositorio archivos como .env.example para que los desarrolladores sepan qué variables configurar en su entorno local.
.