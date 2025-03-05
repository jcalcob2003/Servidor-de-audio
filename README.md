## **Instalación y Configuración Manual de Icecast2**

1. **Instalar Icecast2**:
   ```bash
   sudo apt update
   sudo apt install icecast2
   ```
   - Durante la instalación, selecciona "No" cuando te pregunte si deseas establecer una clave de administración.

2. **Configurar Icecast2**:
   Edita el archivo de configuración:
   ```bash
   sudo nano /etc/icecast2/icecast.xml
   ```
   Modifica las siguientes secciones:
   ```xml
   <source-password>javi</source-password>
   <admin-user>javi</admin-user>
   <admin-password>javi</admin-password>
   ```
   - **`<source-password>`**: Contraseña que el software fuente (Ices2) debe proporcionar para autenticarse con el servidor Icecast.
   - **`<admin-user>`** y **`<admin-password>`**: Credenciales para acceder a la interfaz de administración web de Icecast.

3. **Habilitar y iniciar Icecast2**:
   Edita el archivo `/etc/default/icecast2` y establece `ENABLE=true`:
   ```bash
   sudo nano /etc/default/icecast2
   ```
   Inicia y habilita el servicio:
   ```bash
   sudo systemctl start icecast2
   sudo systemctl enable icecast2
   ```

---

## **Instalación y Configuración Manual de Ices2**

1. **Instalar Ices2 y Vorbis Tools**:
   ```bash
   sudo apt install ices2 vorbis-tools
   ```

2. **Configurar Ices2**:
   Crea un directorio para la configuración de Ices2:
   ```bash
   sudo mkdir /etc/ices2
   ```
   Copia el archivo de configuración de ejemplo:
   ```bash
   sudo cp /usr/share/doc/ices2/examples/ices-playlist.xml /etc/ices2/
   ```
   Edita el archivo de configuración:
   ```bash
   sudo nano /etc/ices2/ices-playlist.xml
   ```
   Modifica las siguientes secciones:
   ```xml
   <name>Javi</name>
   <genre>Pop</genre>
   <description>Javi</description>
   <param name="type">basic</param>
   <param name="file">/home/vagrant/canciones/lista.txt</param>
   <password>javi</password>
   <mount>/audios</mount>
   ```
   - **`<name>`**, **`<genre>`**, **`<description>`**: Metadatos descriptivos de la estación de radio.
   - **`<param name="type">basic</param>`**: Indica que la lista de reproducción se proporcionará a través de un archivo.
   - **`<param name="file">`**: Ruta del archivo que contiene la lista de reproducción.
   - **`<password>`**: Contraseña que Ices2 proporcionará a Icecast para autenticarse (debe coincidir con `<source-password>` en `icecast.xml`).
   - **`<mount>`**: Punto de montaje de la lista de reproducción en el servidor Icecast.

---

## **Preparación Manual de Archivos de Audio**

1. **Descargar archivos de audio**:
   - Descarga archivos `.mp3`.

2. **Convertir archivos a formato `.ogg`**:
   - Convierte los archivos `.mp3` a `.ogg` usando `ffmpeg`:
     ```bash
     sudo apt install ffmpeg
     ffmpeg -i archivo1.mp3 -acodec libvorbis archivo1.ogg
     ffmpeg -i archivo2.mp3 -acodec libvorbis archivo2.ogg
     ```

3. **Copiar archivos a la máquina virtual**:
   - Copia los archivos `.ogg` al directorio de la máquina virtual:
     ```bash
     cp /vagrant/archivo1.ogg /home/vagrant/canciones/
     cp /vagrant/archivo2.ogg /home/vagrant/canciones/
     ```

4. **Generar la lista de reproducción**:
   - Usa el comando `find` para generar la lista de reproducción:
     ```bash
     find /home/vagrant/canciones -name "*.ogg" > /home/vagrant/canciones/lista.txt
     ```

---

## **Automatización Manual del Servicio Ices2** 

1. **Crear un servicio systemd para Ices2**:
   Crea un archivo de servicio:
   ```bash
   sudo nano /etc/systemd/system/ices2.service
   ```
   Añade el siguiente contenido:
   ```ini
   [Unit]
   Description=ices2 daemon
   After=icecast2.service

   [Service]
   User=www-data
   Group=www-data
   WorkingDirectory=/etc/ices2
   ExecStart=ices2 "/etc/ices2/ices-playlist.xml"

   [Install]
   WantedBy=multi-user.target
   ```
   - **`[Unit]`**: Define la unidad y sus dependencias.
   - **`After=icecast2.service`**: Asegura que Ices2 se inicie después de Icecast2.
   - **`[Service]`**: Configura el servicio, incluyendo el usuario, grupo y comando de inicio.
   - **`[Install]`**: Define cómo se habilita el servicio.

2. **Habilitar y iniciar Ices2**:
   ```bash
   sudo systemctl daemon-reload
   sudo systemctl start ices2.service
   sudo systemctl enable ices2.service
   ```

---

## **Prueba del Servidor de Streaming**

1. **Acceder a la interfaz web de Icecast**:
   - En tu máquina real, abre un navegador web y accede a `http://localhost:8000`.
   - Haz clic en "Administration" e ingresa con las credenciales (`javi` / `javi`).

2. **Reproducir la lista de reproducción**:
   - Usa **VLC Media Player** y abre la URL `http://localhost:8000/audios` para reproducir la lista de reproducción.

---
