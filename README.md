# Actividad 3: Laboratorio de pipeline de Jenkins

**Asignatura:** Entornos de Integración y Entrega Continua (UNIR)
**Alumno:** Luis Rodrigo Sanmartin Cumbicus

Pipeline declarativo de Jenkins que automatiza la integración continua de la calculadora del repositorio
[`srayuso/unir-cicd`](https://github.com/srayuso/unir-cicd): construcción, pruebas unitarias, de API y E2E,
archivado y publicación de los informes, y aviso por correo si el pipeline falla.

## Contenido

| Archivo | Descripcion |
|---|---|
| `Jenkinsfile` | Codigo del pipeline (definido directamente en el trabajo de jenkins) |
| `jenkins/Dockerfile` | Imagen de jenkins con `make` y el cliente de docker |

## Etapas del pipeline

| Etapa | Qué hace |
|---|---|
| Source | Descarga el código con `git` (con `retry(3)` ante fallos puntuales de red) |
| Build | `make build`: construye las imágenes `calculator-app` y `calc-web` |
| Unit tests | `make test-unit` y archiva `results/*.xml` |
| API tests | `make test-api` y archiva `results/*.xml` |
| E2E tests | `make test-e2e` (Cypress) y archiva `results/*.xml` |
| post | `always`: publica los informes con `junit` · `failure`: aviso con nombre del trabajo y numero de ejecucion · `cleanup`: `cleanWs()` |

## Cómo levantar Jenkins (Windows 11 + Docker Desktop)

```powershell
# 1. Construir la imagen de jenkins con make y docker
docker build -t jenkins-docker .\jenkins

# 2. Arrancar Jenkins 
docker run -d --name jenkins --dns 8.8.8.8 --dns 1.1.1.1 -p 8080:8080 `
  -v jenkins_home:/var/jenkins_home -v /var/run/docker.sock:/var/run/docker.sock jenkins-docker

# 3. Contrasenia inicial
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword

# 4. Si faltan plugins tras el asistente (dependencias no descargadas)
docker exec jenkins jenkins-plugin-cli --plugin-download-directory /var/jenkins_home/plugins `
  --plugins commons-lang3-api pipeline-stage-view
docker restart jenkins
```

Despues, en `http://localhost:8080`:

1. **Manage Jenkins → Nodes → Built-In Node → Configure → Labels:** `docker`.
2. **New Item → Pipeline**, con *Pipeline script* el contenido de `Jenkinsfile` y *Use Groovy Sandbox* activado.
3. **Build Now**.

## Resultado

Ejecución correcta con 13 pruebas superadas (7 unitarias 1 de API y 5 E2E) y los informes
`unit_result.xml`, `api_result.xml` y `cypress_result.xml` archivados y publicados.
