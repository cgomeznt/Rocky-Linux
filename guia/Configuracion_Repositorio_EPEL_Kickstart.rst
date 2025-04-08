==========================================================
Configuración de Rocky Linux con Repositorio EPEL via Kickstart
==========================================================

----------------------------
Paso 1: Habilitar EPEL en Kickstart
----------------------------

Agrega estas líneas en la sección ``%post`` de tu archivo ``ks.cfg``:

.. code-block:: bash

    %post
    # Instalar EPEL
    dnf -y install epel-release
    
    # Actualizar todo el sistema (incluyendo paquetes de EPEL)
    dnf -y update
    
    # Opcional: Verificar repositorios habilitados
    dnf repolist enabled
    %end

----------------------------
Paso 2: Configuración Completa con EPEL
----------------------------

Ejemplo de archivo ``ks.cfg`` completo:

.. code-block:: bash

    # Rocky Linux 9 - Con repositorio EPEL
    install
    url --url="http://mirror.rockylinux.org/pub/rocky/9/BaseOS/x86_64/os"
    lang es_VE.UTF-8
    keyboard us
    
    %packages
    @^minimal
    @core
    epel-release  # Paquete EPEL añadido explícitamente
    vim
    wget
    %end

    %post
    # Configurar EPEL
    dnf -y install epel-release
    
    # Forzar actualización completa
    dnf -y update
    
    # Opcional: Instalar paquetes específicos de EPEL
    dnf -y install htop nmap
    
    # Verificar repositorios
    echo "Repositorios habilitados:" > /root/repos.log
    dnf repolist enabled >> /root/repos.log
    %end

----------------------------
Notas Clave
----------------------------

1. **Orden de operaciones**:
   - Primero se instala ``epel-release``
   - Luego se ejecuta ``dnf update`` para actualizar todos los paquetes

2. **Verificación**:
   - Tras la instalación, ejecute:

     .. code-block:: bash
        dnf repolist | grep epel
        rpm -q epel-release

3. **Versiones compatibles**:
   - EPEL 9 para Rocky Linux 9
   - EPEL 8 para Rocky Linux 8

----------------------------
Referencias
----------------------------

- `Repositorio EPEL oficial <https://fedoraproject.org/wiki/EPEL>`_
- `Guía de configuración EPEL en Rocky <https://docs.rockylinux.org/guides/epel/>`_

Diferencias clave con la configuración anterior:
------------------------------------------------

Se añadió epel-release en %packages para instalación temprana

Se incluyó la secuencia completa de actualización en %post

Se agregó verificación de repositorios
