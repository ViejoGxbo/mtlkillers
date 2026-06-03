# MTLKILLERS V1
### Sistema de automatización retail — Sodimac Perú → Illustrator / Photoshop

---

## ¿Qué es MTLKILLERS?

MTLKILLERS es una herramienta de automatización para diseñadores de retail que trabajan con productos de Sodimac Perú. Permite extraer datos de productos directamente desde la web (nombre, SKU, precios, stock) y aplicarlos automáticamente a plantillas de Illustrator o Photoshop mediante scripts JSX, eliminando el trabajo manual de copiar y pegar información.

---

## Acceso

**App principal:**
```
https://viejogxbo.github.io/mtlkillers/
```

Funciona desde cualquier computador con Chrome. No requiere instalación.

---

## Flujo de trabajo

```
1. Abres la app en Chrome
2. Pegas las URLs de productos Sodimac
3. Click "Extraer datos" → la app obtiene nombre, SKU, precios
4. Click "JSX Illustrator" o "JSX Photoshop"
5. En AI o PS: Archivo → Scripts → Ejecutar script → seleccionas el JSX
6. Las capas se llenan automáticamente
7. Se exportan los artboards individualmente en EXPORT_CARDS en tu escritorio
```

---

## Extensión de Chrome (recomendado)

Algunas URLs de Sodimac están protegidas y los proxies externos las bloquean. La extensión de Chrome resuelve esto extrayendo los datos directamente desde la página abierta en el navegador, sin proxies.

**Instalación:**
1. Descarga `MTLKILLERS_EXT.zip` desde la app
2. Extrae el ZIP
3. Abre `chrome://extensions`
4. Activa **Modo desarrollador** (arriba a la derecha)
5. Click **Cargar descomprimida** → selecciona la carpeta `MTLKILLERS_EXT`
6. Copia el ID que aparece (ej: `debeeljbaelhbnnhnldkggpfgmdaegcm`)
7. Pégalo en la app y click **Conectar**

Cuando la extensión está conectada, la app la usa automáticamente. Si falla, usa los proxies como respaldo.

---

## Convención de capas en Illustrator

El archivo `.ai` debe tener:
- Una **capa por artboard** con el mismo nombre (ej: `LIMA 1`, `LIMA 2`...)
- Dentro de cada capa, los elementos con estos nombres exactos (mayúsculas y guiones bajos)

### Capas comunes (todas las mesas):

| Nombre de capa | Contenido |
|----------------|-----------|
| `TXT_DESC` | Nombre completo del producto |
| `TXT_SKU` | Cód. tienda (ej: `4479688`) |

### Capas de precio (mesas LIMA):

| Nombre de capa | Contenido | Ejemplo |
|----------------|-----------|---------|
| `TXT_PRECIO` | Precio principal entero | `559` |
| `TXT_DECIMAL` | Decimales con punto | `.90` |
| `TXT_PRECIO_REGULAR` | Precio regular (cuando hay CMR) | `599.90` |
| `TXT_PRECIO_ANTES` | Precio tachado anterior | `699.90` |

### Capas de fondo (mesas LIMA):

| Nombre de capa | Descripción |
|----------------|-------------|
| `BG_PRECIO_CMR` | Rectángulo rojo — visible cuando hay precio CMR |
| `BG_PRECIO_REGULAR` | Rectángulo amarillo — visible cuando solo hay precio regular |
| `GRP_CMR` | Grupo con cards CMR/Débito Falabella |

### Reglas de visibilidad automática:

- **Producto con CMR** → muestra `BG_PRECIO_CMR` + `GRP_CMR` + `TXT_PRECIO_REGULAR` / oculta `BG_PRECIO_REGULAR`
- **Producto sin CMR** → muestra `BG_PRECIO_REGULAR` / oculta `BG_PRECIO_CMR` + `GRP_CMR`
- **Tiene precio anterior** → muestra `TXT_PRECIO_ANTES` y `Precio antes: S/`
- **Sin precio anterior** → oculta `TXT_PRECIO_ANTES` y `Precio antes: S/`

---

## Estructura del archivo .ai

```
📄 MTLKILLERS_CAMPAÑA.ai
├── 🎨 LIMA 1  (capa + artboard 1080x1080px)
│   ├── TXT_DESC
│   ├── TXT_SKU
│   ├── TXT_PRECIO
│   ├── TXT_DECIMAL
│   ├── TXT_PRECIO_REGULAR
│   ├── TXT_PRECIO_ANTES
│   ├── BG_PRECIO_CMR
│   ├── BG_PRECIO_REGULAR
│   └── GRP_CMR
├── 🎨 LIMA 2
│   └── (mismas capas)
...
```

**Importante:** El nombre de la capa debe coincidir exactamente con el nombre del artboard.

---

## Dimensiones y posiciones del diseño (1080x1080px)

| Elemento | X | Y | Fuente | Tamaño |
|----------|---|---|--------|--------|
| `TXT_DESC` | 216 | 935 | PFBeauSansPro-Regular | 31px |
| `TXT_SKU` | 290 | 1036 | PFBeauSansPro-Regular | 22px |
| `TXT_PRECIO` | 835 | 809 | PFBeauSansPro-Bold | 76px |
| `TXT_DECIMAL` | 836 | 967 | PFBeauSansPro-Bold | 23px |
| `TXT_PRECIO_REGULAR` | 797 | 1022 | PFBeauSansPro-Light | 22px |
| `TXT_PRECIO_ANTES` | 797 | 1050 | PFBeauSansPro-Light | 22px |
| `BG_PRECIO_CMR` | 824.86 | 892.35 | — | 281x107, radio 10pt |
| `BG_PRECIO_REGULAR` | 824.86 | 892.35 | — | 281x107, radio 10pt |
| `GRP_CMR` | 649 | 884 | — | 54x121 |

---

## Lógica de precios

| Caso | TXT_PRECIO | TXT_PRECIO_REGULAR | TXT_PRECIO_ANTES |
|------|------------|-------------------|-----------------|
| Solo precio regular | Precio regular | — | — |
| Precio CMR + Regular | Precio CMR | Precio regular | — |
| CMR + Regular + Antes | Precio CMR | Precio regular | Precio antes |
| Regular + Antes | Precio regular | — | Precio antes |

**Formato de precios:** Siempre con punto decimal. `559.90`, nunca `559,90`.
**TXT_DECIMAL:** Siempre con punto. `.90`, `.00`, `.70`.

---

## SKU — Cód. tienda

El sistema extrae el **Cód. tienda** (no el Cód. del producto). Lo busca en este orden:
1. Regex `Cód. tienda` en el HTML de la página
2. Fallback: número en la URL de la imagen (`sodimacPE/XXXXXX_01`)

---

## Corrección automática de tildes

El sistema corrige automáticamente palabras sin tilde comunes en nombres de productos:

| Sin tilde | Con tilde |
|-----------|-----------|
| Electrico | Eléctrico |
| Bateria | Batería |
| Ceramica | Cerámica |
| Inalambrico | Inalámbrico |
| Portatil | Portátil |
| Automatico | Automático |
| Hidraulico | Hidráulico |
| Neumatico | Neumático |

Si encuentras palabras que no se corrigen, agrégalas al diccionario `TILDES` en el HTML.

---

## Exportación

Los archivos se guardan en:
```
~/Desktop/EXPORT_CARDS/
```

Nomenclatura del archivo:
```
MTLKILLERS_LIMA1_4479688.jpg
MTLKILLERS_LIMA2_9087524.jpg
```

Formato: **JPG**, calidad 80, resolución 150dpi, recortado al artboard.

---

## Botones de la app

| Botón | Función |
|-------|---------|
| `⬡ CREAR DOCUMENTO BASE EN AI` | Genera JSX para crear el documento .ai con todas las mesas y capas |
| `⬇ EXTRAER DATOS DE SODIMAC` | Extrae datos de todas las URLs agregadas |
| `✦ JSX ILLUSTRATOR` | Genera el script para llenar capas en Illustrator |
| `✦ JSX PHOTOSHOP` | Genera el script para llenar capas en Photoshop |
| `📋 Pegar lista` | Modal para pegar múltiples URLs de una vez |

---

## Archivos del sistema

| Archivo | Descripción |
|---------|-------------|
| `index.html` | App principal (GitHub Pages) |
| `MTLKILLERS_EXT.zip` | Extensión de Chrome |
| `cards_CMR.svg` | SVG de cards CMR/Débito Falabella |

---

## Pendientes / Próximas mejoras

- [ ] Logos automáticos por marca desde carpeta local
- [ ] Corrección de tildes con API de Claude (más precisa)
- [ ] Soporte para precios por M² automático
- [ ] Preview visual de la card antes de exportar

---

## Repositorio

```
https://github.com/viejogxbo/mtlkillers
```

---

*MTLKILLERS V1 — Sistema de automatización retail*
*Desarrollado para Sodimac Perú → Adobe Illustrator / Photoshop*
