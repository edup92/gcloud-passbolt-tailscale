# Pasos para instalar Ansible y ejecutar el playbook

Este documento describe cómo instalar Ansible en tu máquina local y ejecutar el playbook `main.yml` ubicado en este directorio.

## Requisitos previos

- Sistema operativo basado en Linux (Ubuntu/Debian recomendado)
- Usuario con permisos para instalar paquetes (sudo)
- Conexión a Internet para descargar dependencias

## Instalación de Ansible

1. Actualiza la caché de paquetes:

   ```bash
   sudo apt update ; sudo apt install -y ansible
   ```

3. Verifica la instalación:

   ```bash
   ansible --version
   ```

## Estructura del directorio

```
passbolt-ansible/
├── main.yml        # Playbook principal de Ansible
└── README.md       # Este archivo de instrucciones
```

## Ejecución del playbook

1. Navega al directorio del playbook:

   ```bash
    git clone https://github.com/edup92/passbolt-tailscale.git ; cd passbolt-ansible ; ansible-playbook main.yml --connection=local

¡Listo! Con estos pasos, Ansible instalará NGINX, MySQL, PHP 8.2 (con extensiones), Composer, Git, GnuPG y Tailscale en tu máquina local.
