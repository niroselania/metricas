# Ventas · Centro de control (v2.1.0)

Tablero de ventas sin IVA con gráficos circulares de **Alcance ($)**, **Alcance Días**,
**FCT (U)** y **Alcance (U)** por canal (Retail, Retiro, Bariloche, E-commerce, Mayorista).

La planilla que se sube queda **guardada en el servidor**: cualquier navegador o
computadora ve siempre la última versión, y se puede elegir el mes.

| Archivo | Qué hace |
|---|---|
| `index.html` | El tablero |
| `server.py` | Sirve el sitio, recibe la planilla y guarda los datos en `/data` |
| `ventas_processor.py` | Lee las pestañas `DB <MES>` (detalle) y `<MES>` (FCT) |
| `Dockerfile`, `requirements.txt`, `docker-compose.yml` | Contenedor y stack para Portainer (puerto **8086**) |

> ⚠️ Repositorio **Private**: no subas la planilla a GitHub (el `.gitignore` ya la excluye).

## Novedades v2.1.0
- **Las cargas se combinan por mes**: cada planilla reemplaza solo los meses que trae;
  los demás quedan como estaban. Se puede subir una planilla con todo el año y después
  otra solo con noviembre actualizado.
- **Tiendas extra** (ej. POP UP en julio–septiembre) aparecen solas como pestaña y
  se suman a RETAIL y TOTALES.
- Debajo del título se ve de qué planilla y cuándo se cargó el mes que estás mirando.

Para pasar de 2.0 a 2.1: reemplazá `index.html`, `server.py`, `ventas_processor.py`,
`docker-compose.yml` y `README.md` en GitHub y hacé **Pull and redeploy**. Los datos ya
cargados se conservan.

## Actualizar desde la v1 (la versión con nginx)
1. En GitHub reemplazá `index.html`, `Dockerfile`, `docker-compose.yml`, `README.md`,
   `.gitignore`, `.dockerignore` y agregá `server.py`, `ventas_processor.py`, `requirements.txt`.
2. **Borrá `nginx.conf`** del repositorio (ya no se usa).
3. Portainer > Stacks > ventas-dashboard > **Pull and redeploy** (con *Re-pull image* / rebuild).

## Instalación nueva
1. GitHub > **New repository** > `ventas-dashboard` > **Private** > subí estos archivos.
2. Portainer > **Stacks > Add stack** > Build method **Repository**:
   - URL `https://github.com/TU-USUARIO/ventas-dashboard`, reference `refs/heads/main`,
     compose path `docker-compose.yml`.
   - **Authentication**: usuario de GitHub + Personal Access Token (lectura de *Contents*).
3. **Environment variables** > agregá `UPLOAD_TOKEN` = un código a elección (recomendado).
4. **Deploy the stack** y abrí `http://IP-DE-TU-SERVIDOR:8086`.

## Uso
- **Subir planilla**: la primera vez pide el código de carga (queda recordado en ese navegador).
  Procesa todos los meses que tengan pestaña `DB <MES>` con datos + `<MES>` (≈15 s con la planilla FY27).
  Un mes que viene en la planilla nueva **reemplaza** al guardado (gana la última carga);
  no subas una planilla vieja después de una nueva.
- **Mes**: selector arriba a la derecha (por defecto, el más reciente).
- Alcance Días = días transcurridos / días del mes; en meses cerrados es 100%.
- El sitio se refresca solo cada 5 minutos.

## Datos y verificación
- Volumen `ventas-data` (`/data`): `ventas-data.json` (vigente), `history/` (últimas 30 cargas).
- `http://IP:8086/api/health` → estado · `/api/history` → últimas cargas.
- Si el código de carga se pierde: cambiá `UPLOAD_TOKEN` en Portainer y hacé Redeploy.
