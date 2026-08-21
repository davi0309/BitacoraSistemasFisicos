# Bitácora de proceso — Unidad 3: Instrumento de Fuerza
**Repositorio:** `unidad3-sim-v2`  

[Ver la página desplegada](https://davi0309.github.io/unidad3-sim-v2/)

[Ver repositorio en GitHub](https://github.com/davi0309/unidad3-sim-v2)
## 1. Propósito de la actividad

El propósito de esta unidad fue comprender y modificar una simulación de partículas ejecutada en GPU. Mi propuesta fue convertirla en un instrumento visual para acompañar una canción: cada tecla activa o combina fuerzas, colores, tamaños, formas y cámaras diferentes.

No busqué copiar referencias visuales literalmente. Tomé como inspiración la identidad visual de fenómenos espaciales y visuales abstractos: núcleos energéticos, anillos, discos de acreción, explosiones, galaxias y túneles de partículas, ademas de mandalas y otros.

## 2. Pregunta inicial e intención

La pregunta que guio mi trabajo fue:

> ¿Cómo puedo controlar partículas en tiempo real desde el teclado para construir imágenes mientras suena una canción?

Quise lograr que las teclas crearan cambios visibles entre estados; que las partículas conservaran una estructura, en vez de moverse como ruido aleatorio; que se pudieran combinar fuerzas y colores; y que existiera un modo de presentación limpio, sin interfaz.

## 3. Modelo de la simulación

```text
Teclado / mouse
        ↓
Parámetros y uniforms
        ↓
Compute shader en GPU
posición + velocidad → fuerzas → aceleración → nueva velocidad → nueva posición
        ↓
Buffers de partículas
        ↓
Render de sprites y color
```

El estado de cada partícula se guarda en dos buffers de GPU:

- `positionBuffer`: posición de la partícula.
- `velocityBuffer`: velocidad de la partícula.

Las partículas no se actualizan con un ciclo JavaScript por cada frame. Las fuerzas se calculan en paralelo en GPU.

## 4. Fuerzas utilizadas

| Fuerza | Idea física | Uso visual |
|---|---|---|
| Fuerza radial | Empuja hacia o desde un centro. | Atracción, repulsión, núcleo y explosión. |
| Vórtice | Fuerza perpendicular al radio. | Giro, órbitas, discos y galaxias. |
| Viento | Fuerza constante en una dirección. | Cometas, desplazamientos y estelas. |
| Drag o fricción | Fuerza opuesta a la velocidad. | Detener partículas y estabilizar formas. |
| Fuerza de forma objetivo | Guía cada partícula hacia una posición calculada en GPU. | Rayos, anillos, túneles, discos y estructuras reconocibles. |
| Pulso | Fuerza radial que alterna atracción y repulsión. | Beat visual con la tecla `G`. |

La fuerza radial usa un valor de suavizado (`softening`) para evitar que las partículas exploten cerca del centro.

## 5. Pruebas iniciales

Antes de construir las escenas visuales, se realizaron pruebas para aislar fuerzas.

| Prueba | Fuerza activa | Predicción | Resultado esperado |
|---|---|---|---|
| Inercia | Ninguna | Las partículas mantienen su movimiento inicial. | Movimiento sin aceleración adicional. |
| Viento | Viento +X | La velocidad horizontal aumenta. | Desplazamiento hacia la derecha. |
| Atracción | Fuerza radial positiva | Las partículas se acercan al centro. | Formación de núcleo. |
| Repulsión | Fuerza radial negativa | Las partículas se alejan del centro. | Expansión visible. |
| Vórtice | Fuerza tangencial | Las partículas giran alrededor del centro. | Órbitas y remolinos. |

## 6. Exploraciones visuales

### Figura radial / estrella de partículas

Esta exploración permitió observar una estructura de rayos que sale desde el centro. El resultado sirvió para diseñar la tecla `A`.

<img width="817" height="611" alt="imagen" src="https://github.com/user-attachments/assets/121d21d7-1b7b-4ad2-84a4-e3e3326eb524" />


**Observación:** al agrupar partículas en direcciones angulares discretas, la forma deja de parecer ruido y empieza a leerse como una estrella energética.

### Anillos orbitales

Se probaron anillos concéntricos para estudiar el efecto del vórtice y la fuerza objetivo.

<img width="536" height="429" alt="imagen" src="https://github.com/user-attachments/assets/57a0247b-3017-491f-a92c-76664f27c3d6" />


<img width="407" height="368" alt="imagen" src="https://github.com/user-attachments/assets/6a9832a2-736a-4af9-b317-be5bb0269f7b" />


**Observación:** los anillos se entienden mejor con una fuerza de forma objetivo, una rotación moderada y poca variación vertical.

### Líneas, ondas y estructura abstracta

También se exploraron trayectorias onduladas y composiciones con simetría.

<img width="804" height="612" alt="imagen" src="https://github.com/user-attachments/assets/be2396ef-f813-4b84-a02e-c2844c70d6e0" />


**Observación:** una estructura visual puede seguir moviéndose sin perder su identidad si la fuerza objetivo compensa el efecto del vórtice y del viento.

### Campo energético y nube de partículas

Esta prueba muestra un campo denso con concentración central.

<img width="770" height="610" alt="imagen" src="https://github.com/user-attachments/assets/791d2d91-6d5f-42ee-b194-01349ca92aa6" />

<img width="793" height="601" alt="imagen" src="https://github.com/user-attachments/assets/6bacbdc1-266a-465b-b210-335ca102a8ff" />


**Observación:** al disminuir el brillo aditivo se preserva mejor el color original de las partículas. Antes, las zonas con muchas partículas se volvían blancas porque los canales RGB se sumaban.

### Túnel verde y flujo orgánico

La tecla `H` se diseñó como una interpretación libre de un túnel de partículas: tendriles verdes, simetría radial y movimiento ondulante.

<img width="781" height="596" alt="imagen" src="https://github.com/user-attachments/assets/12107773-2ff6-4c0a-8705-af69cd6b7da3" />


**Observación:** la forma se construyó con rayos cuantizados y oscilación temporal, buscando filamentos coherentes en vez de una nube aleatoria.

### Disco de acreción y energía cálida

Se exploró una composición circular intensa para inspirar el disco de acreción de la tecla `D`.

<img width="613" height="474" alt="imagen" src="https://github.com/user-attachments/assets/fd685b3b-137f-453b-9dbf-42d3caa88005" />


**Observación:** el uso combinado de atracción suave, vórtice fuerte y un anillo objetivo permite formar un disco giratorio alrededor de una zona central menos poblada.

## 7. Controles implementados

| Tecla | Resultado visual | Fuerzas principales |
|---|---|---|
| `A` | Estrella de rayos azul/cian. | Forma radial, vórtice suave y atracción moderada. |
| `W` | Galaxia violeta. | Vórtice, atracción suave y brazos espirales. |
| `S` | Anillos planetarios violeta/magenta. | Tres anillos objetivo y rotación orbital. |
| `D` | Disco de acreción rojo/naranja. | Vórtice fuerte, atracción y anillo giratorio. |
| `F` | Cometa o campo estelar lento. | Viento direccional, vórtice suave y forma alargada. |
| `H` | Túnel verde orgánico. | Tendriles radiales y oscilación temporal. |
| `G` | Pulso rítmico. | Alterna atracción y repulsión. |
| `Z` | Pulso de tamaño pequeño. | Reduce y hace palpitar las partículas. |
| `X` | Arcoíris animado. | Reemplaza temporalmente la paleta de color. |
| `1` | Plano general lejano. | Cámara frontal. |
| `2` | Vista orbital elevada. | Cámara alta diagonal. |
| `3` | Vista lateral dramática. | Cámara baja y amplia. |
| `4` | Acercamiento cinematográfico. | Cámara cercana con zoom. |
| `P` | Presentación. | Oculta interfaz y solicita pantalla completa. |
| Clic derecho | Repulsión puntual. | Fuerza radial negativa desde el cursor. |

Las teclas `A`, `W`, `S`, `D`, `F` y `H` pueden combinarse. La última tecla presionada mantiene la estructura principal; las demás aportan color y fuerzas.

## 8. Problemas encontrados y decisiones tomadas

### Las partículas se veían demasiado aleatorias

**Decisión:** agregar fuerzas de forma objetivo calculadas en GPU.  
**Resultado:** las partículas pudieron organizarse como rayos, anillos, galaxias, discos y túneles.

### Las partículas se volvían blancas

**Causa:** el modo de mezcla aditiva sumaba los colores cuando muchas partículas se superponían.  
**Decisión:** cambiar a mezcla normal y usar paletas más saturadas.  
**Resultado:** se conservaron mejor azul, magenta, verde, naranja y arcoíris.

### El límite cuadrado rompía la ilusión espacial

**Decisión:** reemplazar el rebote en un cubo por un límite esférico grande.  
**Resultado:** las formas tienen más espacio y no desaparecen por los lados de un cubo.

### Las cámaras eran muy parecidas

**Decisión:** rediseñar las cámaras con cambios de posición, altura, objetivo y zoom.  
**Resultado:** cada vista cambia la lectura visual de las formas.

## 9. Resultado final

El resultado es un instrumento visual de partículas controlado desde el teclado. La obra no busca reproducir literalmente imágenes espaciales, sino traducir sus características visuales a reglas físicas y estructuras de partículas.

Se logró integrar:

- Fuerzas aislables en modo LAB.
- Controles expresivos para PERFORMANCE.
- Formas coherentes calculadas en GPU.
- Paletas de color saturadas.
- Cámara con diferentes encuadres.
- Pulso rítmico, cambio de tamaño y arcoíris.
- Repulsión puntual con clic derecho.
- Modo de presentación sin interfaz.

## 10. Reflexión final

Esta actividad me permitió entender que una simulación de partículas no depende solamente de “hacer que se vea bonita”. Para controlar el resultado fue necesario relacionar cada efecto visual con una fuerza, un parámetro y una predicción.

La parte más importante fue pasar de partículas aleatorias a partículas con estructura. La fuerza objetivo permitió mantener una forma reconocible, mientras el vórtice, la atracción, la repulsión y el viento aportaron movimiento.

También comprendí que el color, la cámara, el tamaño y la fricción son parte del instrumento: no son solo decoración, sino controles que cambian la forma en que se interpreta la simulación. 
