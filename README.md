[README.md](https://github.com/user-attachments/files/28729344/README.md)
# Actividad Docker — DAD

**Alumno:** Facundo Nahuel Castillo Jerez  
**Fecha:** 08/06/2026  
**Materia:** Despliegue de Aplicaciones en la Nube (DAD)  
**Repositorio base:** [docker-tutorial de joseluisgs](https://github.com/joseluisgs/docker-tutorial/tree/master)

---

## ejem01 — Servidor web PHP con Apache

Se implementó un servidor web usando PHP 8.2 con Apache. Se corrigió el Dockerfile original que usaba `php:7.0-apache` (versión descontinuada) actualizándolo a `php:8.2-apache`.

### Dockerfile corregido

```dockerfile
FROM php:8.2-apache
COPY src/ /var/www/html/
EXPOSE 80
MAINTAINER JL Gonzalez "jlgs@cifpvirgendegracia.com"
```

### Comandos utilizados

```bash
docker build -t ejem01 .
docker run -d -p 8080:80 --name miejem01 ejem01
```

### Edición desde terminal con nano

```bash
docker exec -it miejem01 bash
apt-get update
apt-get install -y nano
nano /var/www/html/index.html
```

<img width="987" height="574" alt="despliegue actividad 1" src="https://github.com/user-attachments/assets/306d1d8a-dcb4-4d33-b9cb-1243c74c926e" />



---

## ejem02 — WordPress con MariaDB (comandos manuales)

Se interpretaron y ejecutaron manualmente los comandos del archivo `run.sh`, que levanta dos contenedores: una base de datos MariaDB y WordPress.

### Comandos ejecutados

```bash
# Base de datos
docker run -d --name wordpress-db \
    -e MYSQL_ROOT_PASSWORD=secret \
    -e MYSQL_DATABASE=wordpress \
    -e MYSQL_USER=manager \
    -e MYSQL_PASSWORD=secret \
    mariadb:10.3.9

# WordPress
docker run -d --name wordpress \
    --link wordpress-db:mysql \
    -e WORDPRESS_DB_USER=manager \
    -e WORDPRESS_DB_PASSWORD=secret \
    -p 8081:80 \
    wordpress:4.9.8
```


<img width="1896" height="863" alt="Capturawordpress" src="https://github.com/user-attachments/assets/cf7b6dd6-1de9-4892-aa6c-cc37b97c7f91" />

---

## ejem03 — WordPress con red propia y análisis de portabilidad

Se ejecutó el script `run.sh` del ejem03, que mejora el ejem02 usando una red Docker propia (`mi-network`) en vez de `--link`.

### Comandos ejecutados

```bash
docker network create mi-network

docker run -d --name wordpress-db \
    --net=mi-network \
    -e MYSQL_ROOT_PASSWORD=secret \
    -e MYSQL_DATABASE=wordpress \
    -e MYSQL_USER=manager \
    -e MYSQL_PASSWORD=secret \
    mariadb:10.3.9

docker run -d --name wordpress \
    --net=mi-network \
    -e WORDPRESS_DB_HOST=wordpress-db \
    -e WORDPRESS_DB_USER=manager \
    -e WORDPRESS_DB_PASSWORD=secret \
    -p 8082:80 \
    wordpress:4.9.8
```

### Inconvenientes de portabilidad de scripts de S.O.

| Problema | Descripción |
|----------|-------------|
| **Dependencia del S.O.** | `run.sh` solo funciona en Linux/macOS. En Windows requiere Git Bash o WSL. |
| **Rutas absolutas** | Los paths de Linux (`/var/lib/mysql`) no son válidos en Windows, causando errores con `--mount`. |
| **Dependencia de herramientas** | El script asume que `bash` y `docker` están instalados y en el PATH. |
| **Solución** | Usar Docker Compose, que es portable entre sistemas operativos. |


### Captura — WordPress funcionando

<img width="1488" height="891" alt="Captura3wordpress" src="https://github.com/user-attachments/assets/0fc40ab9-03af-418a-888e-4b869b8d1cf2" />


---

## ejem07 — Stack completo con Docker Compose

Se implementó un stack LEMP (Linux, nginx, PHP, MariaDB) usando Docker Compose, con cuatro servicios: nginx, php7, mariadb y phpmyadmin.

### Servicios levantados

| Servicio | Puerto | Descripción |
|----------|--------|-------------|
| nginx | 8083 | Servidor web |
| php7 | — | Procesador PHP |
| mariadb | 3306 | Base de datos |
| phpmyadmin | 8084 | Administrador de BD |

### Comandos utilizados

```bash
cd docker
docker compose up -d
```

### Captura — Página PHP funcionando (puerto 8083)

<img width="982" height="212" alt="Capturaejercicio7" src="https://github.com/user-attachments/assets/638b1f72-f3ac-43ee-b600-704bca69a805" />

### Captura — phpMyAdmin funcionando (puerto 8084)

<img width="1908" height="575" alt="Captura72" src="https://github.com/user-attachments/assets/06d524c5-6702-45c7-80c5-aa27f941f822" />
