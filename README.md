# Registro Comidas

App web para cargar las comidas del día (con foto opcional) y, al tocar **"Finalizar carga del día"**, recibir un mail HTML con el resumen en `hernangramirez79@gmail.com`.

- **Frontend**: `index.html` — una sola página, sin dependencias de build. Se sube tal cual a GitHub Pages (o cualquier hosting estático).
- **Backend**: `apps-script/Code.gs` — corre en Google Apps Script, vinculado a tu cuenta de Google. Guarda cada comida en tu Google Sheet y las fotos en tu carpeta de Drive, y envía el mail con `GmailApp`.
- **Datos**: tu Google Sheet (`Comidas`) y tu carpeta de Drive para fotos — ya están enlazados en el código con sus IDs.

No hace falta ninguna cuenta ni servicio de terceros: todo corre con tu cuenta de Google + GitHub.

---

## 1. Desplegar el backend (Apps Script)

1. Andá a [script.google.com](https://script.google.com) → **Nuevo proyecto**.
2. Borrá el contenido de `Código.gs` y pegá todo el contenido de `apps-script/Code.gs` de este repo.
3. En el menú de archivos del editor, agregá el archivo `appsscript.json` (⚙️ Ver > Mostrar archivo de manifiesto "appsscript.json" en el editor) y reemplazá su contenido por el de `apps-script/appsscript.json` de este repo.
4. En `Code.gs`, cambiá la línea:
   ```js
   var TOKEN = 'CAMBIAME-POR-UNA-CLAVE-SECRETA';
   ```
   por una clave secreta inventada por vos (letras y números, sin espacios). Esto evita que cualquiera que encuentre la URL pueda escribirte datos.
5. **Implementar > Nueva implementación**:
   - Tipo: **Aplicación web**.
   - Ejecutar como: **Yo** (tu cuenta).
   - Quién tiene acceso: **Cualquier usuario**.
6. Al implementar, Google te va a pedir autorizar el script (acceso a tu Sheet, tu Drive y Gmail) — es normal, es tu propio script actuando en tu nombre.
7. Copiá la **URL de la aplicación web** que te da (termina en `/exec`). La vas a necesitar en el paso 3.

Cada vez que edites `Code.gs`, tenés que crear una **nueva implementación** (o "Gestionar implementaciones" > editar > nueva versión) para que los cambios se reflejen en esa URL.

## 2. Subir el frontend a GitHub

```bash
git remote add origin https://github.com/TU_USUARIO/registro-comidas.git
git branch -M main
git push -u origin main
```

Después, en el repo de GitHub: **Settings > Pages > Deploy from a branch**, elegí `main` y carpeta `/ (root)`. GitHub te va a dar una URL tipo `https://TU_USUARIO.github.io/registro-comidas/` — esa es tu app, ya online.

## 3. Conectar el frontend con el backend

Abrí `index.html`, buscá este bloque cerca del principio del `<script>`:

```js
var CONFIG = {
  SCRIPT_URL: '', // pegá acá la URL de tu Apps Script
  TOKEN: ''       // pegá acá la misma clave secreta del paso 1
};
```

Completá `SCRIPT_URL` con la URL del paso 1.7, y `TOKEN` con la clave del paso 1.4. Guardá, subí el cambio (`git add`, `git commit`, `git push`) y listo — GitHub Pages se actualiza solo.

Mientras `SCRIPT_URL` esté vacío, la app funciona en **modo demo** (todo queda solo en el navegador, no se guarda nada real) para que puedas probar la interfaz sin haber desplegado todavía.

## 4. Probar

1. Abrí la URL de GitHub Pages desde el celular.
2. Cargá una comida de prueba (con foto, para probar la cámara).
3. Fijate que aparezca la fila en tu Google Sheet y la foto en tu carpeta de Drive.
4. Tocá "Finalizar carga del día" y confirmá que llegue el mail a `hernangramirez79@gmail.com`.

---

## Estructura del proyecto

```
registro-comidas/
├── index.html              # la app (frontend)
├── apps-script/
│   ├── Code.gs              # backend: guarda datos, sube fotos, manda el mail
│   └── appsscript.json       # manifiesto del proyecto de Apps Script
└── README.md
```

## Columnas de la Google Sheet (pestaña "Comidas")

| ID | Fecha | Hora | Comida | Detalle | FotoURL | Enviado | Registrado en |
|----|-------|------|--------|---------|---------|---------|----------------|

Estas columnas ya están creadas en tu planilla — la app solo va agregando filas.

## Notas de seguridad

- El endpoint de Apps Script queda accesible por su URL (necesario para que GitHub Pages, que es estático, pueda llamarlo). El `TOKEN` es la única protección: no compartas la URL ni el token públicamente.
- La carpeta de Drive de las fotos es pública (para poder mostrarlas en la app y en el mail sin pasos extra) — cualquiera con el link directo de una foto podría verla, aunque no pueda listarlas ni encontrarlas sin el link exacto.
- Si en algún momento querés dar de baja la app, alcanza con desactivar la implementación en Apps Script (Implementar > Gestionar implementaciones > Archivar).

## Personalizar

- **Tipos de comida**: están definidos en los `<button class="chip">` de `index.html` y en `MEAL_ICONS` de `Code.gs` — agregá o cambiá ahí.
- **Mail destinatario**: `DEST_EMAIL` en `Code.gs` (y `DEST_EMAIL` en `index.html`, solo para el texto que se muestra en la confirmación).
- **Colores**: los tokens `--accent`, `--bg`, etc. al principio del `<style>` de `index.html`.
