# Gestor de Contraseñas 🔐

TP - Arquitectura Web - Sistema para gestionar contraseñas de forma segura

## Descripción

API REST para guardar y administrar contraseñas de servicios como redes sociales, bancos, etc. Las contraseñas se guardan encriptadas y solo el usuario puede verlas.

## Como instalar y correr el proyecto

### Requisitos
- Node.js 18 o mas
- npm

### Pasos

1. **Clonar el repositorio**
```bash
git clone https://github.com/totagos/Arquitectura-Web.git
cd Arquitectura-Web
Instalar dependencias

bash
npm install
Ejecutar

bash
npm run dev
El servidor corre en http://localhost:3000

Como probar la API
Endpoints publicos
Ver estado

bash
GET /api/estado
Registrar usuario

bash
POST /api/user
{
  "username": "usuario",
  "password": "clave123",
  "email": "usuario@email.com"
}
Login (obtener token)

bash
POST /api/auth/login
{
  "email": "usuario@email.com", 
  "password": "clave123"
}
Validar contraseña

bash
POST /api/utils/strength
{
  "password": "miClave123!"
}
Endpoints con autenticacion (necesitan token)
Headers: Authorization: Bearer {token}

Guardar credencial

bash
POST /api/credenciales
{
  "servicio": "Facebook",
  "usuario": "mi_usuario",
  "password": "clave_facebook"
}
Listar credenciales

bash
GET /api/credenciales
Ver una credencial

bash
GET /api/credenciales/1
Actualizar credencial

bash
PUT /api/credenciales/1
{
  "servicio": "Facebook Personal"
}
Eliminar credencial

bash
DELETE /api/credenciales/1
Ejemplos con curl
Registrarse

bash
curl -X POST http://localhost:3000/api/user \
  -H "Content-Type: application/json" \
  -d '{"username":"test","password":"123456","email":"test@email.com"}'
Login

bash
curl -X POST http://localhost:3000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"test@email.com","password":"123456"}'
Guardar contraseña (con token)

bash
curl -X POST http://localhost:3000/api/credenciales \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer {token}" \
  -d '{"servicio":"Gmail","usuario":"yo","password":"clave_gmail"}'
Tecnologias usadas
Node.js + Express

JWT para autenticacion

bcryptjs para hashear contraseñas

crypto-js para encriptar credenciales
