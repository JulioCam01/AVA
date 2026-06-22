# Tipos de Productos y Trabajos — AVA

Catálogo de referencia de los tipos de productos que vende y fabrica el negocio, con sus reglas de compra/venta, unidades y particularidades de inventario. Es la base para el catálogo de productos en la base de datos.

---

## Categorías Principales

| Categoría | Ejemplos | Tipo de precio | Unidad compra | Unidad venta | Inventario |
|---|---|---|---|---|---|
| Cristal | Claro 6mm, Filtrasol | Por dimensión | Hoja | m² | Por hoja + recortes |
| Perfil de aluminio | Jamba 3", Riel 2" | Por metro lineal | Tramo (6m/4.20m) | Metro | Por tramo individual |
| Empaque / Vinilo | Vinilo No.11 | Por metro lineal | Kg (rollo) | Metro | General (con factor) |
| Silicón | Silicón GE, Lanco | Por pieza | Caja (6/12/24 pzas) | Pieza | General (con factor) |
| Accesorio | Chapas, jaladeras, herrajes | Precio fijo | Pieza | Pieza | General |
| Ventana (fabricada) | Corrediza 3", manivela | Precio calculado | — | Pieza | No aplica directamente |
| Puerta (fabricada) | Tambor, baño, residencial | Precio manual | — | Pieza | No aplica directamente |
| Cancel (fabricado) | Cancel baño cristal 6mm | Precio manual | — | Pieza | No aplica |
| Domo (fabricado) | Domo vidrio 6mm | Precio manual | — | Pieza | No aplica |
| Barandal (fabricado) | Barandal vidrio templado | Precio manual | — | Pieza | No aplica |

---

## 1. Cristales

### Tipos y grosores

| Tipo | Grosor disponible |
|---|---|
| Claro | 3mm, 4mm, 6mm |
| Filtrasol (bronce) | 3mm, 4mm, 6mm |
| Tintex (gris) | 3mm, 4mm, 6mm |
| Sollite (reflectante) | 3mm, 4mm, 6mm |
| Reflecta | 3mm, 4mm, 6mm |
| Satinado blanco | 3mm, 4mm, 6mm |
| Satinado negro | 3mm, 4mm, 6mm |
| Templado claro | 6mm, 9mm |
| Laminado | 6mm, 9mm |

### Reglas de inventario

- Se compra en **hojas completas**: 180cm × 240cm ó 210cm × 240cm.
- Se vende por **metro cuadrado** → precio = (ancho_cm × alto_cm) / 10,000 × precio_m².
- Los recortes **≥ 10cm en ambas dimensiones y de corte recto** se guardan como recortes reutilizables con sus medidas exactas.
- Los recortes **< 10cm, curvos o de cortes fallidos** son desperdicio y no se registran.

---

## 2. Perfiles de Aluminio

### Tipos de perfil

| Perfil | Pulgadas disponibles |
|---|---|
| Riel (parte inferior del marco) | 2", 3", 4", 4.5" |
| Jamba (parte lateral del marco) | 2", 3", 4", 4.5" |
| Cerco chapa | — |
| Cerco traslape | — |
| Zoclo (perfil de la hoja corrediza) | 2", 3", 4", 4.5" |
| Bolsa | 2", 3" |
| Junquillo | — |
| Solera | — |
| Ángulo | — |
| Marco semilujo | — |
| Cuadrícula | — |
| 2x1 | — |
| 3x1 | — |
| 3x1/2 | — |
| Duela | — |

### Colores disponibles

- Natural (plateado)
- Blanco
- Madera (anodizado café)
- Negro

### Reglas de inventario

- Se compra y almacena en **tramos**: estándar de **6 metros** (600cm) y especial de **4.20 metros** (420cm).
- Cada tramo es una **unidad individual** de inventario con su propio estado.
- Se vende por **metro lineal**.
- Los sobrantes de corte se registran como **pedacería** (con longitud disponible medida).
- Estados de tramo: `disponible` → `apartado` → `consumido`.

---

## 3. Empaques (Vinilo)

### Tipos de empaque

| Tipo | Uso común |
|---|---|
| Vinilo No.11 | Empaque para ventanas corredizas estándar |
| Vinilo No.13 | Ventanas más anchas |
| Vinilo cola de borrego | Sello para puertas |
| Cepillo | Empaque tipo cepillo para hojas corredizas |

### Reglas de compra/venta

- Se compra en **kilogramos** (rollo).
- Se vende en **metros lineales**.
- El factor de conversión varía por tipo: aproximadamente 40 metros por kilogramo para Vinilo No.11 (confirmar con proveedor).
- Factor almacenado en `conversion_producto.factor_compra_venta` por producto.

---

## 4. Silicón

### Tipos

| Tipo | Presentación en caja |
|---|---|
| Silicón neutro blanco GE | 12 pzas / caja |
| Silicón neutro transparente GE | 12 pzas / caja |
| Silicón acetato blanco | 12 pzas / caja |
| Silicón acetato transparente | 12 pzas / caja |
| Silicón negro | 6 pzas / caja |
| Silicón marca Lanco | Variable |

### Reglas de compra/venta

- Se compra en **cajas** (6, 12 o 24 piezas según marca y tipo).
- Se vende por **pieza**.
- Factor de conversión = cantidad de piezas por caja (definido por producto en `conversion_producto`).

---

## 5. Accesorios y Herrajes

Productos de precio fijo, se compran y venden por pieza. Sin factor de conversión.

| Producto | Variantes |
|---|---|
| Chapas de baño (puerta madera) | — |
| Chapas de llave (puerta madera) | — |
| Chapas de ventana de aluminio | — |
| Jaladeras estándar | — |
| Jaladeras de acero inoxidable | — |
| Roletes (ruedas para ventanas corredizas) | — |
| Pivotes para ventanas de manivela | — |
| Bisagras | Piano, estándar |
| Tornillería | Por kit o por pieza |
| Herrajes para canceles | Distintos modelos |
| Herrajes para barandales de acero inox. | — |

---

## 6. Ventanas (Productos Fabricados)

Los precios de las ventanas fabricadas se calculan a partir de los materiales usados más la mano de obra.

### Tipos de ventanas corredizas (soportadas en la Calculadora de Despiece — MVP)

| Tipo | Medidas disponibles | Perfil principal |
|---|---|---|
| Ventana corrediza lisa de aluminio 2" | Variable (a medida) | Riel 2", Jamba 2", Zoclo 2" |
| Ventana corrediza lisa de aluminio 3" | Variable (a medida) | Riel 3", Jamba 3", Zoclo 3" |
| Ventana corrediza cuadritos de aluminio 3" | Variable (a medida) | Riel 3" + Cuadrícula |
| Ventana corrediza lisa madera (anodizado) | Variable (a medida) | Línea madera |
| Ventana corrediza cuadritos madera | Variable (a medida) | Línea madera + Cuadrícula |

### Tipos adicionales (líneas manuales en MVP, calculadora en V2)

- Ventana corrediza 4" y 4.5"
- Ventana de manivela (proyectante)
- Ventana fija de 2" y 3"
- Ventana Toscana (decorativa)

---

## 7. Puertas (Productos Fabricados — Líneas Manuales en MVP)

| Tipo | Descripción |
|---|---|
| Puerta de tambor con marcos de aluminio | Puerta interior con panel de MDF y marco de aluminio |
| Puerta de baño de aluminio | Hoja y marco de aluminio, vidrio o acrílico |
| Puerta residencial de aluminio | Puerta principal con vidrio |
| Puerta mosquitera | Marco de aluminio con malla |
| Puerta corredera (pátio) | Hoja deslizante, similar a ventana corrediza pero de tamaño de puerta |

---

## 8. Canceles de Baño (Productos Fabricados — Líneas Manuales en MVP)

| Tipo | Material principal |
|---|---|
| Cancel de baño aluminio y plástico | Marco de aluminio + hojas de plástico/acrílico |
| Cancel de baño aluminio y acrílico | Marco de aluminio + acrílico |
| Cancel de baño corredizo cristal templado 6mm | Marco de aluminio + cristal templado |
| Cancel de baño con herrajes 9mm | Sin marco (solo herrajes + cristal 9mm) |
| Cancel modelo Oaxaca 6mm | Diseño específico |
| Cancel de baño abatible | Bisagras + cristal templado |

---

## 9. Domos (Productos Fabricados — Líneas Manuales en MVP)

| Tipo | Material |
|---|---|
| Domo de vidrio plano 6mm | Cristal plano + estructura aluminio |
| Domo de vidrio templado 6mm | Cristal templado + estructura |
| Domo de vidrio templado 9mm | Para vanos más grandes |
| Domo piramidal | Estructura en ángulo, múltiples paneles |
| Domo de policarbonato | Material alternativo al vidrio |

---

## 10. Barandales (Productos Fabricados — Líneas Manuales en MVP)

| Tipo | Material |
|---|---|
| Barandal de vidrio templado | Pasamanos + vidrio sin marco visible |
| Barandal de acero inoxidable | Tubería de acero inoxidable |
| Barandal combinado (vidrio + acero inox.) | Cristal + barandal de acero |
| Barandal de aluminio con vidrio | Marco de aluminio + cristal |
| Barandal de aluminio simple | Sin vidrio |

---

## Resumen de Conversiones de Unidades

| Producto | Unidad compra | Unidad venta | Factor (ejemplo) | Tabla |
|---|---|---|---|---|
| Empaque Vinilo No.11 | kg | metro | ≈40 m/kg | conversion_producto |
| Silicón GE neutro blanco | caja (12 pzas) | pieza | 12 pzas/caja | conversion_producto |
| Cristal | hoja (180×240) | m² | Calculado por dimensión | historial_precios (precio/m²) |
| Perfil de aluminio | tramo (6m) | metro | 6 m/tramo | inventario_perfiles (por tramo) |

> Los factores de conversión se definen individualmente por producto en la tabla `conversion_producto`. No existe un factor global por categoría.
