# Pasos para instalar Ansible y ejecutar el playbook

Este documento describe cómo instalar Ansible en tu máquina local y ejecutar el playbook `main.yml` ubicado en este directorio.

## Requisitos previos

- Sistema operativo basado en Linux (Ubuntu/Debian recomendado)
- Usuario con permisos para instalar paquetes (sudo)
- Conexión a Internet para descargar dependencias

## Creacion de instancia de google cloud, snapshot job y firewall

```bash
# 1. Crea la regla de firewall para denegar todo el tráfico entrante
gcloud compute firewall-rules create deny-all-ingress \
  --project=personal-473223 \
  --network=default \
  --direction=INGRESS \
  --priority=100 \
  --action=DENY \
  --rules=all \
  --source-ranges=0.0.0.0/0

# 2. Crea la instancia y el resto de recursos
gcloud compute instances create passbolt \
  --project=personal-473223 \
  --zone=europe-southwest1-a \
  --machine-type=e2-small \
  --network-interface=network-tier=PREMIUM,stack-type=IPV4_ONLY,subnet=default \
  --metadata=enable-osconfig=TRUE,startup-script='apt update && apt install -y ansible' \
  --maintenance-policy=MIGRATE \
  --provisioning-model=STANDARD \
  --service-account=32608782837-compute@developer.gserviceaccount.com \
  --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/trace.append \
  --create-disk=auto-delete=no,boot=yes,device-name=passbolt,image=projects/ubuntu-os-cloud/global/images/ubuntu-minimal-2404-noble-amd64-v20250923a,mode=rw,size=10,type=pd-balanced \
  --no-shielded-secure-boot \
  --shielded-vtpm \
  --shielded-integrity-monitoring \
  --labels=goog-ops-agent-policy=v2-x86-template-1-4-0,goog-ec-src=vm_add-gcloud \
  --reservation-affinity=any \
  --deletion-protection && \
printf 'agentsRule:\n  packageState: installed\n  version: latest\ninstanceFilter:\n  inclusionLabels:\n  - labels:\n      goog-ops-agent-policy: v2-x86-template-1-4-0\n' > config.yaml && \
gcloud compute instances ops-agents policies create goog-ops-agent-v2-x86-template-1-4-0-europe-southwest1-a \
  --project=personal-473223 \
  --zone=europe-southwest1-a \
  --file=config.yaml && \
gcloud compute resource-policies create snapshot-schedule passbolt-snapshot \
  --project=personal-473223 \
  --region=europe-southwest1 \
  --max-retention-days=14 \
  --on-source-disk-delete=KEEP_AUTO_SNAPSHOTS \
  --daily-schedule \
  --start-time=00:00 && \
gcloud compute disks add-resource-policies passbolt \
  --project=personal-473223 \
  --zone=europe-southwest1-a \
  --resource-policies=projects/personal-473223/regions/europe-southwest1/resourcePolicies/passbolt-snapshot
```

## Instalación de Ansible

```bash
sudo apt update ; sudo apt install -y ansible
```

## Ejecución del playbook

```bash
 git clone https://github.com/edup92/passbolt-tailscale.git ; cd passbolt-ansible ; ansible-playbook main.yml --connection=local
