# Gestor de Contraseñas 🔐

## Descripción
Sistema web para gestionar contraseñas de forma segura. Permite a los usuarios guardar, consultar y administrar sus credenciales con encriptación avanzada.

## Funcionalidades
- Registro y autenticación segura de usuarios
- Almacenamiento encriptado de contraseñas
- Validación de fortaleza de contraseñas
- Gestión completa de credenciales (crear, listar, ver, actualizar, eliminar)
- Autenticación por tokens JWT
- API RESTful

## Endpoints de la API

### Públicos
- `GET /api/estado` - Estado del servicio
- `POST /api/user` - Registrar nuevo usuario
- `POST /api/auth/login` - Iniciar sesión
- `POST /api/utils/strength` - Validar fortaleza de contraseña

### Protegidos (requieren token JWT)
- `GET /api/credenciales` - Listar todas las credenciales
- `POST /api/credenciales` - Crear nueva credencial
- `GET /api/credenciales/:id` - Ver credencial específica
- `PUT /api/credenciales/:id` - Actualizar credencial
- `DELETE /api/credenciales/:id` - Eliminar credencial

## Como instalar y correr el proyecto

### Requisitos
- Node.js 18.x o superior
- npm 9.x o superior

### Instalación
1. Clonar el repositorio
2. Instalar dependencias:
npm install
3. Ejecutar:
npm run dev (disponible en http://localhost:3000)
