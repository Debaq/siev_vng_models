# SIEV-VNG: detector de ojos para videonistagmografía

Modelo YOLOv8n de una clase (`OJO`) que encuentra la caja de cada ojo en
imágenes infrarrojas de gafas de videonistagmografía (VNG). Lo usa trainHIT
como alternativa experimental al centro del iris de MediaPipe: el modelo da
la caja del ojo y la pupila se toma como el centroide de los píxeles más
oscuros de adentro.

Desarrollado en el Laboratorio TecMedHub, Universidad Austral de Chile,
Sede Puerto Montt.

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.23106568.svg)](https://doi.org/10.5281/zenodo.23106568)

## Cómo citar

> Baier-Quezada N, Uribe-Hernández V, López-Moncada F, Catipillan-Ulloa B.
> SIEV-VNG: detector de ojos YOLOv8n para videonistagmografía [software].
> Versión r01. Zenodo; 2026. doi:10.5281/zenodo.23106568

Ese DOI es el de la versión r01. Para citar el modelo sin fijar versión:
doi:10.5281/zenodo.23106567.

## Archivos

| Archivo | Qué es | Tamaño | SHA-256 |
|---|---|---|---|
| `siev_vng_r01.pt` | Checkpoint de Ultralytics (pesos originales, fp16) | 6,2 MB | `d09fd03b0381039d3fe0b524276a1a23a9fd9a2a4c4006edb6e23c08133df42d` |
| `siev_vng_r01.onnx` | Exportado a ONNX para onnxruntime-web | 12,1 MB | `f3bea16dea1d47ae84e428755fb14b2c3350ca52bedccf54f5c2fd701c98c064` |
| `siev_vng_r01_metadata.txt` | Volcado de metadatos: arquitectura, argumentos de entrenamiento, métricas y datos del ONNX | | |

## Modelo

- Arquitectura: YOLOv8n (DetectionModel de Ultralytics), 3 011 043 parámetros.
- Clases: una, `0: OJO`.
- Entrenamiento: ajuste fino desde `yolov8n.pt` preentrenado, 30 épocas,
  imagen de 320 px, Ultralytics 8.3.78, 2025-02-24.
- Entrada ONNX: `images` `[1, 3, 320, 320]`, RGB normalizado a 0–1, con
  letterbox gris 114.
- Salida ONNX: `output0` `[1, 5, 2100]` (cx, cy, w, h, confianza), sin NMS.
- Opset 18, exportado con Ultralytics 8.4.171.

Las imágenes de entrenamiento son de gafas VNG: los dos ojos de cerca, en
infrarrojo y gris, con cuadro de 424×240. Para usarlo con otra cámara hay
que recortar imitando ese encuadre (ver `ENCUADRE` en trainHIT).

## Métricas de validación

| Métrica | Valor |
|---|---|
| Precisión | 0,989 |
| Exhaustividad | 0,985 |
| mAP@50 | 0,994 |
| mAP@50–95 | 0,674 |

Son las del conjunto de validación del entrenamiento, no una validación
clínica. El modelo es experimental y no es un dispositivo médico.

## Licencia

AGPL-3.0 (ver `LICENSE`). El modelo deriva de los pesos preentrenados de
Ultralytics YOLOv8, que se distribuyen bajo AGPL-3.0, y hereda esa licencia.
Por eso se publica aparte de trainHIT, cuyo código es Apache-2.0 y descarga
el modelo solo cuando se elige.

## Publicar en el servidor

trainHIT lo baja de `https://tecmedhub.org/siev-vng/siev_vng_r01.onnx`. Se
sube a esa carpeta `siev_vng_r01.onnx` junto con `.htaccess`, que agrega la
cabecera CORS: la página corre en otro origen y sin ella el navegador
bloquea la descarga.
