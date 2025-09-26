# Pasos para instalar Ansible y ejecutar el playbook

Este documento describe cómo instalar Ansible en tu máquina local y ejecutar el playbook `main.yml` ubicado en este directorio.

## Requisitos previos

- Sistema operativo basado en Linux (Ubuntu/Debian recomendado)
- Usuario con permisos para instalar paquetes (sudo)
- Conexión a Internet para descargar dependencias

## Instalación de Ansible

```bash
sudo apt update ; sudo apt install -y ansible
```

## Ejecución del playbook

```bash
 git clone https://github.com/edup92/passbolt-tailscale.git ; cd passbolt-ansible ; ansible-playbook main.yml --connection=local
