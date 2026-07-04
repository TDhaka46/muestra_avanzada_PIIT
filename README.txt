========================================================================
  SATELLITE-TRACK  ·  Maqueta de mantenimiento predictivo de flota
  Prototipo de tesis  ·  Automatizacion del proceso + demo de ML basico
========================================================================


1. QUE ES
------------------------------------------------------------------------
Aplicacion web de una sola pagina (SPA) que demuestra el proceso
automatizado de mantenimiento predictivo para transporte de personal
minero: telemetria -> deteccion -> prediccion -> orquestacion de taller
y almacen -> generacion automatica de Orden de Trabajo (OT).

Es un unico archivo HTML autocontenido. No requiere instalacion,
compilacion ni servidor.


2. COMO LEVANTAR LA APLICACION
------------------------------------------------------------------------
OPCION A (la mas simple):
  1. Haz doble clic en el archivo  SATELLITE-TRACK.html
  2. Se abre en tu navegador por defecto. Listo.

OPCION B (arrastrar):
  Arrastra  SATELLITE-TRACK.html  a una ventana del navegador.

OPCION C (servidor local, opcional, por si tu navegador restringe
archivos locales):
  - Con Python 3:   python3 -m http.server 8000
    (ejecutarlo dentro de la carpeta del proyecto)
    luego abre:     http://localhost:8000/SATELLITE-TRACK.html

Requisitos: cualquier navegador moderno (Chrome, Edge, Firefox, Safari).
Conexion a internet solo la primera vez, para cargar las fuentes y los
iconos desde CDN (Google Fonts y Tabler Icons). Sin internet igual
funciona, con tipografia e iconos del sistema.

Resolucion recomendada: escritorio 1280px de ancho o mas.


3. COMO USAR EL DEMO (recorrido sugerido)
------------------------------------------------------------------------
  a) Al abrir por primera vez aparece el ONBOARDING (5 pantallas) que
     explica el sistema y el uso del ML. Se puede reabrir con el boton
     "?" de la barra superior.

  b) DASHBOARD: muestra la flota, el RUL (vida util restante en horas),
     el estado y la prioridad de cada unidad.

  c) CONSOLA DE TELEMETRIA: boton "Consola Telemetria" abajo a la
     derecha. Ahi esta el modelo de ML:
       - Ingresa senales crudas (temperatura, presion, vibracion, horas)
       - Pulsa "Inferir con el modelo" -> calcula RUL + banda + confianza
         y autocompleta estado y prioridad
       - Pulsa "Inyectar Senal" -> el dashboard reacciona en tiempo real

  d) FLUJO PRESCRIPTIVO: en la fila BUS-089 pulsa "Ver" -> Consola
     Prescriptiva -> "Ejecutar Desvio" -> "Aplicar Cambio" -> se genera
     la Orden de Trabajo automaticamente.


4. QUE SE AGREGO EN ESTA VERSION
------------------------------------------------------------------------
Sobre la maqueta original (solo interfaz, datos fijos) se incorporo:

  [+] PANTALLAS DE ONBOARDING (5 pasos)
      Explican como funciona la plataforma y el uso del ML. Aparecen en
      el primer ingreso; se guardan en localStorage para no repetirse.
      Reabribles desde el boton "?" de la barra superior.

  [+] MODELO DE ML BASICO (corre en el navegador)
      Regresion lineal multiple entrenada al cargar la pagina, sobre
      ~220 muestras sinteticas de degradacion (minimos cuadrados en
      JavaScript puro, sin librerias).
      Entrada: temperatura, presion, vibracion, horas de operacion y
      numero de codigos de error activos.
      Salida: RUL estimado (horas) + banda de incertidumbre + confianza.
      Antes el operador tipeaba el RUL a mano; ahora el modelo lo infiere.

  [+] SENALES CRUDAS + BOTON "INFERIR CON EL MODELO" en la consola.

  [+] LECTURA DE RESULTADO del modelo (RUL, banda, confianza, RMSE).

  [+] Los eventos generados por el modelo se etiquetan como "ML base".

IMPORTANTE (honestidad academica): el modelo es EXPLORATORIO, entrenado
con datos sinteticos. Demuestra el concepto; NO es un sistema productivo.
La evolucion futura propuesta es una red LSTM con datos reales de sensores
servida por un microservicio. Ver ARQUITECTURA-Y-PROPUESTA-TESIS.md.


5. ARCHIVOS DEL ENTREGABLE
------------------------------------------------------------------------
  SATELLITE-TRACK.html ............ Aplicacion completa (doble clic)
  arquitectura-demo-ml.svg ........ Diagrama de arquitectura (para anexo)
  ARQUITECTURA-Y-PROPUESTA-TESIS.md  Arquitectura + textos para la tesis
  README.txt ...................... Este archivo
  SATELLITE-TRACK-context.md ...... Contexto/spec original del prototipo


6. NOTAS TECNICAS
------------------------------------------------------------------------
  - Stack: HTML + CSS + JavaScript vanilla. Sin frameworks ni build.
  - Toda la logica esta en el bloque <script> al final del <body>.
  - Todo el CSS esta en el bloque <style> del <head>.
  - El modelo de ML esta en el objeto "ML" dentro del <script>.
  - Tamano aproximado del archivo: 64 KB.

========================================================================
