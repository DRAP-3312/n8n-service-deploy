# n8n-service-deploy
Repositorio para configuración y deploy la herramienta de automatización N8N

## Actualizar n8n

Para actualizar n8n a una nueva versión:

1. Verifica la última versión estable en [GitHub Releases](https://github.com/n8n-io/n8n/releases)
2. Actualiza la versión en dos archivos:
   - [.deploy/n8n-stack.yml](.deploy/n8n-stack.yml) - línea 32: `image: docker.n8n.io/n8nio/n8n:X.X.X`
   - [.github/workflows/n8n-deploy.yml](.github/workflows/n8n-deploy.yml) - línea 65: `docker pull docker.n8n.io/n8nio/n8n:X.X.X`
3. Haz commit y push de los cambios
4. El workflow de GitHub Actions:
   - Creará un backup automático de tus datos
   - Descargará la nueva imagen
   - Desplegará la actualización
   - Mantendrá solo los últimos 5 backups

**Versión actual:** 1.122.5

### 🔒 Backups

El workflow crea backups automáticos antes de cada despliegue en:
`~/n8n-backups/n8n_backup_YYYYMMDD_HHMMSS.tar.gz`

Para restaurar un backup:
```bash
# Detener el servicio
docker service scale n8n-app_n8n=0

# Restaurar desde backup
docker run --rm -v n8n-app_n8n_storage:/data -v /home/user/n8n-backups:/backup alpine sh -c "cd /data && rm -rf * && tar xzf /backup/n8n_backup_XXXXXXXX_XXXXXX.tar.gz"

# Reiniciar el servicio
docker service scale n8n-app_n8n=1
```

### Actualización manual (si tienes acceso SSH al VPS)

```bash
# Pull de la nueva imagen
docker pull docker.n8n.io/n8nio/n8n:1.122.5

# Actualizar el stack (desde el directorio donde está n8n-stack.yml)
docker stack deploy -c n8n-service-deploy/.deploy/n8n-stack.yml n8n-app

# Verificar el estado
docker service ps n8n-app_n8n
```
