==========================================================
Instalación Automatizada de Rocky Linux desde DVD - Kickstart
==========================================================

Esta guía explica cómo crear y usar un archivo Kickstart (``ks.cfg``) para instalar Rocky Linux de manera personalizada desde un DVD.

----------------------------
Requisitos
----------------------------

- **DVD de Rocky Linux**: Imagen ISO grabada en un DVD o USB booteable.
- **Acceso a una máquina con Rocky Linux o RHEL**: Para editar el archivo Kickstart.
- **Editor de texto**: ``nano``, ``vim`` o cualquier editor preferido.

----------------------------------------
Paso 1: Crear el Archivo Kickstart (ks.cfg)
----------------------------------------

Crea un archivo llamado ``ks.cfg`` con el siguiente contenido básico (personalizable):

.. code-block:: bash

    # Rocky Linux 9 - Instalación Automatizada desde DVD

    # Configuración básica
    install
    cdrom
    lang es_VE.UTF-8
    keyboard us
    timezone America/Caracas --utc

    # Configuración de red (opcional, DHCP por defecto)
    network --bootproto=dhcp --device=eth0 --activate

    # Seguridad
    firewall --enabled --service=ssh
    selinux --enforcing

    # Configuración de usuarios
    rootpw --plaintext TuContraseñaSegura  # ¡Cambiar en producción!
    user --name=admin --password=admin123 --groups=wheel --gecos="Usuario Administrador"

    # Configuración de disco (LVM automático)
    zerombr
    clearpart --all --initlabel
    autopart --type=lvm

    # Gestor de arranque
    bootloader --location=mbr --boot-drive=sda

    # Selección de paquetes (mínimo + extras)
    %packages
    @^minimal
    @core
    vim-enhanced
    git
    wget
    %end

    # Script post-instalación (personalización adicional)
    %post
    # Actualizar sistema
    dnf -y update

    # Configurar sudo sin contraseña para wheel (opcional)
    echo "%wheel ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers.d/admin

    # Mensaje de bienvenida
    echo "¡Instalación completada! Rocky Linux está listo." > /etc/motd
    %end

----------------------------------------
Paso 2: Guardar el Archivo en el DVD/USB
----------------------------------------

1. **Si usas DVD**:
   - Monta la ISO de Rocky Linux:
     .. code-block:: bash
        mkdir /mnt/iso
        mount -o loop Rocky-9.x.iso /mnt/iso

   - Copia ``ks.cfg`` al directorio raíz del DVD:
     .. code-block:: bash
        cp ks.cfg /mnt/iso/

   - Cierra la sesión:
     .. code-block:: bash
        umount /mnt/iso

2. **Si usas USB**:
   - Copia el archivo a la raíz del USB:
     .. code-block:: bash
        cp ks.cfg /media/usb/

----------------------------------------
Paso 3: Iniciar la Instalación Automatizada
----------------------------------------

1. Inserta el DVD/USB y arranca la máquina.
2. En el menú de arranque de Anaconda (instalador), presiona ``Tab`` para editar la línea de arranque y añade:
   .. code-block:: bash
      inst.ks=hd:LABEL=Rocky-9-0:/ks.cfg

   *Nota*: Si el archivo está en USB, usa:
   .. code-block:: bash
      inst.ks=hd:sdb1:/ks.cfg  # Ajusta ``sdb1`` según tu dispositivo.

3. La instalación comenzará automáticamente sin intervención.

----------------------------------------
Personalización Avanzada
----------------------------------------

- **Particionamiento manual** (ejemplo):
  .. code-block:: bash
    part /boot --fstype="xfs" --size=500
    part / --fstype="xfs" --size=15000
    part /home --fstype="xfs" --size=10000

- **Repositorios adicionales** (en ``%post``):
  .. code-block:: bash
    dnf config-manager --add-repo=https://mirrors.rockylinux.org/rocky/9/AppStream/x86_64/os/

- **Deshabilitar servicios**:
  .. code-block:: bash
    systemctl disable firewalld

----------------------------------------
Solución de Problemas
----------------------------------------

- **Errores en Kickstart**: Verifica logs en ``/var/log/anaconda/``.
- **DVD no detectado**: Asegúrate de que la ISO esté grabada correctamente (usar ``dd`` o ``brasero``).
- **Fallo en particionado**: Usa ``autopart`` o define manualmente las particiones.

----------------------------------------
Referencias
----------------------------------------

- `Documentación Oficial de Rocky Linux <https://docs.rockylinux.org/>`_
- `Guía de Kickstart de Red Hat <https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/9/html/performing_an_advanced_rhel_installation/kickstart-commands-and-options-reference_installing-rhel-as-an-experienced-user>`_



Notas importantes:
-----------------------

Seguridad: Evita contraseñas en texto plano en entornos reales (usa openssl passwd para cifrarlas).

Pruebas: Verifica la plantilla en una máquina virtual antes de usarla en producción.

Soporte para UEFI: Si el sistema usa UEFI, añade bootloader --location=partition.
