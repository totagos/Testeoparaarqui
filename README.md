# Gestor de contraseñas

API REST sencilla en Node.js y Express para gestionar usuarios, autenticación y credenciales de distintos servicios, orientada a conceptos básicos de ciberseguridad (hash de contraseñas, JWT y cifrado simétrico).

---

## Descripción

Este proyecto implementa un gestor de contraseñas básico expuesto como API REST:

* Permite registrar usuarios y autenticarlos mediante JWT.
* Almacena credenciales (usuario + contraseña) de diferentes servicios asociadas a cada usuario.
* Las contraseñas de las credenciales se cifran con AES antes de guardarse en memoria.
* Incluye un endpoint para analizar la “fuerza” de una contraseña.
* Toda la información se guarda en memoria (arrays), por lo que se pierde al reiniciar el servidor (útil como TP / demo, no para producción).

---

## Funcionalidades

* Registro de usuarios con:

  * Validación de datos obligatorios.
  * Hash de contraseñas con `bcryptjs`. 
* Login de usuarios:

  * Validación de credenciales (email + contraseña).
  * Generación de token JWT con expiración de 1 día. 
* Logout “lógico” (respuesta simple del servidor). 
* Gestión de credenciales por usuario autenticado:

  * Crear credenciales cifrando la contraseña con AES (`crypto-js`).
  * Listar credenciales propias (sin mostrar la contraseña).
  * Ver una credencial específica (incluyendo la contraseña desencriptada).
  * Actualizar una credencial.
  * Eliminar una credencial. 
* Utilidad de seguridad:

  * Endpoint que evalúa la fuerza de una contraseña y devuelve un “score” y feedback. 
* Endpoint de estado / healthcheck para comprobar si la API está levantada. 

Dependencias principales: `express`, `bcryptjs`, `jsonwebtoken`, `crypto-js`, y `nodemon` para desarrollo.

---

## Endpoints de la API

La API expone los siguientes grupos de endpoints:

### 1. Estado de la API

**GET `/api/estado`**
Devuelve un JSON con el estado del servidor y un timestamp.

Ejemplo de respuesta:

```json
{
  "status": "andando",
  "message": "todo piola",
  "timestamp": "2025-01-01T12:00:00.000Z"
}
```



---

### 2. Usuarios (`/api/user`)

Router de usuarios, sin autenticación previa (se usa para registrar y consultar usuarios en memoria). 

#### POST `/api/user/`

Crea un nuevo usuario.

* Body (JSON):

```json
{
  "username": "tuUsuario",
  "email": "correo@example.com",
  "password": "miPasswordSegura123!"
}
```

* Validaciones:

  * `username`, `email` y `password` son obligatorios.
  * `email` no puede repetirse.

* Respuesta (201):

```json
{
  "message": "usuario creado",
  "user": {
    "id": 1,
    "username": "tuUsuario",
    "email": "correo@example.com"
  }
}
```

#### GET `/api/user/`

Lista todos los usuarios registrados, sin incluir la contraseña.

* Respuesta (200):

```json
[
  {
    "id": 1,
    "username": "tuUsuario",
    "email": "correo@example.com",
    "creado": "2025-01-01T12:00:00.000Z"
  }
]
```

#### GET `/api/user/:id`

Obtiene los datos de un usuario por ID.

* Parámetros:

  * `:id` → ID numérico del usuario.

* Respuesta (200):

```json
{
  "id": 1,
  "username": "tuUsuario",
  "email": "correo@example.com",
  "creado": "2025-01-01T12:00:00.000Z"
}
```

* Errores:

  * 404 si el usuario no existe.

---

### 3. Autenticación (`/api/auth`)

Router de autenticación basado en JWT, compartiendo el array de usuarios con el router de `/api/user`.

#### POST `/api/auth/login`

Realiza login de un usuario.

* Body (JSON):

```json
{
  "email": "correo@example.com",
  "password": "miPasswordSegura123!"
}
```

* Validaciones:

  * `email` y `password` obligatorios.
  * Verifica que exista un usuario con ese `email`.
  * Compara la contraseña en texto plano con el hash almacenado mediante `bcrypt.compare`.

* Respuesta (200):

```json
{
  "message": "login ok",
  "token": "jwt_generado",
  "user": {
    "id": 1,
    "username": "tuUsuario",
    "email": "correo@example.com"
  }
}
```

* Errores:

  * 400 si faltan datos.
  * 401 si las credenciales son incorrectas.
  * 500 si ocurre un error interno.

#### POST `/api/auth/logout`

Endpoint simple que devuelve un mensaje de cierre de sesión:

* Respuesta (200):

```json
{
  "message": "chau session"
}
```

(No invalida el token en servidor; es solo de ejemplo.)

---

### 4. Credenciales (`/api/credenciales`)

Router protegido: todos los endpoints requieren un token JWT válido en el header `Authorization: Bearer <token>`. 

#### Middleware de autenticación

* El middleware `necesitaAuth`:

  * Lee `Authorization` de los headers.
  * Espera formato `Bearer <token>`.
  * Verifica el token con la clave JWT configurada en el servidor.
  * Si es válido, asigna `req.user` con los datos del usuario (incluyendo `userId`).
  * Si no, responde con 401.

#### GET `/api/credenciales/`

Lista las credenciales del usuario autenticado (sin mostrar la contraseña).

* Headers:

  * `Authorization: Bearer <token>`

* Respuesta (200):

```json
{
  "cantidad": 2,
  "credenciales": [
    {
      "id": 1,
      "servicio": "gmail",
      "usuario": "miCorreo",
      "creado": "2025-01-01T12:00:00.000Z"
    },
    {
      "id": 2,
      "servicio": "github",
      "usuario": "miUserGitHub",
      "creado": "2025-01-01T13:00:00.000Z"
    }
  ]
}
```

#### POST `/api/credenciales/`

Crea una nueva credencial asociada al usuario autenticado, cifrando la contraseña con AES.

* Headers:

  * `Authorization: Bearer <token>`

* Body (JSON):

```json
{
  "servicio": "gmail",
  "usuario": "miCorreo",
  "password": "miPassSuperSecreta"
}
```

* Validaciones:

  * `servicio`, `usuario` y `password` obligatorios.

* Respuesta (201):

```json
{
  "message": "credencial guardada",
  "credencial": {
    "id": 1,
    "servicio": "gmail",
    "usuario": "miCorreo",
    "creado": "2025-01-01T12:00:00.000Z"
  }
}
```

#### GET `/api/credenciales/:id`

Devuelve una credencial concreta del usuario autenticado, incluyendo la contraseña desencriptada.

* Headers:

  * `Authorization: Bearer <token>`

* Parámetros:

  * `:id` → ID numérico de la credencial.

* Respuesta (200):

```json
{
  "id": 1,
  "servicio": "gmail",
  "usuario": "miCorreo",
  "password": "miPassSuperSecreta",
  "creado": "2025-01-01T12:00:00.000Z"
}
```

* Errores:

  * 404 si la credencial no existe o no pertenece al usuario.

#### PUT `/api/credenciales/:id`

Actualiza los datos de una credencial del usuario autenticado.

* Headers:

  * `Authorization: Bearer <token>`

* Body (JSON) – todos opcionales, se actualiza solo lo enviado:

```json
{
  "servicio": "nuevoServicio",
  "usuario": "nuevoUsuario",
  "password": "nuevaPass"
}
```

* Respuesta (200):

```json
{
  "message": "credencial actualizada",
  "credencial": {
    "id": 1,
    "servicio": "nuevoServicio",
    "usuario": "nuevoUsuario",
    "creado": "2025-01-01T12:00:00.000Z"
  }
}
```

* Errores:

  * 404 si la credencial no existe o no pertenece al usuario.

#### DELETE `/api/credenciales/:id`

Elimina una credencial del usuario autenticado.

* Headers:

  * `Authorization: Bearer <token>`

* Parámetros:

  * `:id` → ID numérico de la credencial.

* Respuesta (200) (forma general):

```json
{
  "message": "credencial borrada",
  "credencial": {
    "id": 1,
    "servicio": "gmail",
    "usuario": "miCorreo",
    "creado": "2025-01-01T12:00:00.000Z"
  }
}
```

* Errores:

  * 404 si la credencial no existe o no pertenece al usuario. 

---

### 5. Utilidades (`/api/utils`)

#### POST `/api/utils/strength`

Evalúa la fuerza de una contraseña. No requiere autenticación. 

* Body (JSON):

```json
{
  "password": "MiPass123!"
}
```

* Lógica:

  * Suma puntos si:

    * Longitud >= 8.
    * Tiene mayúsculas.
    * Tiene minúsculas.
    * Tiene números.
    * Tiene caracteres especiales.
  * Devuelve:

    * `score` (0 a 5).
    * `strength` (`fuerte`, `normal`, `debil`, `muy_debil`).
    * `feedback`: array de mensajes cortos.

* Respuesta (ejemplo):

```json
{
  "password": "********",
  "score": 4,
  "strength": "normal",
  "max_score": 5,
  "feedback": ["esta bien"]
}
```

* Errores:

  * 400 si no se envía `password`.

---

## Cómo instalar y correr el proyecto

1. **Requisitos previos**

   * Node.js 18 o superior instalado.
   * npm instalado (viene con Node.js).

2. **Clonar el repositorio**

   ```bash
   git clone https://github.com/TU-USUARIO/TU-REPO.git
   cd TU-REPO
   ```

3. **Instalar dependencias**

   ```bash
   npm install
   ```

   Esto instala `express`, `bcryptjs`, `jsonwebtoken`, `crypto-js` y `nodemon` como dev dependency.

4. **Ejecutar en modo desarrollo** (con recarga automática)

   ```bash
   npm run dev
   ```

5. **Ejecutar en modo producción/simple**

   ```bash
   npm start
   ```

   Ambos scripts levantan el servidor usando `src/app.js` en el puerto `3000` por defecto.

6. **Verificar que el servidor está corriendo**

   * Ir a: `http://localhost:3000/api/estado`
   * Deberías ver un JSON con `status: "andando"`.

---

## Cómo probar la API

Puedes usar Postman, Insomnia o `curl`. A continuación, un flujo típico:

### 1. Crear usuario

* Método: `POST`
* URL: `http://localhost:3000/api/user/`
* Headers:

  * `Content-Type: application/json`
* Body:

```json
{
  "username": "miUsuario",
  "email": "correo@example.com",
  "password": "MiPass123!"
}
```

### 2. Hacer login y obtener token

* Método: `POST`
* URL: `http://localhost:3000/api/auth/login`
* Headers:

  * `Content-Type: application/json`
* Body:

```json
{
  "email": "correo@example.com",
  "password": "MiPass123!"
}
```

* Respuesta:

  * Copiar el valor de `token`.

### 3. Crear una credencial protegida

* Método: `POST`
* URL: `http://localhost:3000/api/credenciales/`
* Headers:

  * `Content-Type: application/json`
  * `Authorization: Bearer <token>`
* Body:

```json
{
  "servicio": "gmail",
  "usuario": "miCorreo",
  "password": "PassGmail123!"
}
```

### 4. Listar credenciales del usuario

* Método: `GET`
* URL: `http://localhost:3000/api/credenciales/`
* Headers:

  * `Authorization: Bearer <token>`

Verás un listado con `cantidad` y el array de `credenciales`.

### 5. Ver detalles (incluyendo contraseña desencriptada)

* Método: `GET`
* URL: `http://localhost:3000/api/credenciales/1`
* Headers:

  * `Authorization: Bearer <token>`

### 6. Actualizar una credencial

* Método: `PUT`
* URL: `http://localhost:3000/api/credenciales/1`
* Headers:

  * `Content-Type: application/json`
  * `Authorization: Bearer <token>`
* Body (ejemplo):

```json
{
  "password": "PassGmailActualizada456!"
}
```

### 7. Borrar una credencial

* Método: `DELETE`
* URL: `http://localhost:3000/api/credenciales/1`
* Headers:

  * `Authorization: Bearer <token>`

### 8. Probar la utilidad de fuerza de contraseña

* Método: `POST`
* URL: `http://localhost:3000/api/utils/strength`
* Headers:

  * `Content-Type: application/json`
* Body:

```json
{
  "password": "MiPass123!"
}
```

La respuesta indicará el `score`, la `strength` y un `feedback` con sugerencias. 

---

Con este README deberías poder clonar el proyecto, instalar dependencias, levantar el servidor y probar todos los endpoints principales de la API.
