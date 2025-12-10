# COMANDOS UTILIZADOS EN Los Laboratorios del Modulo IX

----------

# PRÁCTICA 1: INSTALACIÓN Y ADMINISTRACIÓN CON WEBMIN EN RHEL 8

## 0. ESCENARIO

### SINTAXIS

```
Sistema: Red Hat Enterprise Linux 8.x con interfaz gráfica
Hostname: <NOMBRE_HOST>.<DOMINIO>
IP del servidor: <IP_SERVIDOR>
Gateway: <IP_GATEWAY>

```

### EJEMPLO

```
Sistema: Red Hat Enterprise Linux 8.x con interfaz gráfica
Hostname: servidor-luis.samba.lab
IP del servidor: 192.168.1.200
Gateway: 192.168.1.1

```

----------

## 1. INSTALACIÓN DE WEBMIN

### 1.1 Crear el repositorio de Webmin

#### SINTAXIS

```bash
sudo -i
nano /etc/yum.repos.d/webmin.repo

```

Contenido del archivo:

```ini
[Webmin]
name=Webmin Distribution Neutral
mirrorlist=https://download.webmin.com/download/yum/mirrorlist
enabled=1
gpgcheck=1
gpgkey=https://download.webmin.com/jcameron-key.asc

```

Guardar: `Ctrl+O, Enter, Ctrl+X`

----------

### 1.2 Importar la llave GPG e instalar

```bash
wget https://download.webmin.com/jcameron-key.asc
rpm --import jcameron-key.asc
dnf install webmin -y

```

----------

### 1.3 Habilitar el servicio

```bash
systemctl enable --now webmin
systemctl status webmin

```

----------

### 1.4 Abrir el puerto en el firewall

```bash
firewall-cmd --permanent --add-port=10000/tcp
firewall-cmd --reload

```

----------

### 1.5 Instalar paquete de compatibilidad (IMPORTANTE)

```bash
dnf install NetworkManager-initscripts-updown -y

```

**Nota:** Este paquete es necesario para que Webmin pueda activar interfaces de red en RHEL 8.

----------

## 2. ACCESO A WEBMIN

### SINTAXIS

```
1. Abrir navegador
2. Ir a: https://<IP_SERVIDOR>:10000
3. Aceptar aviso del certificado autofirmado
4. Iniciar sesión con usuario root y su contraseña

```

### EJEMPLO

```
1. Abrir navegador
2. Ir a: https://192.168.1.200:10000
3. Aceptar aviso del certificado autofirmado
4. Iniciar sesión con usuario root y su contraseña

```

----------

## 3. ADMINISTRACIÓN DE USUARIOS

**Módulo:** `System → Users and Groups`

### 3.1 Crear un usuario

### SINTAXIS

```
1. Ir a: System → Users and Groups
2. Clic en: Create a new user
3. Configurar:
   - Username: <NOMBRE_USUARIO>
   - Real name: <NOMBRE_REAL>
   - Shell: /bin/bash
   - Password: Seleccionar "Normal password" y escribir <CONTRASEÑA>
4. Clic en: Create

```

### EJEMPLO

```
1. Ir a: System → Users and Groups
2. Clic en: Create a new user
3. Configurar:
   - Username: usuario_prueba
   - Real name: Usuario de Prueba
   - Shell: /bin/bash
   - Password: Seleccionar "Normal password" y escribir: Pass123!
4. Clic en: Create

```

----------

### 3.2 Eliminar el usuario

### SINTAXIS

```
1. En la lista de usuarios, marcar la casilla del usuario: <NOMBRE_USUARIO>
2. Clic en: Delete Selected Users
3. Confirmar la eliminación

```

### EJEMPLO

```
1. En la lista de usuarios, marcar la casilla del usuario: usuario_prueba
2. Clic en: Delete Selected Users
3. Confirmar la eliminación

```

----------

## 4. GESTIÓN DE SERVICIOS (SSH)

**Módulo:** `System → Bootup and Shutdown`

```
1. Ir a: System → Bootup and Shutdown
2. Buscar: sshd en la lista
3. Usar botones de acción:
   - Stop → Detiene el servicio
   - Start → Inicia el servicio
   - Restart → Reinicia el servicio

```

----------

## 5. CONFIGURACIÓN DE RED: IP ESTÁTICA

**Módulo:** `Networking → Network Configuration`

### 5.1 Verificar que existe el perfil de conexión

#### SINTAXIS

```bash
# Si al intentar activar la interfaz aparece el error "No suitable profile found"
sudo nmcli connection add type ethernet ifname <NOMBRE_INTERFAZ> con-name <NOMBRE_INTERFAZ>

```

#### EJEMPLO

```bash
# Si al intentar activar la interfaz aparece el error "No suitable profile found"
sudo nmcli connection add type ethernet ifname ens160 con-name ens160

```

----------

### 5.2 Configurar la IP estática

#### SINTAXIS

```
1. Ir a: Networking → Network Configuration
2. Clic en: Network Interfaces
3. Ir a la pestaña: Activated at Boot
4. Clic en el nombre de tu interfaz: <NOMBRE_INTERFAZ>
5. Configurar:
   - Activate at boot: Yes
   - IPv4 address: Seleccionar "Static configuration"
   - IPv4 address: <IP_DESEADA>
   - Netmask: <MASCARA_RED>
6. Clic en: Save

```

#### EJEMPLO

```
1. Ir a: Networking → Network Configuration
2. Clic en: Network Interfaces
3. Ir a la pestaña: Activated at Boot
4. Clic en el nombre de tu interfaz: ens160
5. Configurar:
   - Activate at boot: Yes
   - IPv4 address: Seleccionar "Static configuration"
   - IPv4 address: 192.168.1.200
   - Netmask: 255.255.255.0
6. Clic en: Save

```

----------

### 5.3 Aplicar los cambios de la interfaz

#### SINTAXIS

```
1. Regresar a: Network Interfaces
2. Marcar la casilla de tu interfaz: <NOMBRE_INTERFAZ>
3. Clic en: Apply Selected Interfaces (botón azul)

⚠️ IMPORTANTE: La conexión se perderá momentáneamente. 
   Acceder con la nueva IP: https://<IP_NUEVA>:10000

```

#### EJEMPLO

```
1. Regresar a: Network Interfaces
2. Marcar la casilla de tu interfaz: ens160
3. Clic en: Apply Selected Interfaces (botón azul)

⚠️ IMPORTANTE: La conexión se perderá momentáneamente. 
   Acceder con la nueva IP: https://192.168.1.200:10000

```

----------

### 5.4 Configurar el Gateway

#### SINTAXIS

```
1. Acceder a Webmin con la nueva IP: https://<IP_NUEVA>:10000
2. Ir a: Networking → Network Configuration
3. Clic en: Routing and Gateways
4. Ir a la pestaña: Active configuration
5. En Create active route:
   - Route destination: Default route
   - Netmask for destination: Default (NO pongas 255.255.255.255)
   - Route via: Seleccionar "Gateway" y escribir: <IP_GATEWAY>
6. Clic en: Create

```

#### EJEMPLO

```
1. Acceder a Webmin con la nueva IP: https://192.168.1.200:10000
2. Ir a: Networking → Network Configuration
3. Clic en: Routing and Gateways
4. Ir a la pestaña: Active configuration
5. En Create active route:
   - Route destination: Default route
   - Netmask for destination: Default (NO pongas 255.255.255.255)
   - Route via: Seleccionar "Gateway" y escribir: 192.168.1.1
6. Clic en: Create

```

----------

### 5.5 Verificar conectividad

```bash
ping -c 4 8.8.8.8

```

Si responde, tienes internet configurado correctamente.

⚠️ **ADVERTENCIA:** NO uses el botón "Apply Configuration" de la página principal de Network Configuration. Este botón puede reiniciar toda la configuración y perder los cambios. Solo usa "Apply Selected Interfaces" desde la lista de interfaces.

----------

## 6. ADMINISTRACIÓN DE SOFTWARE

### 6.1 Actualizar paquetes

**Módulo:** `System → Software Package Updates`

```
1. Ir a: System → Software Package Updates
2. Clic en: Refresh Available Packages
3. Si hay actualizaciones, aparecerán seleccionadas automáticamente
4. Clic en: Update Selected Packages
5. Esperar a que termine
6. Clic en: Return to package list

```

----------

### 6.2 Verificar si un paquete está instalado (antes de instalar)

**Módulo:** `System → Software Packages`

#### SINTAXIS

```
1. Ir a: System → Software Packages
2. En la pestaña: Installed packages
3. En Search For Package escribir el nombre: <NOMBRE_PAQUETE>
4. Si no aparece ningún resultado, no está instalado

```

#### EJEMPLO

```
1. Ir a: System → Software Packages
2. En la pestaña: Installed packages
3. En Search For Package escribir el nombre: wireshark
4. Si no aparece ningún resultado, no está instalado

```

----------

### 6.3 Instalar un paquete

#### SINTAXIS

```
1. Ir a: System → Software Packages
2. Clic en la pestaña: Install a new package
3. Seleccionar: Package from YUM
4. Escribir el nombre del paquete: <NOMBRE_PAQUETE>
5. Clic en: Install

Nota: Si aparece error de repositorio (como docker-ce-stable), 
ir a Software Package Updates → Package Repositories, 
marcar el repositorio problemático y hacer clic en Disable Repositories. 
Luego intentar instalar de nuevo.

```

#### EJEMPLO

```
1. Ir a: System → Software Packages
2. Clic en la pestaña: Install a new package
3. Seleccionar: Package from YUM
4. Escribir el nombre del paquete: wireshark
5. Clic en: Install

Nota: Si aparece error de repositorio (como docker-ce-stable), 
ir a Software Package Updates → Package Repositories, 
marcar el repositorio problemático y hacer clic en Disable Repositories. 
Luego intentar instalar de nuevo.

```

----------

### 6.4 Verificar la instalación

#### SINTAXIS

```
1. Ir a: Software Packages → Installed packages
2. En Search For Package escribir: <NOMBRE_PAQUETE>
3. Clic en el paquete para ver los detalles
4. Deberías ver: versión, fecha de instalación, descripción, etc.

```

#### EJEMPLO

```
1. Ir a: Software Packages → Installed packages
2. En Search For Package escribir: wireshark
3. Clic en el paquete para ver los detalles
4. Deberías ver: versión, fecha de instalación, descripción, etc.

```

----------

### 6.5 Eliminar un paquete

#### SINTAXIS

```
1. Ir a: Software Packages → Installed packages
2. Buscar: <NOMBRE_PAQUETE>
3. Marcar la casilla del paquete
4. Clic en: Uninstall selected packages
5. Confirmar la eliminación
6. Verificar buscando de nuevo <NOMBRE_PAQUETE>
   Debe mostrar: "0 packages matched <NOMBRE_PAQUETE>"

```

#### EJEMPLO

```
1. Ir a: Software Packages → Installed packages
2. Buscar: wireshark
3. Marcar la casilla del paquete
4. Clic en: Uninstall selected packages
5. Confirmar la eliminación
6. Verificar buscando de nuevo wireshark
   Debe mostrar: "0 packages matched wireshark"

```

----------

## 7. GESTIÓN DE ARCHIVOS Y DIRECTORIOS

**Módulo:** `Others → File Manager`

### 7.1 Explorar directorios

```
1. Ir a: TOOLS → File Manager
2. En el panel izquierdo navegar por el árbol de directorios
3. Clic en /tmp para trabajar ahí

```

----------

### 7.2 Crear y editar un archivo

#### SINTAXIS

```
1. Dentro de /tmp, hacer clic derecho en un espacio vacío
2. Seleccionar: File → Create
3. Escribir el nombre: <NOMBRE_ARCHIVO>.txt
4. Hacer doble clic en el archivo para abrirlo
5. Escribir texto
6. Guardar y cerrar

```

#### EJEMPLO

```
1. Dentro de /tmp, hacer clic derecho en un espacio vacío
2. Seleccionar: File → Create
3. Escribir el nombre: Prueba_webmin.txt
4. Hacer doble clic en el archivo para abrirlo
5. Escribir: "Este es un archivo de prueba para Webmin"
6. Guardar y cerrar

```

----------

### 7.3 Eliminar el archivo

```
1. Seleccionar el archivo (marcar la casilla)
2. Hacer clic derecho → Delete
3. Confirmar la eliminación

```

----------

## 8. MONITORIZACIÓN DEL SISTEMA

### 8.1 CPU y Memoria (Procesos)

**Módulo:** `System → Running Processes`

```
1. Ir a: System → Running Processes
2. Usar las pestañas:
   - PID → Lista de procesos por ID
   - Memory → Procesos ordenados por uso de memoria
   - CPU → Procesos ordenados por uso de CPU

```

----------

### 8.2 Almacenamiento (Discos)

**Módulo:** `System → Disk and Network Filesystems`

```
1. Ir a: System → Disk and Network Filesystems
2. Ver tabla con:
   - Punto de montaje (/, /boot, etc.)
   - Tipo de sistema de archivos (xfs, tmpfs)
   - Porcentaje de uso

```

----------

### 8.3 Resumen general (Dashboard)

```
1. Hacer clic en: Dashboard o System Information en el menú
2. Ver medidores circulares de:
   - CPU
   - Memoria Real
   - Memoria Virtual (swap)
   - Espacio en disco
3. También muestra:
   - Hostname
   - Versión de Webmin
   - Sistema operativo
   - Tiempo encendido (uptime)
   - Número de procesos

```

----------

## RESUMEN DE LA PRÁCTICA

Tarea

Módulo

Estado

Instalar Webmin

Terminal

✓

Crear/eliminar usuario

System → Users and Groups

✓

Gestionar servicios SSH

System → Bootup and Shutdown

✓

Configurar IP estática

Network Configuration → Network Interfaces

✓

Configurar Gateway

Network Configuration → Routing and Gateways

✓

Actualizar paquetes

System → Software Package Updates

✓

Instalar paquete

System → Software Packages

✓

Eliminar paquete

System → Software Packages → Uninstall

✓

Gestionar archivos

Others → File Manager

✓

Monitorizar sistema

Running Processes, Disk Filesystems, Dashboard

✓

----------

----------

# PRÁCTICA 2: DESPLIEGUE DE VM CON TERRAFORM EN DIGITALOCEAN

## GUION PARA VIDEO

### [INTRO]

Hola, en este video vamos a crear una máquina virtual en DigitalOcean usando Terraform. Todo de forma automática, sin hacer clic en nada manualmente. Vamos allá.

----------

### [PASO 1 - TOKEN]

Lo primero que necesitamos es un token de DigitalOcean. Este token es como una llave de acceso para que Terraform pueda crear cosas en nuestra cuenta.

```
1. Entrar a: cloud.digitalocean.com/account/api/tokens
2. Dar clic en: "Generate New Token"
3. Nombre del token: <NOMBRE_TOKEN>
4. Seleccionar: "Full Access" para darle todos los permisos
5. Generar el token

```

**Importante:** Copiar ese token y guardarlo bien porque solo se muestra una vez.

#### EJEMPLO

```
1. Entrar a: cloud.digitalocean.com/account/api/tokens
2. Dar clic en: "Generate New Token"
3. Nombre del token: terraform-token
4. Seleccionar: "Full Access" para darle todos los permisos
5. Generar el token

```

----------

### [PASO 2 - SSH KEY]

Ahora necesitamos la llave SSH. Esto es para poder conectarnos a la máquina después de crearla. Desde tu servidor Red Hat escribes:

```bash
sudo nano ~/.ssh/id_rsa.pub

```

Copiar todo lo que aparece ahí.

Si ya tienes una llave subida de otra práctica, puedes usar esa misma. Solo necesitas el fingerprint que aparece en DigitalOcean.

Si no la tienes:

```
1. Ir a: cloud.digitalocean.com/account/security?i=e12c3b
2. Dar clic en: "Add SSH Key"
3. Pegar la llave que copiaste
4. Guardar
5. Copiar el fingerprint que aparece (números separados por dos puntos)

```

Lo vas a necesitar más adelante.

----------

### [PASO 3 - INSTALAR TERRAFORM]

Ahora instalamos Terraform en Red Hat. Terraform es la herramienta que usaremos para crear la máquina de forma automática. Ejecutar estos comandos:

```bash
sudo dnf install -y yum-utils
sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo
sudo dnf install -y terraform

```

Verificar que se instaló:

```bash
terraform -version

```

----------

### [PASO 4 - CREAR CARPETA Y ARCHIVO]

Crear una carpeta para trabajar:

```bash
mkdir ~/terraform-digitalocean
cd ~/terraform-digitalocean

```

Ahora crear el archivo principal donde va toda la configuración:

```bash
sudo nano main.tf

```

Aquí escribimos la configuración completa. Esta primera parte le dice a Terraform que vamos a trabajar con DigitalOcean, que use el token para conectarse a nuestra cuenta, y declara una variable para guardar ese token:

```hcl
terraform {
  required_providers {
    digitalocean = {
      source = "digitalocean/digitalocean"
      version = "~> 2.0"
    }
  }
}

provider "digitalocean" {
  token = var.do_token
}

variable "do_token" {}

```

Guardar y salir: `Ctrl+O, Enter, Ctrl+X`

----------

### [PASO 5 - ARCHIVO DEL TOKEN]

Crear otro archivo para poner el token de forma segura:

```bash
sudo nano terraform.tfvars

```

#### SINTAXIS

```hcl
do_token = "<TU_TOKEN_AQUI>"

```

#### EJEMPLO

```hcl
do_token = "dop_v1_1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcd"

```

Reemplazar con tu token real. Guardar y salir: `Ctrl+O, Enter, Ctrl+X`

----------

### [PASO 6 - INICIALIZAR]

Ahora inicializamos Terraform:

```bash
sudo terraform init

```

Esto descarga los componentes necesarios para trabajar con DigitalOcean.

----------

### [PASO 7 - CONFIGURAR LA VM]

Volver al archivo principal para decirle qué máquina queremos crear:

```bash
sudo nano main.tf

```

Al final del archivo, agregar la configuración del droplet. Un droplet es como DigitalOcean llama a sus máquinas virtuales. Aquí le decimos qué sistema operativo usar, el nombre de la máquina, la ubicación, el tamaño, y qué llave SSH usar para conectarnos:

#### SINTAXIS

```hcl
resource "digitalocean_droplet" "<NOMBRE_RECURSO>" {
  image = "<IMAGEN_SO>"
  name = "<NOMBRE_VM>"
  region = "<REGION>"
  size = "<TAMAÑO>"
  ssh_keys = ["<FINGERPRINT_SSH>"]
}

```

#### EJEMPLO

```hcl
resource "digitalocean_droplet" "os3vm" {
  image = "ubuntu-22-04-x64"
  name = "OS3vm"
  region = "nyc1"
  size = "s-1vcpu-1gb"
  ssh_keys = ["a1:b2:c3:d4:e5:f6:g7:h8:i9:j0:k1:l2:m3:n4:o5:p6"]
}

```

**Importante:** Verificar que al final de este bloque esté la llave de cierre `}`. Si no está, agregarla. Sin esa llave va a dar error.

Guardar y salir: `Ctrl+O, Enter, Ctrl+X`

----------

### [PASO 8 - VALIDAR]

Antes de crear nada, validar que no haya errores en lo que escribimos:

```bash
sudo terraform validate

```

Si sale "Success", todo está correcto. Si sale un error que dice "Unclosed configuration block", es que falta la llave de cierre `}` al final del bloque del droplet. Volver a editar el archivo y agregarla.

----------

### [PASO 9 - CREAR LA VM]

Ahora sí, le decimos a Terraform que cree la máquina:

```bash
sudo terraform apply

```

Terraform te muestra qué va a crear y te pregunta si estás seguro. Escribir "yes" y Enter.

Terraform empieza a crear la máquina automáticamente en DigitalOcean. Esperar unos segundos y listo.

----------

### [PASO 10 - VERIFICAR]

Para verificar que todo salió bien, puedes entrar por SSH usando la IP que te dio:

#### SINTAXIS

```bash
ssh root@<IP_GENERADA>

```

#### EJEMPLO

```bash
ssh root@161.35.110.141

```

También puedes entrar al portal de DigitalOcean y ver tu máquina creada ahí mismo.

----------

### [CIERRE]

Y listo, así de fácil creamos una máquina virtual usando código. Ya no necesitas hacer clic en el portal, todo lo haces desde la terminal con Terraform. Esto es infraestructura como código.

Nos vemos en el siguiente video.

----------

## ARCHIVO MAIN.TF COMPLETO

### SINTAXIS

```hcl
terraform {
  required_providers {
    digitalocean = {
      source = "digitalocean/digitalocean"
      version = "~> 2.0"
    }
  }
}

provider "digitalocean" {
  token = var.do_token
}

variable "do_token" {}

resource "digitalocean_droplet" "<NOMBRE_RECURSO>" {
  image = "<IMAGEN_SO>"
  name = "<NOMBRE_VM>"
  region = "<REGION>"
  size = "<TAMAÑO>"
  ssh_keys = ["<FINGERPRINT_SSH>"]
}

```

### EJEMPLO

```hcl
terraform {
  required_providers {
    digitalocean = {
      source = "digitalocean/digitalocean"
      version = "~> 2.0"
    }
  }
}

provider "digitalocean" {
  token = var.do_token
}

variable "do_token" {}

resource "digitalocean_droplet" "os3vm" {
  image = "ubuntu-22-04-x64"
  name = "OS3vm"
  region = "nyc1"
  size = "s-1vcpu-1gb"
  ssh_keys = ["a1:b2:c3:d4:e5:f6:g7:h8:i9:j0:k1:l2:m3:n4:o5:p6"]
}

```

----------

----------

# PRÁCTICA 3: INSTALACIÓN Y CONFIGURACIÓN DE ANSIBLE

## REQUISITOS PREVIOS

```
- VM nueva en DigitalOcean con Rocky Linux 9 (nombre: AnsibleClient)
- VM Windows 10 ya configurada con red en Bridge
- Servidor RHEL restaurado desde snapshot

```

----------

## PASO 1: INSTALAR ANSIBLE EN RHEL

```bash
# Instalar EPEL
sudo dnf install epel-release -y

# Instalar Ansible
sudo dnf install ansible -y

# Instalar pip para Python 3.12
sudo dnf install python3.12-pip -y

# Instalar dependencias para compilar
sudo dnf install gcc python3-devel libffi-devel openssl-devel rust cargo -y

# Instalar setuptools-rust
sudo pip3 install setuptools-rust

# Instalar pywinrm en Python 3.12
sudo python3.12 -m pip install pywinrm

# Verificar instalación
ansible --version

```

----------

## PASO 2: CONFIGURAR NODO LINUX (DIGITALOCEAN - ROCKY LINUX)

Anotar la IP de tu VM nueva en DigitalOcean.

### SINTAXIS

Desde tu servidor RHEL:

```bash
# Conectarte como root
ssh root@<IP_DIGITALOCEAN>
# Si pide confirmación, escribe: yes

```

Dentro de la VM DigitalOcean:

```bash
# Crear usuario ansible
useradd -m ansible

# Crear contraseña
passwd ansible
# Escribe una contraseña

# Dar permisos sudo
usermod -aG wheel ansible

# Permitir sudo sin contraseña
echo 'ansible ALL=(ALL) NOPASSWD:ALL' | tee /etc/sudoers.d/ansible

# Habilitar autenticación por contraseña en SSH
vi /etc/ssh/sshd_config
# Buscar: PasswordAuthentication no
# Cambiarlo a: PasswordAuthentication yes
# Presionar: i (editar), cambiar, Esc, :wq (guardar y salir)

# Reiniciar SSH
systemctl restart sshd

# Salir
exit

```

De vuelta en tu servidor RHEL:

```bash
# Copiar llave SSH al usuario ansible
ssh-copy-id ansible@<IP_DIGITALOCEAN>
# Te pedirá la contraseña del usuario ansible

# Probar conexión sin contraseña
ssh ansible@<IP_DIGITALOCEAN>
# Si entras sin pedir contraseña, está perfecto
exit

```

### EJEMPLO

Desde tu servidor RHEL:

```bash
# Conectarte como root
ssh root@161.35.110.141
# Si pide confirmación, escribe: yes

```

Dentro de la VM DigitalOcean:

```bash
# Crear usuario ansible
useradd -m ansible

# Crear contraseña
passwd ansible
# Escribe: ansible123

# Dar permisos sudo
usermod -aG wheel ansible

# Permitir sudo sin contraseña
echo 'ansible ALL=(ALL) NOPASSWD:ALL' | tee /etc/sudoers.d/ansible

# Habilitar autenticación por contraseña en SSH
vi /etc/ssh/sshd_config
# Buscar: PasswordAuthentication no
# Cambiarlo a: PasswordAuthentication yes
# Presionar: i (editar), cambiar, Esc, :wq (guardar y salir)

# Reiniciar SSH
systemctl restart sshd

# Salir
exit

```

De vuelta en tu servidor RHEL:

```bash
# Copiar llave SSH al usuario ansible
ssh-copy-id ansible@161.35.110.141
# Te pedirá la contraseña del usuario ansible: ansible123

# Probar conexión sin contraseña
ssh ansible@161.35.110.141
# Si entras sin pedir contraseña, está perfecto
exit

```

----------

## PASO 3: CONFIGURAR NODO WINDOWS

En Windows PowerShell como Administrador:

Verificar la IP:

### SINTAXIS

```powershell
ipconfig
# Debe mostrar una IP en la red <TU_RED> (ejemplo: 192.168.1.x)

```

### EJEMPLO

```powershell
ipconfig
# Debe mostrar: 192.168.1.201

```

----------

Desactivar firewall PERMANENTEMENTE:

```powershell
Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False
Get-NetFirewallProfile | Select Name,Enabled
# Verifica que todos digan Enabled: False

```

----------

Cambiar perfil de red a Privado:

```powershell
Set-NetConnectionProfile -NetworkCategory Private

```

----------

Configurar WinRM:

```powershell
# Iniciar WinRM
winrm quickconfig
# Escribe: Y cuando pregunte

# Configurar autenticación
winrm set winrm/config/service/auth '@{Basic="true"}'
winrm set winrm/config/service '@{AllowUnencrypted="true"}'
winrm set winrm/config/listener?Address=*+Transport=HTTP '@{Port="5985"}'

# Configurar políticas
Set-Item -Path WSMan:\localhost\Service\AllowUnencrypted -Value $true
Set-Item -Path WSMan:\localhost\Service\Auth\Basic -Value $true

```

----------

Crear usuario ansible:

### SINTAXIS

```powershell
# Crear usuario con contraseña
$Password = ConvertTo-SecureString "<TU_CONTRASEÑA>" -AsPlainText -Force
New-LocalUser -Name "ansible" -Password $Password -Description "Ansible User" -PasswordNeverExpires

# Agregar a administradores (en español)
Add-LocalGroupMember -Group "Administradores" -Member "ansible"

# Add to administrators group (in English)
Add-LocalGroupMember -Group "Administrators" -Member "ansible"

# Verificar
Get-LocalUser -Name "ansible"

```

### EJEMPLO

```powershell
# Crear usuario con contraseña
$Password = ConvertTo-SecureString "LUISEDUARDO809" -AsPlainText -Force
New-LocalUser -Name "ansible" -Password $Password -Description "Ansible User" -PasswordNeverExpires

# Agregar a administradores (en español)
Add-LocalGroupMember -Group "Administradores" -Member "ansible"

# Add to administrators group (in English)
Add-LocalGroupMember -Group "Administrators" -Member "ansible"

# Verificar
Get-LocalUser -Name "ansible"

```

----------

## PASO 4: CREAR INVENTARIO DE ANSIBLE

En tu servidor RHEL:

```bash
sudo nano /etc/ansible/hosts

```

### SINTAXIS

Agregar este contenido:

```ini
[linux]
<NOMBRE_HOST_LINUX> ansible_host=<IP_DIGITALOCEAN> ansible_user=ansible ansible_ssh_private_key_file=<RUTA_LLAVE_SSH>

[win]
<NOMBRE_HOST_WINDOWS> ansible_host=<IP_WINDOWS> ansible_user=ansible ansible_password=<CONTRASEÑA_WINDOWS> ansible_connection=winrm ansible_winrm_transport=basic ansible_winrm_server_cert_validation=ignore ansible_port=5985

```

### EJEMPLO

```ini
[linux]
cliente1 ansible_host=161.35.110.141 ansible_user=ansible ansible_ssh_private_key_file=/home/2025-0826/.ssh/id_rsa

[win]
cliente2 ansible_host=192.168.1.201 ansible_user=ansible ansible_password=LUISEDUARDO809 ansible_connection=winrm ansible_winrm_transport=basic ansible_winrm_server_cert_validation=ignore ansible_port=5985

```

Guardar: `Ctrl+O, Enter` y salir: `Ctrl+X`

----------

## PASO 5: VERIFICAR CONECTIVIDAD DE RED

Antes de probar Ansible, verificar que haya conectividad:

### SINTAXIS

```bash
# Probar ping a Linux
ping -c 3 <IP_DIGITALOCEAN>

# Probar ping a Windows
ping -c 3 <IP_WINDOWS>

```

### EJEMPLO

```bash
# Probar ping a Linux
ping -c 3 161.35.110.141

# Probar ping a Windows
ping -c 3 192.168.1.201

```

Ambos deben responder. Si Windows no responde, el firewall sigue activo.

----------

## PASO 6: PROBAR CONEXIÓN CON ANSIBLE

```bash
# Probar Linux
ansible linux -m ping

# Probar Windows
ansible win -m win_ping

```

Resultado esperado:

```
cliente1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}

cliente2 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}

```

----------

----------

# PRÁCTICA 4: COMANDOS AD-HOC

## 🔄 TAREA 1: COPIAR ARCHIVO EN LA MÁQUINA WINDOWS (ANSIBLE2)

La tarea consiste en copiar un archivo que ya existe en el escritorio de la máquina Windows a su carpeta de Documentos. Si el archivo no existe, toca crear el archivo primero.

### Comando Ad-Hoc (Usando win_copy):

#### SINTAXIS

```bash
ansible win -m win_copy -a "src=<RUTA_ORIGEN> dest=<RUTA_DESTINO> remote_src=yes" -u ansible

```

#### EJEMPLO

```bash
ansible win -m win_copy -a "src=C:/Users/ansible.DESKTOP-M12EB03/Desktop/archivoluis.txt dest=C:/Users/ansible.DESKTOP-M12EB03/Documents/ remote_src=yes" -u ansible

```

### Tabla de Parámetros:

Parámetro

Valor

Función

win

Grupo del inventario

Dirige el comando al cliente Windows

-m win_copy

Módulo a usar

Copia archivos en el host remoto

src

Ruta de origen

La ubicación actual del archivo (escritorio)

dest

Ruta de destino

Donde se copiará el archivo (documentos)

remote_src=yes

Argumento

Indica que el archivo está en el host remoto

-u ansible

Usuario

Usuario con el que se ejecuta el comando

----------

## 💻 TAREA 2: REINICIAR LA VM LINUX (ANSIBLE1)

### Comando Ad-Hoc (Usando reboot):

```bash
ansible linux -m reboot -u ansible --become

```

Reinicia de forma segura la VM del grupo [linux]. El módulo reboot espera a que el host vuelva a estar en línea después del reinicio. Se usa --become para permisos de administrador (sudo).

### Tabla de Parámetros:

Parámetro

Función

linux

Grupo del inventario que se va a reiniciar

-m reboot

Módulo que gestiona el reinicio seguro

-u ansible

Usuario con el que se ejecuta el comando

--become

Escala privilegios a sudo/root

----------

----------

# PRÁCTICA 5: PLAYBOOKS DE ANSIBLE

## REQUISITOS PREVIOS

```
- Práctica 3 completada (inventario configurado y conexiones funcionando)
- VM Windows encendida (no suspendida)
- VM Linux en DigitalOcean activa

```

----------

## PASO 1: CREAR DIRECTORIO PARA PLAYBOOKS

En tu servidor RHEL:

```bash
# Crear directorio
mkdir -p ~/ansible-playbooks

# Entrar al directorio
cd ~/ansible-playbooks

```

----------

## PASO 2: CREAR PLAYBOOK PARA WINDOWS (NOTEPAD++)

```bash
nano install_notepad.yml

```

Agregar este contenido:

```yaml
---
- name: Instalar Notepad++ en Windows
  hosts: win
  tasks:
    - name: Descargar Notepad++
      win_get_url:
        url: https://github.com/notepad-plus-plus/notepad-plus-plus/releases/download/v8.6.9/npp.8.6.9.Installer.x64.exe
        dest: C:\Windows\Temp\notepad_installer.exe

    - name: Instalar Notepad++
      win_package:
        path: C:\Windows\Temp\notepad_installer.exe
        arguments: /S
        state: present

    - name: Verificar instalación
      win_stat:
        path: C:\Program Files\Notepad++\notepad++.exe
      register: notepad_installed

    - name: Mostrar resultado
      debug:
        msg: "Notepad++ instalado correctamente"
      when: notepad_installed.stat.exists

```

Guardar: `Ctrl+O, Enter` y salir: `Ctrl+X`

----------

## PASO 3: EJECUTAR PLAYBOOK DE WINDOWS

### SINTAXIS

Verificar que Windows esté encendida:

```bash
# Verificar conectividad
ping -c 2 <IP_WINDOWS>

```

Ejecutar playbook:

```bash
ansible-playbook install_notepad.yml

```

### EJEMPLO

Verificar que Windows esté encendida:

```bash
# Verificar conectividad
ping -c 2 192.168.1.201

```

Ejecutar playbook:

```bash
ansible-playbook install_notepad.yml

```

Resultado esperado:

```
PLAY RECAP *********************************
cliente2 : ok=4 changed=2 unreachable=0 failed=0

```

----------

## PASO 4: CREAR PLAYBOOK PARA LINUX (NGINX)

```bash
nano install_nginx.yml

```

Agregar este contenido:

```yaml
---
- name: Instalar y configurar NGINX en Linux
  hosts: linux
  become: yes
  tasks:
    - name: Instalar NGINX
      dnf:
        name: nginx
        state: present

    - name: Iniciar servicio NGINX
      systemd:
        name: nginx
        state: started
        enabled: yes

    - name: Verificar que NGINX está corriendo
      systemd:
        name: nginx
      register: nginx_status

    - name: Mostrar estado del servicio
      debug:
        msg: "NGINX está corriendo correctamente"
      when: nginx_status.status.ActiveState == "active"

    - name: Abrir puerto 80 en firewall
      firewalld:
        port: 80/tcp
        permanent: yes
        state: enabled
        immediate: yes
      ignore_errors: yes

```

Guardar: `Ctrl+O, Enter` y salir: `Ctrl+X`

----------

## PASO 5: EJECUTAR PLAYBOOK DE LINUX

```bash
ansible-playbook install_nginx.yml

```

Resultado esperado:

```
PLAY RECAP *********************************
cliente1 : ok=6 changed=2 unreachable=0 failed=0 ignored=1

```

El `ignored=1` es normal (es el error del firewall que se ignora).

----------

## PASO 6: VERIFICAR INSTALACIÓN DE NOTEPAD++ EN WINDOWS

En Windows PowerShell:

```powershell
# Verificar que existe el ejecutable
Test-Path "C:\Program Files\Notepad++\notepad++.exe"
# Debe devolver: True

# Abrir Notepad++
& "C:\Program Files\Notepad++\notepad++.exe"

```

O simplemente búscalo en el menú Inicio de Windows.

----------

## PASO 7: VERIFICAR INSTALACIÓN DE NGINX EN LINUX

### SINTAXIS

Desde tu servidor RHEL:

```bash
# Conectarte a DigitalOcean
ssh ansible@<IP_DIGITALOCEAN>

# Verificar estado del servicio
sudo systemctl status nginx
# Debe mostrar: active (running)

# Probar NGINX localmente
curl http://localhost
# Debe mostrar el HTML de la página de bienvenida

# Salir
exit

```

Verificar desde navegador:

```
Abrir navegador e ir a: http://<IP_DIGITALOCEAN>
Debe mostrar la página de bienvenida de NGINX

```

### EJEMPLO

Desde tu servidor RHEL:

```bash
# Conectarte a DigitalOcean
ssh ansible@161.35.110.141

# Verificar estado del servicio
sudo systemctl status nginx
# Debe mostrar: active (running)

# Probar NGINX localmente
curl http://localhost
# Debe mostrar el HTML de la página de bienvenida

# Salir
exit

```

Verificar desde navegador:

```
Abrir navegador e ir a: http://161.35.110.141
Debe mostrar la página de bienvenida de NGINX


