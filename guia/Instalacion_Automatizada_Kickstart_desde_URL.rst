==========================================================
Instalación Automatizada de Rocky Linux usando Kickstart desde URL
==========================================================

Esta guía explica cómo configurar una instalación desatendida de Rocky Linux cargando el archivo ``ks.cfg`` desde un servidor web (URL).

----------------------------
Requisitos Previos
----------------------------

- **ISO de Rocky Linux**: Imagen de instalación grabada en DVD/USB o servidor PXE.
- **Servidor web accesible**: Apache, Nginx o cualquier servicio que pueda alojar el archivo ``ks.cfg``.
- **Conectividad de red**: La máquina objetivo debe tener acceso a la URL donde está alojado ``ks.cfg``.

----------------------------------------
Paso 1: Crear y Publicar el Archivo Kickstart
----------------------------------------

1. **Crear el archivo ``ks.cfg``** (ejemplo básico):

   .. code-block:: bash

      # Rocky Linux 9 - Instalación Automatizada via URL
      install
      url --url="http://mirror.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os"
      lang es_VE.UTF-8
      keyboard us
      timezone America/Caracas --utc

      # Configuración de red (DHCP por defecto)
      network --bootproto=dhcp --device=eth0 --activate

      # Configuración de seguridad
      firewall --enabled --service=ssh
      selinux --enforcing

      # Usuario y contraseña (¡Cambiar en producción!)
      rootpw --plaintoctext rootpassword
      user --name=admin --password=admin123 --groups=wheel

      # Configuración de disco (LVM automático)
      zerombr
      clearpart --all --initlabel
      autopart --type=lvm

      # Gestor de arranque
      bootloader --location=mbr --boot-drive=sda

      # Paquetes a instalar
      %packages
      @^minimal
      @core
      vim
      wget
      %end

      # Script post-instalación
      %post
      dnf -y update
      echo "¡Instalación completada desde URL!" > /etc/motd
      %end

2. **Subir el archivo a un servidor web**:

   - Ejemplo con Python (servidor temporal):

    .. code-block:: bash
        python3 -m http.server 8000


   - O subir a un servicio como GitHub, Pastebin, o un hosting web.

----------------------------------------
Paso 2: Iniciar la Instalación desde Medios Físicos (DVD/USB)
----------------------------------------

1. **Arrancar desde el DVD/USB** de Rocky Linux.
2. **Editar la línea de arranque**:
   - Presiona la tecla ``Tab`` en el menú de instalación.
   - Añade el siguiente parámetro (ajusta la URL)::

        inst.ks=http://tuserver.com:8000/ks.cfg

   *Ejemplos de URLs válidas*:

   - ``http://192.168.1.100/ks.cfg``
   - ``https://raw.githubusercontent.com/usuario/repo/main/ks.cfg``

3. **Iniciar la instalación**: Presiona ``Enter`` y el sistema cargará automáticamente el archivo Kickstart desde la URL.

----------------------------------------
Paso 3: Configuraciones Adicionales (Opcional)
----------------------------------------

- **Kickstart con HTTPS**::

    inst.ks=https://dominio.com/ks.cfg

- **Ignorar verificación SSL** (si usas un certificado autofirmado)::

    inst.ks=https://dominio.com/ks.cfg inst.noverifyssl

- **Especificar interfaz de red manualmente**::

    inst.ks=http://url/ks.cfg ip=192.168.1.50::192.168.1.1:255.255.255.0:hostname:eth0:none

----------------------------------------
Solución de Problemas
----------------------------------------

- **Error 404**: Verifica que la URL sea accesible desde otra máquina con ``curl http://url/ks.cfg``.
- **Fallo en la descarga**: Usa un servidor HTTP simple (ej: ``python -m http.server``).
- **Logs de diagnóstico**: Revisa ``/var/log/anaconda/anaconda.log`` en caso de errores.

----------------------------------------
Referencias
----------------------------------------

- `Documentación oficial de Kickstart <https://docs.rockylinux.org/guides/kickstart/>`_
- `Red Hat Kickstart Reference <https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/9/html/performing_an_advanced_rhel_installation/kickstart-commands-and-options-reference_installing-rhel-as-an-experienced-user>`_

Notas clave:
----------------

Servidor web: Asegúrate de que el archivo ks.cfg tenga permisos de lectura pública.

Pruebas: Verifica la URL con curl o un navegador antes de usarla en la instalación.

Seguridad: Para entornos reales, usa contraseñas cifradas (openssl passwd -6) y HTTPS.
