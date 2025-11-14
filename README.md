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



## Probar la api 
application/json

### 1. Registrar Usuario
<img width="855" height="824" alt="image" src="https://github.com/user-attachments/assets/095ad8f7-4918-4729-9cdd-cbeebc974ad4" />

### 2. login y obtener token
<img width="894" height="914" alt="image" src="https://github.com/user-attachments/assets/e3582015-7da7-4005-ac6f-dc454726dd79" />

### 3. Validar fortaleza de la contraseña
<img width="854" height="852" alt="image" src="https://github.com/user-attachments/assets/059298cf-5f27-4b0a-be8f-ff22103f7750" />

### 4. Guardar credencial
<img width="877" height="887" alt="image" src="https://github.com/user-attachments/assets/d52a2766-b6d1-4a13-ab19-13fbd8f983d5" />

<img width="843" height="468" alt="image" src="https://github.com/user-attachments/assets/e4961512-cfb5-4da1-9e7a-3737112ededc" />

### 5. Listar credenciales
<img width="841" height="920" alt="image" src="https://github.com/user-attachments/assets/1ec71207-a767-41f4-8d7d-2ba61b7cf034" />

### 6. Ver credencial especifica
<img width="855" height="863" alt="image" src="https://github.com/user-attachments/assets/39ce0dde-5438-4e88-938f-4cee3a7ed5fd" />

### 7. Actualizar credencial
<img width="855" height="884" alt="image" src="https://github.com/user-attachments/assets/66683d36-4dbf-41ac-850f-8dbf816d86af" />

<img width="866" height="894" alt="image" src="https://github.com/user-attachments/assets/2cdd76c5-90ce-4998-a6cb-05a248994b23" />

### 8. Eliminar credencial
<img width="840" height="896" alt="image" src="https://github.com/user-attachments/assets/5ec2d231-178e-47b1-9ca5-c95054639360" />

<img width="873" height="848" alt="image" src="https://github.com/user-attachments/assets/a995d92b-abc6-4b81-8738-61ba68c0b098" />

### 9. Probar seguridad

<img width="894" height="852" alt="image" src="https://github.com/user-attachments/assets/6e72f4c4-bcba-4394-8fed-0a04c50a681b" />


