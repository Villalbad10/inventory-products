# Inventory Products - Microservicios con Docker

Este proyecto implementa un sistema de microservicios para la gestión de productos e inventario utilizando Docker Compose.

## 📋 Descripción del Sistema

El sistema está compuesto por:

- **Product Service** (Puerto 8081): Servicio para gestión de productos
- **Inventory Service** (Puerto 8082): Servicio para gestión de inventario
- **Product Database** (Puerto 5432): Base de datos PostgreSQL para productos
- **Inventory Database** (Puerto 5433): Base de datos PostgreSQL para inventario

## ⚠️ Nota Importante sobre Datos de Prueba

**Al inicializar el inventory-service, se ejecutará automáticamente un script que insertará datos de prueba en la base de datos de inventario.** Estos datos incluyen registros de ejemplo que permiten probar la funcionalidad del sistema inmediatamente después del despliegue.

## 🛠️ Requisitos Previos

Antes de ejecutar el sistema, asegúrate de tener instalado:

- [Docker](https://docs.docker.com/get-docker/) (versión 20.10 o superior)
- [Docker Compose](https://docs.docker.com/compose/install/) (versión 2.0 o superior)

### Verificar Instalación

```bash
# Verificar Docker
docker --version

# Verificar Docker Compose
docker-compose --version
```

## ⚙️ Configuración de Variables de Entorno

El proyecto utiliza variables de entorno para una configuración flexible. **Es obligatorio crear un archivo `.env` antes de ejecutar el sistema.**

### 1. Crear el archivo de configuración

```bash
# Copiar el archivo de ejemplo
cp env.example .env
```

### 2. Editar las variables de entorno

Edita el archivo `.env` con tus valores preferidos:

```bash
# Database Configuration
POSTGRES_USER=postgres
POSTGRES_PASSWORD=change_this_password

# Product Database
PRODUCT_DB_NAME=products_db
PRODUCT_DB_PORT=5432

# Inventory Database
INVENTORY_DB_NAME=inventory_db
INVENTORY_DB_PORT=5433

# Services Configuration
PRODUCT_SERVICE_PORT=8081
INVENTORY_SERVICE_PORT=8082

# API Keys
PRODUCT_API_KEY=your-product-service-key
INVENTORY_API_KEY=your-inventory-service-key
```

### 3. Personalizar la configuración (opcional)

Puedes cambiar:
- **Puertos** de los servicios y bases de datos
- **Nombres** de las bases de datos
- **Contraseñas** de las bases de datos
- **API Keys** para seguridad

## 🚀 Instrucciones de Ejecución

### 1. Clonar el Repositorio

```bash
git clone <tu-repositorio>
cd inventory-products
```

### 2. Configurar Variables de Entorno

```bash
# Crear archivo de configuración
cp env.example .env

# Editar el archivo .env con tus valores (opcional)
nano .env  # o usar tu editor preferido
```

### 3. Construir y Ejecutar los Servicios

```bash
# Construir e iniciar todos los servicios
docker-compose up --build

# O ejecutar en segundo plano
docker-compose up --build -d
```

### 4. Verificar el Estado de los Servicios

```bash
# Ver el estado de todos los contenedores
docker-compose ps

# Ver los logs de todos los servicios
docker-compose logs

# Ver logs de un servicio específico
docker-compose logs product-service
docker-compose logs inventory-service
```

## 📡 Endpoints de los Servicios

### Product Service (Puerto configurable, por defecto 8081)
- **Health Check**: `http://localhost:${PRODUCT_SERVICE_PORT}/actuator/health`
- **API Base**: `http://localhost:${PRODUCT_SERVICE_PORT}/api/v1`
- **Documentación Swagger**: [http://localhost:8081/swagger-ui/index.html#/](http://localhost:8081/swagger-ui/index.html#/)

### Inventory Service (Puerto configurable, por defecto 8082)
- **Health Check**: `http://localhost:${INVENTORY_SERVICE_PORT}/actuator/health`
- **API Base**: `http://localhost:${INVENTORY_SERVICE_PORT}/api/v1`
- **Documentación Swagger**: [http://localhost:8082/swagger-ui/index.html#/](http://localhost:8082/swagger-ui/index.html#/)

> **Nota**: Los puertos se configuran en el archivo `.env`. Por defecto son 8081 y 8082.

## 🗄️ Bases de Datos

### Product Database
- **Host**: localhost
- **Puerto**: `${PRODUCT_DB_PORT}` (por defecto: 5432)
- **Base de Datos**: `${PRODUCT_DB_NAME}` (por defecto: products_db)
- **Usuario**: `${POSTGRES_USER}` (por defecto: postgres)
- **Contraseña**: `${POSTGRES_PASSWORD}` (configurable)

### Inventory Database
- **Host**: localhost
- **Puerto**: `${INVENTORY_DB_PORT}` (por defecto: 5433)
- **Base de Datos**: `${INVENTORY_DB_NAME}` (por defecto: inventory_db)
- **Usuario**: `${POSTGRES_USER}` (por defecto: postgres)
- **Contraseña**: `${POSTGRES_PASSWORD}` (configurable)

> **Nota**: Todas las configuraciones de base de datos se pueden personalizar en el archivo `.env`.

## 🔧 Comandos Útiles

### Gestión de Servicios

```bash
# Detener todos los servicios
docker-compose down

# Detener y eliminar volúmenes (⚠️ Esto elimina los datos)
docker-compose down -v

# Reiniciar un servicio específico
docker-compose restart product-service

# Reconstruir y reiniciar un servicio
docker-compose up --build product-service
```

### Monitoreo y Debugging

```bash
# Ver logs en tiempo real
docker-compose logs -f

# Ver logs de un servicio específico
docker-compose logs -f inventory-service

# Acceder al shell de un contenedor
docker-compose exec product-service sh
docker-compose exec inventory-service sh

# Ver el uso de recursos
docker stats
```

### Limpieza

```bash
# Eliminar contenedores detenidos
docker-compose rm

# Eliminar imágenes no utilizadas
docker image prune

# Limpieza completa del sistema Docker
docker system prune -a
```

## 🐛 Solución de Problemas

### Problemas Comunes

1. **Puerto ya en uso**:
   ```bash
   # Verificar qué proceso usa el puerto
   netstat -tulpn | grep :8081
   
   # Cambiar el puerto en docker-compose.yml si es necesario
   ```

2. **Error de conexión a la base de datos**:
   ```bash
   # Verificar que las bases de datos estén corriendo
   docker-compose ps
   
   # Ver logs de las bases de datos
   docker-compose logs product-db
   docker-compose logs inventory-db
   ```

3. **Servicios no se inician**:
   ```bash
   # Ver logs detallados
   docker-compose logs --tail=100
   
   # Verificar health checks
   docker-compose ps
   ```

### Verificación del Estado del Sistema

```bash
# Verificar que todos los servicios estén corriendo
docker-compose ps

# Verificar health checks
curl http://localhost:8081/actuator/health
curl http://localhost:8082/actuator/health
```

### Verificación de Datos de Prueba

Una vez que el inventory-service esté ejecutándose, podrás verificar que los datos de prueba se han insertado correctamente:

```bash
# Ver logs del inventory-service para confirmar la inserción de datos
docker-compose logs inventory-service | grep -i "insert\|data\|sql"

# Acceder a la base de datos de inventario para ver los datos
docker-compose exec inventory-db psql -U postgres -d inventory_db -c "SELECT * FROM inventory;"
```

## 📊 Monitoreo de Recursos

```bash
# Ver uso de CPU y memoria
docker stats

# Ver información detallada de un contenedor
docker inspect product-service
docker inspect inventory-service
```

## 🔄 Actualización del Sistema

```bash
# Detener servicios
docker-compose down

# Actualizar código (si es necesario)
git pull

# Reconstruir y ejecutar
docker-compose up --build
```

## 📁 Estructura del Proyecto

```
inventory-products/
├── docker-compose.yml          # Configuración de Docker Compose
├── env.example                 # Archivo de ejemplo para variables de entorno
├── .env                        # Variables de entorno (crear desde env.example)
├── product-service/            # Servicio de Productos
│   ├── Dockerfile
│   └── src/
├── inventory-service/          # Servicio de Inventario
│   ├── Dockerfile
│   └── src/
└── README.md                   # Este archivo
```

> **Importante**: El archivo `.env` no debe incluirse en el control de versiones por seguridad. Úsalo para personalizar tu configuración local.

## 🔐 Variables de Entorno

El sistema utiliza las siguientes variables de entorno que se configuran en el archivo `.env`:

### Configuración de Base de Datos
```bash
POSTGRES_USER=postgres                    # Usuario de PostgreSQL
POSTGRES_PASSWORD=change_this_password    # Contraseña de PostgreSQL
PRODUCT_DB_NAME=products_db               # Nombre de la BD de productos
PRODUCT_DB_PORT=5432                      # Puerto de la BD de productos
INVENTORY_DB_NAME=inventory_db            # Nombre de la BD de inventario
INVENTORY_DB_PORT=5433                    # Puerto de la BD de inventario
```

### Configuración de Servicios
```bash
PRODUCT_SERVICE_PORT=8081                 # Puerto del servicio de productos
INVENTORY_SERVICE_PORT=8082               # Puerto del servicio de inventario
PRODUCT_API_KEY=your-product-service-key  # API Key para el servicio de productos
INVENTORY_API_KEY=your-inventory-service-key # API Key para el servicio de inventario
```

### Variables Internas (se generan automáticamente)
- `db_url`: Se construye automáticamente usando las variables de BD
- `url_service`: URL del servicio de productos para comunicación interna

## 📞 Soporte

Si encuentras problemas al ejecutar el sistema:

1. Verifica que Docker y Docker Compose estén correctamente instalados
2. Revisa los logs de los servicios: `docker-compose logs`
3. Asegúrate de que los puertos 8081, 8082, 5432 y 5433 estén disponibles
4. Verifica que no haya otros servicios usando los mismos puertos

## 🏃‍♂️ Inicio Rápido

Para usuarios que quieren ejecutar el sistema inmediatamente:

```bash
# 1. Clonar el repositorio
git clone <tu-repositorio>
cd inventory-products

# 2. Configurar variables de entorno
cp env.example .env

# 3. Ejecutar el sistema
docker-compose up --build

# 4. Verificar que esté funcionando
curl http://localhost:8081/actuator/health
curl http://localhost:8082/actuator/health
```

¡El sistema debería estar funcionando correctamente! 🎉

> **Nota**: Si cambias los puertos en el archivo `.env`, ajusta las URLs de verificación correspondientemente.
