# postgres-stack

Compose de PostgreSQL para ser consumido por Odoo (u otras apps) en la red `odoo-net`. Incluye healthcheck, logging con rotación, volumen nombrado y servicios opcionales para métricas y backups.

## Estructura
```
docker-compose.yml   # Servicio pg16, exporter y perfil de backup
.env.example         # Variables de entorno de ejemplo
config/
  postgresql.conf    # Config base montada en el contenedor
  pg_hba.conf        # Reglas de acceso
backups/             # Carpeta destino de dumps (perfil backup)
```

## Uso
1) Copia `.env-example` a `.env` y ajusta:
   - `POSTGRES_IMAGE`, `POSTGRES_INSTANCE` (nombra contenedor y volumen), `POSTGRES_PORT` (puerto host).
   - `POSTGRES_DB`, `POSTGRES_USER`, `POSTGRES_PASSWORD`.
2) Arranca la base:
   ```bash
   docker compose --env-file .env up -d
   ```
   Se crea el volumen `${POSTGRES_INSTANCE}-data`.
3) Exporter (Prometheus) escucha en `${POSTGRES_EXPORTER_PORT:-9187}`.
4) Backup on-demand:
   ```bash
   docker compose --env-file .env --profile backup run --rm backup
   ```
   Deja el dump en `backups/`.

## Notas
- `config/postgresql.conf` y `pg_hba.conf` se montan en modo lectura; ajusta allí tunning y reglas de acceso.
- La red `odoo-net` debe existir y ser compartida con Odoo.
- Logging de contenedor rota con `json-file` (100m, 3 archivos).
