# Despliegue de la PokeDex en Azure Static Web Apps

**Autora:** Yomaira Pardo
**Repositorio:** https://github.com/JomiJoestar/pokedex
**URL de la aplicación:** [POKEDEX](https://gentle-rock-0b9771e1e.2.azurestaticapps.net/)]
**Resultado de seguridad:** A+ en [securityheaders.com](https://securityheaders.com)

Este documento explica cómo se compiló, se subió a GitHub y se publicó en Azure la aplicación PokeDex (proyecto Angular), incluyendo la configuración de los encabezados de seguridad y los problemas encontrados. La creación de la cuenta de Azure está en [README.md](README.md).

---

## 1. Resumen del proceso

1. Descargar el código fuente y compilarlo en el computador local.
2. Crear el repositorio `pokedex` en GitHub y subir el código.
3. Crear una Static Web App en Azure conectada a ese repositorio.
4. Azure compila y publica automáticamente con GitHub Actions.
5. Agregar los encabezados de seguridad y escanear el sitio.

**Por qué Static Web Apps:** la PokeDex es una aplicación Angular que, una vez compilada, se compone solo de archivos estáticos (HTML, CSS y JavaScript). Static Web Apps sirve esos archivos con HTTPS incluido, se conecta a GitHub y publica cada cambio de forma automática, con un plan gratuito.

---

## 2. Requisitos previos

Se verificaron las herramientas instaladas:

```bash
git --version
node --version
npm --version
```

![Versiones de git, node y npm](capturas/08-versiones-herramientas.png)

---

## 3. Obtener y compilar el código en local

### 3.1 Obtener el código fuente

Se clonó el repositorio del profesor y se copió la carpeta del proyecto Angular (`poke-dex-lab/source/pokedex-angular`) a una carpeta propia:

```bash
git clone https://github.com/rcuello/ac4dem1a.git
```

Se verificó que en la raíz de la carpeta copiada estuvieran los archivos `package.json` y `angular.json`.

![Carpeta del proyecto con package.json y angular.json](capturas/09-carpeta-proyecto.png)

### 3.2 Instalar dependencias

Dentro de la carpeta del proyecto:

```bash
npm install
```

Este comando descarga las dependencias en la carpeta `node_modules`.

![Resultado de npm install](capturas/10-npm-install.png)

### 3.3 Compilar

```bash
npx ng build
```

Este comando compila el proyecto y crea la carpeta `dist/`.

![Resultado de ng build](capturas/11-ng-build.png)

La carpeta de salida quedó en `dist/pokedex-angular`. Esa ruta es la **ubicación de salida** (*output location*) que se configura después en Azure.

![Carpeta dist](capturas/12-carpeta-dist.png)

![Ruta de salida dist/pokedex-angular](capturas/13-ruta-dist.png)

### 3.4 Probar en local

```bash
npx ng serve
```

La aplicación compiló correctamente y se abrió en `http://localhost:4200`.

![Compilación de ng serve](capturas/14-ng-serve.png)

![Aplicación funcionando en localhost](capturas/15-app-localhost.png)

---

## 4. Crear el repositorio en GitHub y subir el código

### 4.1 Crear el repositorio

En GitHub se creó un repositorio **público** llamado `pokedex`, sin marcar README, `.gitignore` ni licencia, para que quedara vacío.

![Creación del repositorio pokedex](capturas/16-crear-repositorio.png)

### 4.2 Subir el código

En la carpeta del proyecto:

```bash
git init
git add .
git commit -m "Código inicial de la Pokedex"
git branch -M main
git remote add origin https://github.com/JomiJoestar/pokedex.git
git push -u origin main
```

![Comandos de git](capturas/17-git-push.png)

> El `push` falló la primera vez por un archivo de workflow del proyecto original. Ver el problema 2 en la sección 8.

---

## 5. Crear la Static Web App en Azure

### 5.1 Buscar el servicio

En [portal.azure.com](https://portal.azure.com), en la barra de búsqueda se escribió **Static Web Apps** y se seleccionó el servicio.

![Búsqueda de Static Web Apps](capturas/18-buscar-static-web-apps.png)

Se pulsó **Crear**.

![Lista vacía de Static Web Apps](capturas/19-crear-static-web-app.png)

### 5.2 Datos básicos

- **Suscripción:** la de la cuenta gratuita (Azure subscription 1).
- **Grupo de recursos:** nuevo, llamado `rg-pokedex`.
- **Nombre:** un nombre único para la aplicación.
- **Tipo de plan:** **Gratis (Free)**.

![Formulario de datos básicos](capturas/20-formulario-basico.png)

![Nombre de la aplicación y plan Free](capturas/21-nombre-plan-free.png)

### 5.3 Conectar con GitHub

- **Origen:** GitHub.
- Se inició sesión con GitHub y se autorizó el acceso.

![Origen GitHub](capturas/22-origen-github.png)

- **Organización:** mi usuario de GitHub.
- **Repositorio:** `pokedex`.
- **Rama:** `main`.

![Repositorio y rama](capturas/23-repositorio-rama.png)

### 5.4 Detalles de compilación

| Campo | Valor |
|---|---|
| Valores preestablecidos de compilación | Angular |
| Ubicación de la aplicación | `/` |
| Ubicación de la API | (vacío) |
| Ubicación de salida | `dist/pokedex-angular` |

Se pulsó **Revisar y crear** y luego **Crear**.

---

## 6. Verificar el despliegue

Al crear el recurso, Azure agregó un workflow de GitHub Actions al repositorio (carpeta `.github/workflows`). Cada vez que se hace `push` a `main`, ese workflow compila y publica la aplicación.

![Recurso Static Web App creado](capturas/26-recurso-creado.png)

En la pestaña **Actions** de GitHub la ejecución terminó con el ✔ verde.

![GitHub Actions en verde](capturas/27-github-actions-verde.png)

Para traer el workflow que Azure creó al computador local:

```bash
git pull
```

La aplicación quedó publicada y accesible desde la URL pública del recurso.

![Página publicada](capturas/28-pagina-publicada.png)

---

## 7. Encabezados de seguridad

### 7.1 Primer escaneo

Antes de agregar los encabezados se escaneó el sitio en [securityheaders.com](https://securityheaders.com). Resultado: [ESCRIBE AQUÍ LA NOTA QUE TE SALIÓ ANTES, y agrega la captura si la tienes].

### 7.2 Archivo de configuración

En Static Web Apps los encabezados HTTP se definen en un archivo `staticwebapp.config.json` ubicado en la raíz del proyecto, junto a `package.json`.

![Archivo staticwebapp.config.json en la carpeta del proyecto](capturas/29-archivo-config.png)

Contenido del archivo:

```json
{
  "navigationFallback": {
    "rewrite": "/index.html",
    "exclude": ["/assets/", ".{css,js,gif,png,jpg,ico,woff,woff2,ttf,eot,otf,json}"]
  },
  "globalHeaders": {
    "Content-Security-Policy": "default-src 'self'; script-src 'self'; style-src 'self' 'unsafe-inline'; img-src 'self' data: https:; font-src 'self' data:; connect-src 'self' https://beta.pokeapi.co https://graphql.pokeapi.co https://pokeapi.co; object-src 'none'; base-uri 'self'; form-action 'self'; frame-ancestors 'none'",
    "X-Frame-Options": "DENY",
    "Permissions-Policy": "geolocation=(), camera=(), microphone=(), payment=()",
    "Referrer-Policy": "strict-origin-when-cross-origin",
    "X-Content-Type-Options": "nosniff"
  }
}
```

![Contenido del archivo de configuración](capturas/30-contenido-config.png)

### 7.3 Qué hace cada encabezado

| Encabezado | Función |
|---|---|
| `Content-Security-Policy` | Define desde qué orígenes puede cargar la página scripts, estilos, imágenes y conexiones. Evita ataques de inyección de código (XSS). |
| `X-Frame-Options: DENY` | Impide que la página se muestre dentro de un iframe de otro sitio (clickjacking). |
| `X-Content-Type-Options: nosniff` | Evita que el navegador "adivine" el tipo de un archivo. |
| `Referrer-Policy` | Controla cuánta información del origen se envía al navegar a otro sitio. |
| `Permissions-Policy` | Desactiva funciones del navegador que la app no usa (ubicación, cámara, micrófono, pagos). |

Además, el `navigationFallback` hace que, si se entra directamente a una ruta interna de la aplicación, Azure devuelva `index.html` y Angular resuelva la ruta, evitando errores 404.

En `connect-src` se permiten los dominios de PokéAPI, porque la aplicación consulta ahí los datos de los Pokémon.

### 7.4 Publicar los cambios

```bash
git add .
git commit -m "Agrega headers de seguridad"
git push
```

Se esperó a que GitHub Actions terminara en verde y se volvió a escanear.

### 7.5 Resultado del escaneo

El sitio obtuvo la calificación **A+** en securityheaders.com.

![Resultado A+ en securityheaders.com](capturas/31-escaneo-a-plus.png)

---

## 8. Problemas encontrados y solución

### Problema 1: la región no estaba permitida (`RequestDisallowedByAzure`)

**Qué pasó:** al crear la Static Web App con la suscripción de Azure for Students, el despliegue falló con el error `RequestDisallowedByAzure`: la suscripción tiene una política que limita las regiones donde se pueden crear recursos, y las regiones en las que existe Static Web Apps no estaban permitidas.

![Revisión con el error de política](capturas/24-error-revision.png)

![Error de región](capturas/25-error-region.png)

**Solución:** se creó una cuenta gratuita de Azure con otro correo (ver [README.md](README.md)) y se repitió la creación con esa suscripción, donde el recurso se creó sin errores.

### Problema 2: el `push` fue rechazado por un archivo de workflow

**Qué pasó:** al subir el código por primera vez, GitHub rechazó el `push` con el mensaje `refusing to allow an OAuth App to create or update workflow ... without workflow scope`. El proyecto original incluía el archivo `.github/workflows/pipelines.yml`, y GitHub exige un permiso especial para subir ese tipo de archivos.

![Error de push por el workflow](capturas/32-error-push-workflow.png)

**Solución:** se eliminó la carpeta del workflow original (Azure crea el suyo propio), se reescribió el commit y se volvió a subir:

```powershell
Remove-Item -Recurse .github\workflows
git add -A
git commit --amend -m "Código inicial de la Pokedex"
git push -u origin main
```

### Problema 3: la aplicación se veía sin las imágenes de los Pokémon

**Qué pasó:** tras desplegar, la página cargaba con los nombres de los Pokémon pero sin sus imágenes. La aplicación no encontraba la ruta de las imágenes en producción.

![Aplicación publicada sin imágenes](capturas/28-pagina-publicada.png)

**Solución:** en el archivo `src/environments/environment.prod.ts` se agregó la ruta de las imágenes:

```ts
imagesPath: '/assets/images',
```

![Cambio en environment.prod.ts](capturas/33-corrigiendo-imagespath.png)

Después se subió el cambio con `git add .`, `git commit` y `git push`, y Azure volvió a publicar la aplicación.

### Problema 4: la primera política de seguridad rompía el diseño

**Qué pasó:** una primera versión del encabezado `Content-Security-Policy` era demasiado estricta. La consola del navegador (F12) mostraba errores de tipo "violates the following Content Security Policy directive": se bloqueaban los estilos en línea que Angular genera y las conexiones a PokéAPI, por lo que la página se veía sin diseño y mostraba un error 500.

**Solución:** se ajustó la política para permitir los estilos en línea (`style-src 'self' 'unsafe-inline'`) y las conexiones a los dominios de PokéAPI (`connect-src`), como aparece en la configuración final de la sección 7.2. Con eso la aplicación volvió a funcionar y se mantuvo la calificación A+.

---

## 9. Auditorías adicionales (opcional)

[Si hiciste el escaneo con SSL Labs u OWASP ZAP, describe aquí el resultado y agrega las capturas. Si no lo hiciste, borra esta sección.]

---

## 10. Eliminación de recursos

Una vez finalizada la evaluación, se elimina el grupo de recursos `rg-pokedex` desde el portal de Azure (**Grupos de recursos → rg-pokedex → Eliminar grupo de recursos**) para no dejar servicios en ejecución.
