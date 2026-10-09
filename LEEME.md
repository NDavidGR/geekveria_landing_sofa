# Landing Geekveria · SOFA

```
index.html     la página (todo el diseño y el código en un solo archivo)
img/           las fotos, ya optimizadas
LEEME.md       esto
```

No necesita servidor propio, base de datos ni PHP. Para publicar, sube `index.html` y la carpeta `img` al mismo nivel en cualquier hosting estático (Vercel, Namecheap, etc.).

---

## 1. Conectar el formulario (10 minutos, hacerlo ANTES de publicar)

Mientras no se conecte, el formulario muestra "No se pudo enviar" y no guarda nada.

1. Crea una hoja nueva en Google Sheets, por ejemplo "Registros Geekveria SOFA".
2. En la hoja: **Extensiones → Apps Script**. Borra lo que haya y pega esto:

```js
const HOJA = 'Registros';

function doPost(e) {
  const lock = LockService.getScriptLock();
  lock.waitLock(10000);
  try {
    const p = e.parameter;
    const ss = SpreadsheetApp.getActiveSpreadsheet();
    const sh = ss.getSheetByName(HOJA) || ss.insertSheet(HOJA);
    if (sh.getLastRow() === 0) {
      sh.appendRow(['fecha', 'nombre', 'correo', 'celular', 'autorizacion', 'origen']);
    }
    sh.appendRow([new Date(), p.nombre || '', p.correo || '', p.celular || '', p.autorizacion || '', p.origen || '']);
    return ContentService.createTextOutput('ok');
  } finally {
    lock.releaseLock();
  }
}
```

3. **Implementar → Nueva implementación → tipo "Aplicación web"**.
   - Ejecutar como: **Yo**
   - Quién tiene acceso: **Cualquier persona**
4. Autoriza los permisos cuando Google los pida.
5. Copia la URL de la aplicación web (termina en `/exec`).
6. Abre `index.html`, busca `var FORM_ENDPOINT = "";` y pega la URL entre las comillas.
7. Prueba: abre la página, llena el formulario con datos de prueba y revisa que aparezca la fila en la hoja. Bórrala después.

Si cambias el código de Apps Script, hay que crear una **nueva implementación** para que el cambio aplique.

---

## 2. Antes de imprimir el QR

- Publica la página y copia la URL final.
- Pruébala desde un celular con datos móviles, no con wifi.
- Genera el QR con esa URL. Si quieres saber cuánta gente llegó por el QR, usa `?src=qr-sofa` al final de la URL: ese valor queda en la columna `origen` de la hoja.
- En `index.html`, busca `og:image` y cambia `img/og.jpg` por la URL completa de la imagen (por ejemplo `https://tudominio.com/img/og.jpg`). Con la ruta relativa, WhatsApp e Instagram no muestran la vista previa al compartir el enlace.

---

## 3. Qué hace y qué no hace el formulario

- Guarda: fecha, nombre, correo, celular, autorización y origen.
- No comprueba los pasos 01 y 03 (seguir en redes y compartir la foto con **@geekveria** y **#NacionGeekSOFA**). Eso lo revisa el equipo en el local, en el celular del cliente.
- Tiene un campo trampa oculto contra bots.
- La barra fija inferior se esconde mientras el formulario está a la vista.
