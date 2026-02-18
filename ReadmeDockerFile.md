# ================================
# Imagen base
# ================================
# Define la imagen base del contenedor.
# En este caso se usa Python 3.10 ya instalado sobre Linux,
# lo que evita tener que instalar Python manualmente.
FROM python:3.10


# ================================
# Creación de usuario no root
# ================================
# Ejecuta un comando dentro del contenedor durante el build.
# Se crea un usuario llamado "api-user" sin contraseña
# para ejecutar la aplicación de forma más segura (evita usar root).
RUN adduser --disabled-password --gecos '' api-user


# ================================
# Directorio de trabajo
# ================================
# Establece el directorio de trabajo dentro del contenedor.
# Todos los comandos posteriores (RUN, CMD, etc.)
# se ejecutarán desde esta ruta.
WORKDIR /opt/bankchurn-api


# ================================
# Copia del código fuente
# ================================
# Copia el contenido local de ./bankchurn-api
# al directorio /opt/bankchurn-api dentro del contenedor.
ADD ./bankchurn-api /opt/bankchurn-api/


# ================================
# Instalación de dependencias
# ================================
# Actualiza pip a la última versión disponible
# para evitar problemas de compatibilidad.
RUN pip install --upgrade pip

# Instala las dependencias de Python definidas en requirements.txt
# necesarias para que la aplicación funcione.
RUN pip install -r /opt/bankchurn-api/requirements.txt


# ================================
# Permisos de ejecución
# ================================
# Da permisos de ejecución al script run.sh
# para que pueda ejecutarse como comando al iniciar el contenedor.
RUN chmod +x /opt/bankchurn-api/run.sh

# Cambia el propietario de todos los archivos del proyecto
# al usuario "api-user" para evitar problemas de permisos
# cuando el contenedor se ejecute con ese usuario.
RUN chown -R api-user:api-user ./


# ================================
# Usuario por defecto
# ================================
# Define que a partir de este punto
# el contenedor se ejecutará como "api-user"
# y no como root (buena práctica de seguridad).
USER api-user


# ================================
# Puerto expuesto
# ================================
# Indica que el contenedor escucha en el puerto 8001.
# No abre el puerto automáticamente, solo lo documenta.
EXPOSE 8001


# ================================
# Comando de inicio
# ================================
# Define el comando que se ejecuta cuando el contenedor arranca.
# En este caso ejecuta el script run.sh usando bash.
CMD ["bash", "./run.sh"]
