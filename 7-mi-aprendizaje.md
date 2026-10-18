# COMPLETAR  
Comparando sus conocimientos antes de hacer la práctica con sus conocimientos después de hacer la tarea, explicar los principales aprendizajes logrados para beneficio de su formación profesional.  
Si solucionó un problema presentado al realizar la práctica también se debe documentar.

# Mi Aprendizaje
Conocía de forma teórica qué era Docker y para qué servía (crear contenedores), pero no sabía cómo limitar los recursos de un contenedor, cómo funcionaban los Healthchecks, ni cómo construir imágenes propias a partir de un archivo Dockerfile, tampoco tenía claro el concepto de políticas de reinicio ni cómo Docker maneja el caché para optimizar la construcción de imágenes.

**Después de la práctica**
Aprendí a limitar el uso de memoria RAM y CPU de un contenedor utilizando las banderas `--memory`, `--memory-swap`, `--cpus` y `--cpuset-cpus` 
Comprendí la importancia de esto para evitar que un solo contenedor consuma todos los recursos del host y afecte el rendimiento de los demás.
 Entendí cómo configurar verificaciones de salud (`--health-cmd`, `--health-interval`, `--health-retries`, etc.) para que Docker pueda determinar automáticamente si un contenedor está funcionando correctamente. 
Aprendí a escribir un Dockerfile desde cero, comprendiendo la función de cada instrucción (`FROM`, `RUN`, `COPY`, `EXPOSE`, `CMD`, entre otras),ademas entendí la diferencia entre `CMD` y `ENTRYPOINT`, y cómo se construye una imagen con `docker build`.
Al crear la versión 2.0 de la imagen después de modificar el archivo `index.html`, observé que Docker reutilizó las capas de la construcción anterior (mostrando `CACHED` en los pasos que no cambiaron). 
Aprendí a configurar el comportamiento de un contenedor ante fallos o reinicios del sistema usando `--restart always`, `unless-stopped`, `on-failure` y `no`. 
Comprendí qué son las imágenes huérfanas (dangling) y cómo identificarlas y eliminarlas usando `docker images -f "dangling=true"` y `docker image prune`.

**Problema presentado**

Durante la construcción de la imagen basada en `centos:7`, me encontré con un error que indicaba `Could not retrieve mirrorlist http://mirrorlist.centos.org`. Investigué y descubrí que **CentOS 7 llegó a su fin de vida (EOL) el 30 de junio de 2024**, por lo que los repositorios oficiales ya no están disponibles.

**Solución:** Modifiqué el Dockerfile para redirigir los repositorios hacia el archivo histórico (Vault) de CentOS, usando comandos `sed` antes de ejecutar `yum update`:

```dockerfile
RUN sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-* && \
    sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-*
