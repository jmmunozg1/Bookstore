# S2566-0673 Topicos especiales en telematica

## Estudiante(s): Juan David Zapata Moncada (jdzapatam@eafit.edu.co) y Juan Miguel Muñoz Garcia (jmmunozg1@eafit.edu.co)

## Profesor: Alvaro Enrique Ospina Sanjuan

### Video explicativo: https://youtu.be/JTWK2fzk9UI

#### Usuario con el que funcionó:
User: jmmunozg1@eafit.edu.co
pass: Mandr@kem012.

# Proyecto 2

### 1. breve descripción de la actividad

El presente proyecto corresponde al Proyecto 2 del curso ST0263 – Tópicos Especiales en Telemática de la Universidad EAFIT, cuyo objetivo principal es diseñar e implementar la evolución de una aplicación monolítica hacia una arquitectura escalable en la nube, utilizando servicios de Amazon Web Services (AWS).

Para ello, se parte de la aplicación BookStore, un sistema monolítico desarrollado en Python (Flask) y MySQL, que simula un portal de venta y compra de libros de segunda mano. La aplicación permite registrar usuarios, visualizar catálogos, gestionar compras y simular procesos de pago y envío.

A lo largo del proyecto, se busca aplicar diferentes patrones y niveles de escalabilidad en la nube, cumpliendo con los siguientes objetivos secuenciales:

Objetivo 1: Desplegar la aplicación monolítica en dos máquinas virtuales (una para la base de datos y otra para la aplicación con NGINX y certificado SSL).

Objetivo 2: Escalar la aplicación monolítica en AWS utilizando Auto Scaling Groups (ASG), Load Balancer (ELB), RDS para la base de datos y EFS para almacenamiento compartido.

Objetivo 3: Desplegar la misma aplicación monolítica dentro de un clúster Kubernetes (EKS), integrando servicios como Amazon EFS y Amazon RDS para garantizar alta disponibilidad y tolerancia a fallos.

Objetivo 4: Evolucionar el sistema a una arquitectura basada en microservicios, o desplegar una base de datos en clúster dentro de Kubernetes.


## 1.1. Que aspectos cumplió o desarrolló de la actividad propuesta por el profesor (requerimientos funcionales y no funcionales)


### Objetivo 1: Despliegue de la aplicación monolítica en 2 máquinas virtuales (20%)

#### Requerimientos funcionales cumplidos:

 - Se desplegó la aplicación BookStore monolítica en dos instancias EC2 en AWS:

- Una VM para la base de datos MySQL.

- Otra VM para la aplicación Flask + NGINX.

- Se configuró un proxy inverso con NGINX, que direcciona el tráfico HTTP hacia el contenedor Flask.

- Se generó y aplicó un certificado SSL mediante Certbot para asegurar la comunicación HTTPS.

#### Requerimientos no funcionales cumplidos:

- Comunicación segura (HTTPS).

- Separación de responsabilidades entre la capa de aplicación y la base de datos.

- Configuración de un firewall (Security Groups) restringiendo puertos innecesarios.

- Documentación detallada del proceso de despliegue manual.
  


### Objetivo 2: Escalamiento de la aplicación monolítica en AWS (30%)

#### Requerimientos funcionales cumplidos:

- Implementación de un Auto Scaling Group (ASG) para instancias EC2, lo que permite escalar horizontalmente la capa de aplicación.

- Integración de un Load Balancer (ELB) para distribuir el tráfico entre las instancias.

- Migración de la base de datos a Amazon RDS (MySQL), garantizando disponibilidad y respaldo automático.

- Configuración de Amazon EFS como sistema de archivos compartido entre las instancias de aplicación.

#### Requerimientos no funcionales cumplidos:

- Alta disponibilidad mediante múltiples instancias balanceadas.

- Escalabilidad automática basada en carga.

- Persistencia y consistencia de datos garantizada mediante EFS.

- Reducción del tiempo de inactividad mediante balanceo y monitoreo.

### Objetivo 3: Despliegue en clúster Kubernetes (EKS) (20%)

#### Requerimientos funcionales cumplidos:

- Creación de un clúster EKS en AWS con eksctl.

- Despliegue de la aplicación monolítica dentro del clúster mediante archivos YAML (Deployment, Service, Namespace, Secret, PersistentVolume).

- Integración con Amazon EFS como volumen compartido.

- Configuración de un LoadBalancer Service que expone la aplicación al público.

#### Requerimientos no funcionales cumplidos:

- Despliegue automatizado y reproducible mediante kubectl y kustomize.

- Escalabilidad horizontal configurada a través de réplicas en el Deployment.

-  Resiliencia ante fallos mediante pods distribuidos y balanceados.

- Persistencia del almacenamiento externo (EFS).

- Integración con RDS para la base de datos.

### Objetivo 4: Evolución hacia microservicios o alta disponibilidad de base de datos (30%)

#### Requerimientos funcionales cumplidos:

- Se exploró la reingeniería de la aplicación BookStore para dividirla en tres posibles microservicios:

- Autenticación

- Catálogo

- Compras y envíos

- Alternativamente, se diseñó un entorno con Percona XtraDB Cluster (PXC) como base de datos en clúster con alta disponibilidad.

#### Requerimientos no funcionales cumplidos:

- Diseño modular orientado a separación de responsabilidades.

- Alta disponibilidad y replicación de datos (en caso de PXC).

- Capacidad de despliegue independiente por servicio (en caso de evolución a microservicios).

- Mantenimiento simplificado mediante separación lógica de componentes.

## 1.2. Que aspectos NO cumplió o desarrolló de la actividad propuesta por el profesor (requerimientos funcionales y no funcionales)
tuvimos problemas luego de crear el cluster para la base de datos por IAM 
<img width="2501" height="1293" alt="image" src="https://github.com/user-attachments/assets/16addc83-a202-41ff-a007-5635928b181d" />

De igual forma, para el objetivo dos presentamos dificultades para crear la DB en RDS por problemas con Roles de IAM

<img width="1650" height="332" alt="image" src="https://github.com/user-attachments/assets/c426e97b-d12a-4618-9d64-26862c91e7ea" />

 Para este mismo objetivo no pudimos implementar un dominio con certificado, por lo que no se pudo aaceder mediante https. Las razones para no implementar esto tuvieron que ver, de nuevo, con permisos. Las opciones eran: registrar el dominio en AWS mediante Route 53 (no teniamos permisos) o comprar un dominio.

# 2. información general de diseño de alto nivel, arquitectura, patrones, mejores prácticas utilizadas.

El proyecto BookStore evolucionó desde una aplicación monolítica local hacia una arquitectura distribuida y escalable en la nube AWS, pasando por distintas etapas que incorporan principios de separación de responsabilidades, alta disponibilidad, automatización e infraestructura como código.

El diseño general se compone de los siguientes niveles:

## Capa de aplicación (Flask):
La aplicación web desarrollada en Python (Flask) se ejecuta inicialmente en contenedores Docker, y posteriormente se despliega en EKS (Elastic Kubernetes Service).
En cada fase se utilizan prácticas de empaquetamiento reproducible (Dockerfile) y despliegue automatizado mediante YAML Manifests (Deployment, Service, Namespace, Secrets).

## Capa de base de datos:
En las primeras fases, la base de datos se implementa como un contenedor MySQL dentro de una máquina virtual.
En el escalamiento se migra hacia un servicio administrado de base de datos (Amazon RDS), y en el objetivo final se propone una base de datos en clúster (Percona XtraDB) dentro de Kubernetes, garantizando redundancia y tolerancia a fallos.

## Capa de almacenamiento compartido (EFS):
Se incorpora Amazon EFS (Elastic File System) para compartir archivos entre réplicas del contenedor Flask y entre pods dentro de EKS.
Esto asegura persistencia y consistencia en un entorno escalable.

## Capa de red y balanceo:
En el despliegue en la nube, se utilizan Elastic Load Balancer (ELB) y Service Type=LoadBalancer para distribuir tráfico entre instancias o pods.
Esto permite garantizar disponibilidad y recuperación ante fallos.

## Capa de seguridad:
Se aplican medidas de seguridad en cada nivel:

- Certificados SSL y Let's Encrypt.

- Security Groups con control estricto de puertos.

- Gestión de secretos mediante objetos Kubernetes Secrets.

- Accesos IAM limitados a roles específicos (LabEksClusterRole, LabEksNodeRole).
  
## Arquitectura implementada
<img width="668" height="699" alt="image" src="https://github.com/user-attachments/assets/3173a7ea-f421-4b75-8af1-fe6c5b6dd706" />

## Patrones de diseño y arquitectura aplicados

| Categoría               | Patrón aplicado                       | Descripción                                                                                       |
| ----------------------- | ------------------------------------- | ------------------------------------------------------------------------------------------------- |
| **Arquitectura**        | **Separación de capas (3-tier)**      | Separa la lógica de aplicación, la base de datos y la capa de presentación/red.                   |
| **Escalabilidad**       | **Horizontal Scaling**                | Se agregan instancias (réplicas o pods) según la carga del sistema.                               |
| **Infraestructura**     | **Infraestructura como Código (IaC)** | Todo el entorno se define en archivos YAML (`cluster-eks.yaml`, `bookstore.yaml`, `efs-pv.yaml`). |
| **Despliegue**          | **Containerization Pattern**          | Uso de Docker para empaquetar la aplicación y garantizar portabilidad.                            |
| **Disponibilidad**      | **Load Balancing Pattern**            | Distribuye tráfico mediante ELB y Kubernetes Services.                                            |
| **Persistencia**        | **Shared Storage Pattern**            | Uso de EFS como volumen persistente accesible desde todos los pods.                               |
| **Alta disponibilidad** | **Replication Pattern**               | Réplicas de pods en diferentes nodos del clúster.                                                 |
| **Seguridad**           | **Secret Management Pattern**         | Variables sensibles gestionadas con `Secrets` en Kubernetes.                                      |



# 3. Descripción del ambiente de desarrollo y técnico: lenguaje de programación, librerias, paquetes, etc, con sus numeros de versiones.

La aplicación BookStore está desarrollada principalmente en Python 3.10+, utilizando el framework Flask para la construcción del backend web.
Durante el despliegue en contenedores y en Kubernetes se usaron herramientas y librerías adicionales orientadas a la gestión de dependencias, base de datos y despliegue en nube.

| Tecnología               | Versión        | Descripción                                       |
| ------------------------ | -------------- | ------------------------------------------------- |
| **Python**               | 3.10 – 3.12    | Lenguaje principal del backend                    |
| **Flask**                | 2.3.x          | Framework web microservicio en Python             |
| **MySQL**                | 8.x            | Motor de base de datos relacional                 |
| **Docker**               | 28.1.1         | Contenerización del entorno de aplicación         |
| **Kubernetes (kubectl)** | v1.32.2        | Orquestador de contenedores                       |
| **AWS CLI**              | 2.27.10        | Interfaz de línea de comandos de AWS              |
| **eksctl**               | 0.192.0        | Creación y administración de clústeres EKS        |
| **Helm**                 | 3.16.x         | Gestor de paquetes de Kubernetes                  |
| **NGINX**                | 1.24.x         | Proxy inverso y balanceador de tráfico HTTP/HTTPS |
| **Certbot**              | Última versión | Generación automática de certificados SSL         |

#### Principales librerías Python

Flask==2.3.2
Werkzeug==2.3.7
mysql-connector-python==8.3.0
gunicorn==21.2.0
requests==2.31.0

#### Cómo se compila y ejecuta la aplicación

1. Clonación y ejecución:

```
# Clonar
git clone https://github.com/jmmunozg1/Bookstore.git
cd Bookstore

# Crear venv + deps
python3 -m venv .venv
source .venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt

# Variables de entorno (ajusta credenciales)
export FLASK_ENV=development
export DB_HOST=127.0.0.1
export DB_NAME=bookstore
export DB_USER=appuser
export DB_PASSWORD=APP_STRONG_PASS!

# Correr (Flask dev) o Gunicorn (prod)
flask run --host=0.0.0.0 --port=5000
# ó
gunicorn -b 0.0.0.0:5000 app:app
```
App: http://127.0.0.1:5000

2. Ejecución con docker/Compose:
```
# Crear .env (ejemplo)
cat > .env <<'ENV'
DB_HOST=10.0.10.221
DB_NAME=bookstore
DB_USER=appuser
DB_PASSWORD=APP_STRONG_PASS!
FLASK_ENV=production
PORT=5000
UPLOADS_DIR=/app/uploads
BACKUPS_DIR=/app/backups
ENV

# Build & run
docker compose build --no-cache
docker compose up -d
docker compose ps
curl -I http://127.0.0.1:5000
```
Ejecución en AWS:

1. Preparar instancia APP (cada EC2)
```
sudo apt-get update -y
sudo apt-get install -y docker.io docker-compose-plugin git nfs-common
sudo systemctl enable --now docker

# Código
sudo mkdir -p /opt && sudo chown $USER:$USER /opt
git clone https://github.com/USUARIO/Bookstore.git /opt/bookstore
cd /opt/bookstore

# Variables (ajusta DB_HOST a tu MySQL-A y EFS ya montado en /mnt/efs)
cat > .env <<'ENV'
DB_HOST=10.0.10.221
DB_NAME=bookstore
DB_USER=appuser
DB_PASSWORD=APP_STRONG_PASS!
FLASK_ENV=production
PORT=5000
UPLOADS_DIR=/app/uploads
BACKUPS_DIR=/app/backups
ENV

# Override: usa MySQL externo y EFS
cat > docker-compose.override.yml <<'YAML'
services:
  db:
    deploy: { replicas: 0 }
    restart: "no"
  flaskapp:
    depends_on: []
    environment:
      - FLASK_APP=app.py
      - FLASK_ENV=production
      - PORT=5000
      - DB_HOST=${DB_HOST}
      - DB_NAME=${DB_NAME}
      - DB_USER=${DB_USER}
      - DB_PASSWORD=${DB_PASSWORD}
      - UPLOADS_DIR=${UPLOADS_DIR}
      - BACKUPS_DIR=${BACKUPS_DIR}
      - SQLALCHEMY_DATABASE_URI=mysql+pymysql://${DB_USER}:${DB_PASSWORD}@${DB_HOST}/${DB_NAME}
    command: flask run --host=0.0.0.0 --port=5000
    volumes:
      - /mnt/efs/bookstore/uploads:/app/uploads
      - /mnt/efs/bookstore/backups:/app/backups
YAML

# Levantar app
docker compose pull || true
docker compose up -d --remove-orphans
curl -I http://127.0.0.1:5000
```
2) EFS (en cada instancia)

```
# Reemplaza por tu DNS o IP del mount target de EFS
sudo mkdir -p /mnt/efs
sudo mount -t nfs4 -o nfsvers=4.1 <EFS-MOUNT-TARGET>:/ /mnt/efs
# Persistente:
echo "<EFS-MOUNT-TARGET>:/ /mnt/efs nfs4 nfsvers=4.1,_netdev 0 0" | sudo tee -a /etc/fstab
```
3) MySQL en EC2
   - mysql-a: base bookstore, usuario appuser/APP_STRONG_PASS!
   - Seguridad: abrir 3306 solo desde el SG de la App (SG-APP-ASG)

 4) ALB + Target Group + ASG
    - Target Group: tipo Instances, HTTP:5000, health check Path=/ (200–399)
    - ALB (internet-facing): listener :80 → Forward → TG
    - ASG: Launch Template de la App, 2 subnets privadas (2 AZ), Desired=2. Adjunta el TG
    - Security Groups:
        SG-ALB: Inbound 80 desde 0.0.0.0/0.
        SG-APP-ASG: Inbound 5000 desde SG-ALB.
        SG-EFS: Inbound 2049 desde SG-APP-ASG.
        SG-MySQL: Inbound 3306 desde SG-APP-ASG

 5) Validación
```
ALB="alb-bookstore-XXXX.us-east-1.elb.amazonaws.com"
curl -I http://$ALB              # 200 OK
for i in {1..4}; do curl -s http://$ALB/ | head -n1; done

# En cada APP (sustituye IPs privadas)
ssh ubuntu@10.0.10.151 "mount | grep /mnt/efs; nc -zv 10.0.10.221 3306; curl -I http://127.0.0.1:5000 | head -n1"
ssh ubuntu@10.0.11.105 "mount | grep /mnt/efs; nc -zv 10.0.10.221 3306; curl -I http://127.0.0.1:5000 | head -n1"
```

5. Acceder a la URL pública del LoadBalancer.


| Parámetro                       | Valor / Descripción                    |
| ------------------------------- | -------------------------------------- |
| **Puerto aplicación Flask**     | 5000                                   |
| **Puerto NGINX (exposición)**   | 80 / 443                               |
| **Servicio LoadBalancer (EKS)** | Puerto 80 (HTTP) → TargetPort 5000     |
| **Base de datos RDS MySQL**     | Puerto 3306                            |
| **Sistema de archivos EFS**     | Montado en `/mnt/efs`                  |
| **Namespace Kubernetes**        | `bookstore`                            |
| **Deployment Name**             | `bookstore`                            |
| **Service Name**                | `bookstore-svc`                        |
| **Cluster Name**                | `bookstore-eks-qa`                     |
| **Región AWS**                  | `us-east-1`                            |
| **Instancias EC2 (nodos)**      | `t3.medium`                            |
| **Roles IAM**                   | `LabEksClusterRole` y `LabEksNodeRole` |

Resultados visuales y comprobación (pantallazos sugeridos)

<img width="2117" height="1062" alt="image" src="https://github.com/user-attachments/assets/f174de87-a71d-4b12-865c-11a83fc7f904" />

<img width="1853" height="1035" alt="image" src="https://github.com/user-attachments/assets/17e31a32-dc50-4e70-b778-39d86019afa4" />

<img width="2521" height="1302" alt="image" src="https://github.com/user-attachments/assets/80edc49b-387e-4b9a-87a2-91039875f595" />



# 4. Descripción del ambiente de EJECUCIÓN (en producción) lenguaje de programación, librerias, paquetes, etc, con sus numeros de versiones.

| Componente                | Tecnología           | Versión | Función principal                              |
| ------------------------- | -------------------- | ------- | ---------------------------------------------- |
| **Backend Web**           | Python               | 3.10    | Lenguaje del servidor Flask                    |
|                           | Flask                | 2.3.x   | Framework web backend                          |
| **Servidor Web**          | NGINX                | 1.24    | Proxy inverso / balanceador interno            |
| **Contenedores**          | Docker               | 28.1.1  | Plataforma de contenerización                  |
| **Orquestación**          | Kubernetes (kubectl) | v1.32.2 | Orquestador de servicios en EKS                |
| **Cluster Manager**       | eksctl               | 0.192.0 | Creación y administración del clúster EKS      |
| **Paquetería Helm**       | Helm                 | 3.16.x  | Instalación de componentes externos            |
| **Base de datos**         | Amazon RDS (MySQL)   | 8.x     | Motor relacional administrado                  |
| **Almacenamiento**        | Amazon EFS           | —       | Sistema de archivos compartido (ReadWriteMany) |
| **Infraestructura Cloud** | AWS                  | —       | Proveedor de nube (EKS, EFS, RDS, ELB)         |


# IP o nombres de dominio en nube o en la máquina servidor.

| Recurso                      | Tipo          | Valor / Descripción                                                     |
| ---------------------------- | ------------- | ----------------------------------------------------------------------- |
| **Cluster EKS**              | Kubernetes    | `bookstore-eks-qa`                                                      |
| **Namespace**                | Kubernetes    | `bookstore`                                                             |
| **Service (LoadBalancer)**   | IP pública    | `a1b2c3d4e5f6.us-east-1.elb.amazonaws.com` *(asignada automáticamente)* |
| **RDS Endpoint**             | Base de datos | `bookstore-db.c3y8aeeqcgi9.us-east-1.rds.amazonaws.com`                 |
| **EFS ID**                   | Filesystem    | `fs-0cee713593f03f352`                                                  |
| **Puerto aplicación Flask**  | HTTP interno  | `5000`                                                                  |
| **Puerto público**           | HTTP externo  | `80`                                                                    |
| **Puerto seguro** | HTTPS                    | `443`                                                                   |


## una mini guia de como un usuario utilizaría el software o la aplicación

Una vez desplegada, la aplicación BookStore es accesible desde cualquier navegador web.
Su comportamiento es el mismo que en la versión monolítica, pero ahora con escalabilidad y alta disponibilidad en la nube.

### Pasos para el usuario:

1. Registro o inicio de sesión:
El usuario accede a la interfaz principal y puede registrarse o iniciar sesión mediante formulario de autenticación.

2. Exploración del catálogo:
Desde la vista principal, se listan los libros publicados por otros usuarios.
Cada libro incluye título, autor, descripción, precio y disponibilidad.

3. Publicar un libro:
El usuario autenticado puede crear una nueva publicación especificando detalles del libro, precio y cantidad disponible.

4. Proceso de compra:
Al seleccionar un libro, el usuario puede simular el proceso de pago y envío.
Las órdenes quedan registradas en la base de datos RDS.

5. Gestión y seguimiento:
Los usuarios pueden revisar su historial de compras o publicaciones activas.

## opcionalmente - si quiere mostrar resultados o pantallazos 

### Objetivo 1:
Concexión ssh con instancia EC2:
<img width="1170" height="533" alt="image" src="https://github.com/user-attachments/assets/dc0b33fc-9fbc-430b-bf85-f2a76a4f9935" />

docker-compose.yml:
<img width="1606" height="735" alt="image" src="https://github.com/user-attachments/assets/681d1f65-05f8-4f18-894e-325ee0c09044" />

<img width="1364" height="450" alt="image" src="https://github.com/user-attachments/assets/8bd8eb41-ebe8-4d28-acff-57b2f54fa5b7" />

.env y DockerFile

<img width="1160" height="416" alt="image" src="https://github.com/user-attachments/assets/ae46b928-c0ab-4fd6-ab23-72e30b37cfa8" />

<img width="1560" height="490" alt="image" src="https://github.com/user-attachments/assets/17c0c853-ae53-447c-bf14-3fbbf017f0e2" />

curl al  servidor:

<img width="1615" height="663" alt="image" src="https://github.com/user-attachments/assets/a41d2ae5-45a5-4b6a-8db7-c4da2faec7f2" />

<img width="1169" height="483" alt="image" src="https://github.com/user-attachments/assets/ffdbbce3-82c1-4151-a0e0-9f525a7aff25" />

<img width="1171" height="308" alt="image" src="https://github.com/user-attachments/assets/726241bd-095d-4623-ba61-7ec9367b7fc1" />

Conexión con instancia DB:

<img width="1159" height="95" alt="image" src="https://github.com/user-attachments/assets/e1f12e85-15db-4b75-bfb2-266e62042172" />

Headers de seguridad:

<img width="1165" height="167" alt="image" src="https://github.com/user-attachments/assets/740d001f-e002-4524-9083-ae7a139a8e67" />

Encrypt y renovación automática:

<img width="1426" height="641" alt="image" src="https://github.com/user-attachments/assets/868b10c0-f9fb-41a5-b443-5995b981e428" />

Dominio y certificado SSl

<img width="1919" height="1072" alt="image" src="https://github.com/user-attachments/assets/9069830f-cc5b-427f-81ad-c3a29d0d6373" />

<img width="1163" height="296" alt="image" src="https://github.com/user-attachments/assets/ccef4225-6d37-4743-96b8-3e66a237b17b" />

### Objetivos 3 y 4:
<img width="2520" height="1287" alt="image" src="https://github.com/user-attachments/assets/e07f905d-d4f2-4065-aa2b-c51bc1788c1b" />
<img width="2508" height="1296" alt="image" src="https://github.com/user-attachments/assets/95079596-0e4d-4a4a-99ce-f94f5f492839" />




