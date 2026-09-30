# API de reservas de citas

API REST construida con Node.js, Express y Prisma para registrar usuarios, iniciar sesión y gestionar citas y bloques horarios en PostgreSQL. La API permite consultar, crear, actualizar y eliminar reservas; los administradores pueden crear bloques horarios y consultar las reservas.

## Requisitos

- Node.js y npm
- PostgreSQL
- Una base de datos PostgreSQL accesible desde esta computadora

## Instalación

```bash
git clone <URL_DEL_REPOSITORIO>
cd api-reservas-citas
npm install
```

Copia `.env.example` a `.env` y configura las variables.

```env
PORT=3000
NODE_ENV=development
DATABASE_URL="postgresql://USUARIO:CONTRASENA@localhost:5432/NOMBRE_BASE?schema=public"
JWT_SECRET="pon-aqui-una-clave-larga-y-aleatoria"
```

Prepara la base de datos y genera el cliente de Prisma:

```bash
npx prisma migrate deploy
npx prisma generate
```

Para cargar los datos de demostraciónn (opcional):

```bash
node prisma/seed.js
```

El seed crea usuarios de ejemplo con contraseña `Password123!` y **borra las citas y bloques horarios existentes** antes de insertar los datos. Úsalo solo en una base de datos de prueba.

## Ejecutar

```bash
npm run dev
```

El servidor escucha en `http://localhost:3000` (o el puerto indicado en `PORT`). Para iniciar sin modo de desarrollo:

```bash
npm start
```

## Rutas disponibles

Todas las rutas usan el prefijo `/api`.

| Método | Ruta | Descripción | Acceso |
|---|---|---|---|
| POST | `/api/auth/register` | Registrar usuario | PÃºblico |
| POST | `/api/auth/login` | Iniciar sesión y recibir token JWT | Público |
| GET | `/api/auth/protected-route` | Ejemplo de ruta protegida | Token JWT |
| POST | `/api/reservations` | Crear reserva | Token JWT |
| GET | `/api/reservations/:id` | Consultar reserva | Token JWT |
| PUT | `/api/reservations/:id` | Actualizar reserva | Token JWT |
| DELETE | `/api/reservations/:id` | Eliminar reserva | Token JWT |
| POST | `/api/admin/time-blocks` | Crear bloque horario | Token JWT de ADMIN |
| GET | `/api/admin/reservations` | Listar reservas | Token JWT de ADMIN |
| GET | `/api/users/:id/appoinments` | Consultar citas de un usuario | Actualmente no requiere token |

Para las rutas protegidas, enví­a el token devuelto por `/api/auth/login` en el encabezado:

```http
Authorization: Bearer <TOKEN>
```

Ejemplo de registro:

```json
{
  "name": "Ana Ejemplo",
  "email": "ana@example.com",
  "password": "una-clave-segura"
}
```

Ejemplo de inicio de sesión:

```json
{
  "email": "ana@example.com",
  "password": "una-clave-segura"
}
```

La reserva usa `date`, `userId` y `timeBlockId`; el bloque horario usa `startTime` y `endTime`, con fechas en formato ISO 8601. Consulta `prisma/schema.prisma` para ver los modelos.

## Estructura

- `src/routes`: definición y montaje de rutas (`src/server.js` inicia esta aplicación).
- `src/controllers`: manejo de solicitudes y respuestas.
- `src/services`: lógica de negocio y consultas con Prisma.
- `src/middlewares`: autenticación JWT, registro de solicitudes y manejo de errores.
- `prisma/schema.prisma`: modelos de la base de datos.
- `prisma/migrations`: migraciones de Prisma.
- `prisma/seed.js`: datos de demostración.

El `app.js` ubicado en la raíz contiene ejemplos anteriores del curso; el servidor usado por los comandos `npm start` y `npm run dev` está en `src/server.js`.

## Antes de usar en producción

Revisa el control de acceso a las rutas de reservas: actualmente se requiere un token, pero conviene verificar que cada usuario solo pueda ver, modificar o cancelar sus propias citas. También protege con autenticación el historial de usuario. El repositorio no incluye una suite de pruebas todaví­a.


## Tecnologías

- **Node.js**: entorno de ejecución.
- **Express 5**: servidor y rutas de la API REST.
- **PostgreSQL**: base de datos relacional.
- **Prisma ORM**: modelos, migraciones y acceso a la base de datos.
- **JWT (`jsonwebtoken`)**: autenticación mediante tokens.
- **bcryptjs**: hash de contraseñas.
- **CORS** y **dotenv**: configuración de acceso y variables de entorno.
