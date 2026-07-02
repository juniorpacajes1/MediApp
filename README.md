# MediApp Pro

Aplicacion profesional de gestion y agendamiento de citas medicas construida con Flutter y Supabase.

## Arquitectura

Se utiliza **Clean Architecture** con las siguientes capas:

- **domain/**: Entidades y contratos (interfaces abstractas). No depende de ningun framework.
- **data/**: Implementacion concreta de repositorios usando Supabase (DataSource + RepositoryImpl).
- **presentation/**: Pantallas (screens), proveedores de estado (Provider) y widgets reutilizables.
- **core/**: Tema, constantes, errores, validadores y utilidades compartidas.
- **routes/**: Navegacion centralizada con rutas nombradas.
- **services/**: Servicios transversales como la navegacion por rol.

### Gestion de Estado

Se eligio **Provider** porque:
- Es simple de entender y mantener.
- Viene incluido con Flutter (sin dependencias extra significativas).
- Suficiente para el tamano y complejidad de esta aplicacion.
- Facilita la inyeccion de dependencias y la comunicacion entre widgets.

### Flujo del Sistema

1. El usuario abre la app y ve la pantalla de seleccion de rol (Paciente, Medico, Secretaria).
2. Selecciona un rol y es redirigido al login.
3. Al iniciar sesion, se verifica el rol y se navega al dashboard correspondiente.
4. Cada rol tiene funcionalidades especificas (ver diagrama abajo).
5. Las citas fluyen: Paciente solicita -> Secretaria aprueba -> Medico atiende.

## Estructura de Carpetas

```
lib/
  config/          - Configuracion de Supabase
  core/
    constants/     - Constantes, nombres de tablas y rutas
    errors/        - Excepciones personalizadas
    theme/         - Tema Material Design 3
    utils/         - Validadores y clase Result
  data/
    datasources/   - SupabaseDataSource (acceso directo a Supabase)
    repositories/  - Implementaciones de repositorios
  domain/
    entities/      - Entidades de dominio (Usuario, Cita, etc.)
    repositories/  - Contratos abstractos de repositorios
  presentation/
    providers/     - AuthProvider, CitaProvider, NotificacionProvider
    screens/       - Pantallas organizadas por rol
    widgets/       - Widgets reutilizables
  routes/          - Definicion de rutas
  services/        - Servicio de navegacion por rol
```

## Conectar Supabase

1. Crear un proyecto en [supabase.com](https://supabase.com).
2. Ir a **Settings > API** y copiar:
   - **Project URL** (ej: `https://xxxxx.supabase.co`)
   - **anon public** key (la que empieza con `eyJ...`)
3. Abrir `lib/config/supabase_config.dart` y reemplazar los valores.

## Ejecutar el Proyecto

### Requisitos
- Flutter 3.x (version estable)
- Dart 3.x
- Supabase configurado

### Pasos

1. Clonar/descomprimir el proyecto
2. Ejecutar el script SQL en **Supabase Dashboard > SQL Editor**:
   ```
   Copiar todo el contenido de database/schema.sql y ejecutarlo
   ```
3. Instalar dependencias:
   ```
   flutter pub get
   ```
4. Ejecutar en navegador (recomendado para prueba rapida):
   ```
   flutter run -d chrome
   ```
5. O en un dispositivo Android conectado:
   ```
   flutter run
   ```

## Como Desplegar

### Web
```
flutter build web
```
Subir la carpeta `build/web` a cualquier hosting (Vercel, Netlify, Firebase Hosting).

### Android
```
flutter build apk --release
```
El APK generado estara en `build/app/outputs/flutter-apk/`.

## Modelo de Dominio

| Entidad | Descripcion |
|---------|-------------|
| **Usuario** | Datos comunes: nombre, email, telefono, rol, estado |
| **Paciente** | Perfil extendido: fecha nacimiento, identidad, direccion, contacto emergencia |
| **Medico** | Perfil profesional: colegiatura, consultorio, biografia |
| **Secretaria** | Perfil administrativo: identidad, telefono |
| **Especialidad** | Especialidades medicas (Cardiologia, Pediatria, etc.) |
| **HorarioDisponible** | Horarios configurados por medico por dia de semana |
| **Cita** | Citas con estados: solicitada, aceptada, realizada, cancelada |
| **ResultadoCita** | Diagnostico, medicamentos y observaciones de una cita |
| **Notificacion** | Alertas para el usuario: confirmacion, cancelacion, recordatorio |

## Roles del Sistema

### Paciente
- Ver dashboard con proximas citas
- Solicitar nuevas citas (especialidad > medico > horario)
- Ver historial de citas y resultados
- Cancelar citas pendientes
- Editar perfil

### Medico
- Ver agenda del dia con estadisticas
- Consultar agenda semanal con filtros
- Registrar atencion (diagnostico, medicamentos)
- Configurar horarios disponibles

### Secretaria
- Ver dashboard con citas pendientes
- Aprobar, cancelar y reprogramar citas
- Generar reportes con filtros (PDF/CSV)

## Agregar Nuevas Funcionalidades

1. Crear la entidad en `domain/entities/`.
2. Crear el contrato en `domain/repositories/`.
3. Implementar en `data/repositories/`.
4. Agregar metodos en `data/datasources/supabase_datasource.dart`.
5. Crear o actualizar el Provider en `presentation/providers/`.
6. Crear la pantalla en `presentation/screens/`.
7. Registrar la ruta en `routes/app_routes.dart`.
8. Crear las tablas/RLS necesarias en el SQL.

## Licencia

Proyecto privado. Todos los derechos reservados.