# AD Studio — qué cambiar cuando haya dominio propio

Este sitio está publicado como **preview privado** en GitHub Pages, bajo la cuenta personal
`karenbuenoh`, mientras Andrés consigue su dominio. Varias decisiones se tomaron *para esa
etapa temporal* y hay que revertirlas o completarlas antes del lanzamiento real.

Fecha de este documento: **10 de septiembre de 2026**.

---

## 1. Datos placeholder — obligatorio cambiarlos antes de lanzar

| Qué | Dónde | Valor actual | Valor real |
|---|---|---|---|
| WhatsApp Business | `index.html`, constante `WA_NUMBER` al inicio del `<script>` | `000000000000` | formato internacional sin signos, ej. `521XXXXXXXXXX` |
| Correo de contacto | `index.html`, sección Contacto (`mailto:`) | `hola@adstudio.mx` (inventado) | el real |
| Cobertura | Contacto, `<dd>Nacional y destinos</dd>` | genérico | confirmar con Andrés |
| Tiempo de respuesta | Contacto, `<dd>Mismo día hábil</dd>` | supuesto | confirmar |

El número de WhatsApp aparece **en dos lugares que se llenan solos** desde `WA_NUMBER`
(el botón flotante y el enlace de la barra lateral). Se cambia una sola vez.

## 2. Fotografías — todas son de relleno

Las 19 imágenes de `img/` son de **StockSnap, licencia CC0** (uso comercial libre, sin
atribución). Son un relleno para que Andrés vea el diseño, **no son suyas**.

Al recibir el lote real: pisar cada archivo conservando el nombre y la proporción.

| Archivo | Qué debe ir ahí | Proporción |
|---|---|---|
| `hero-inicio.jpg` | apertura de Inicio | 3:2 |
| `reel-cine.jpg` | carrete ancho de Inicio | 2.39:1 |
| `instalaciones.jpg` | instalaciones corporativas | 3:2 |
| `boda-fineart.jpg` | boda | 3:2 |
| `dron.jpg` | toma aérea | 3:2 |
| `movimiento.jpg` | retrato en movimiento | 3:2 |
| `empresas-hero.jpg` | apertura de Empresas (sector salud/industria) | 3:2 |
| `personas-hero.jpg` | apertura de Personas (boda) | 4:5 |
| `galeria-cine.jpg` | ceremonia, plano ancho | 2.39:1 |
| `danza.jpg` | artistas / bailarines | 4:5 |
| `preparativos.jpg` | detalle de preparativos | 4:5 |
| `familia.jpg` | evento familiar | 4:5 |
| `marca-personal.jpg` | retrato de marca personal | 4:5 |
| `nosotros-hero.jpg` | detrás de cámara | 3:2 |
| `team-1..4.jpg` | retratos del equipo real | 4:5 |
| `estudio.jpg` | estudio o locación | 3:2 |

**Además de reemplazar el archivo, hay que corregir el `alt`** de cada `<img>`: hoy describe la
foto de relleno y quedaría mintiendo sobre la foto real. Los `alt` son lo que lee un lector de
pantalla y lo que Google indexa cuando el sitio sea público.

Exportar en RGB, optimizado para web (el brief pide WebP o JPG ligero), lado largo ~1600 px.

## 3. Bloqueo de indexación — **hay que quitarlo para lanzar**

Esto es lo más fácil de olvidar: **el sitio está configurado para NO aparecer en Google.**
Si se lanza así, el sitio es invisible en buscadores.

Quitar de `<head>` en el lanzamiento:

```html
<meta name="robots" content="noindex, nofollow, noarchive, nosnippet, noimageindex, notranslate">
<meta name="googlebot" content="noindex, nofollow">
<meta name="bingbot" content="noindex, nofollow">
<meta name="robots" content="noai, noimageai">
```

Y borrar o vaciar `robots.txt`, que hoy dice `Disallow: /`.

`<meta name="noai, noimageai">` es opcional dejarlo: bloquea el rastreo para entrenamiento de
IA sin afectar el posicionamiento en buscadores. Decisión de Andrés.

## 4. SEO que falta (se omitió a propósito por el `noindex`)

Con el sitio oculto no tenía sentido escribirlo. Al lanzar hay que agregar:

- `<meta name="description">` — un resumen de ~155 caracteres.
- Open Graph y Twitter Card (`og:title`, `og:description`, `og:image`, `og:url`) para que el
  link se vea bien al compartirlo por WhatsApp e Instagram. **Esto importa mucho** para un
  negocio que se mueve por redes.
- Favicon e íconos de aplicación.
- `<link rel="canonical">` con el dominio definitivo.
- Datos estructurados `LocalBusiness` / `ProfessionalService` (schema.org) con dirección,
  teléfono y horario: es lo que alimenta la ficha de Google.
- `sitemap.xml`.

## 5. Cambio de hosting

Hoy: `https://karenbuenoh18.github.io/ad-studio/` — repo personal de Karen.

Al migrar hay dos caminos:

1. **Transferir el repositorio** a la cuenta de GitHub de Andrés (Settings → Transfer
   ownership). Se lleva el sitio y el historial completo, y Karen deja de ser responsable.
2. **Apuntar el dominio** desde el repo actual: agregar un archivo `CNAME` con el dominio y
   configurar los DNS. Sirve como paso intermedio, pero el repo sigue siendo de Karen.

Recomendado: **transferir**. Es un sitio de él.

Detalle técnico al mover: hoy vive en un **subdirectorio** (`/ad-studio/`). Al pasar a un
dominio propio quedará en la raíz (`/`). Como todas las rutas del sitio son **relativas**
(`img/...`, `fonts/...`) y la navegación es por hash (`#/empresas`), **no hay que tocar nada**.
Está hecho así justamente para que la mudanza sea copiar y pegar.

## 6. Decisiones técnicas tomadas para la etapa de preview

Cosas que se hicieron por seguridad/privacidad mientras el sitio está público sin ser oficial.
Ninguna estorba en producción, pero conviene saber que están:

- **Fuentes auto-alojadas.** Montserrat, Cormorant Garamond e Inter viven en `fonts/` en vez de
  cargarse desde Google Fonts. El sitio no hace ni una llamada a terceros: nadie externo ve
  quién lo visita. Solo se bajó el subconjunto latino (12 archivos, 516 KB).
  Si algún día se agrega un idioma con otros caracteres, hay que bajar más subconjuntos.
- **Content-Security-Policy** por `<meta>`, con `default-src 'none'`: el navegador tiene
  prohibido cargar cualquier recurso externo. Si más adelante se agrega Google Analytics, un
  mapa, un video de YouTube o un píxel de Meta, **hay que autorizarlo explícitamente en esa
  línea o simplemente no cargará** — y falla en silencio, sin error visible. Es la causa número
  uno de "no entiendo por qué no aparece el video".
- **`referrer: no-referrer`** — los sitios a los que se salga no sabrán de dónde vino el clic.
- **Sin analítica, sin cookies, sin rastreadores.** Hoy no hay ninguno. Si se agrega analítica
  al lanzar, con eso aparece la obligación de avisar (aviso de privacidad / cookies).
- **El formulario no tiene backend.** No guarda ni envía nada a ningún servidor: arma el texto
  y abre WhatsApp. Ventaja: no hay datos personales almacenados en ningún lado, así que no hay
  nada que proteger ni que cumplir en materia de datos. Si algún día se quiere que llegue por
  correo, ahí sí entra un servicio externo y hay que revisar la CSP y el aviso de privacidad.

## 7. Contenido que sigue pendiente de confirmar con el cliente

Redactado por nosotros a partir del brief, **no validado por Andrés**:

- Los textos de la sección **Nosotros** (filosofía y trayectoria) — la línea de tiempo
  (oficio → video 4K → dron → firma con dos unidades) es una interpretación razonable del
  brief, pero él debe confirmarla o corregirla.
- Los **roles del equipo** (dirección de fotografía, edición y color, operación de dron).
  El brief menciona "el equipo técnico detrás de escena" sin nombres. Faltan los nombres reales.
- Los **paquetes de Empresas no tienen precio**. El brief define el alcance de cada uno pero no
  las tarifas. Decidir si se publican precios o se mantiene "cotización a medida".
- Las frases de la franja de credenciales (15+ años, 4K, dron) salen del brief; el "2" de
  unidades de negocio es nuestro.

## 8. Origen del proyecto

- `ESTRUCTURA WEB.docx` — brief del cliente, 10 sep 2026. **Es la fuente de verdad.**
- `Andy_Fotografia_Diseno_Web.pdf` y `Propuesta_Portafolio_Fotografico_2.pdf` — material de julio.
- `prototipo-andy-fotografia.html` — boceto viejo de julio 2025, con la marca anterior
  ("Andy Fotografía", solo bodas). **Quedó superado por el brief** y no debe usarse como
  referencia: cambió el nombre, la paleta, las tipografías y el modelo de negocio.
