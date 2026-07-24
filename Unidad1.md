# Ecos del Océano

## Descripción

Consulté diferentes eventos y exposiciones relacionadas con el océano, y la que más llamó mi atención fue **"Ecos del Océano"**, ya que invita a reflexionar sobre la contaminación de los mares. Sin embargo, su enfoque no se limita únicamente a la contaminación por residuos, sino que también aborda la **contaminación acústica**.

Uno de los pilares principales de esta exposición es demostrar cómo los cetáceos (ballenas, delfines y otros mamíferos marinos) perciben su entorno y cómo el ruido generado por las actividades humanas actúa como un contaminante que altera su comportamiento. La exposición presenta estudios que respaldan la idea de que el ruido producido por embarcaciones, sonares y otras fuentes humanas afecta la forma en que estos animales se orientan, se comunican y perciben su entorno.



# Interacciones planteadas

## Posibilidad

Al comienzo de la experiencia, las partículas se mueven mediante una **turbulencia** generada con **ruido Perlin**, creando un comportamiento orgánico e impredecible.

## Tendencia

Ocurre cuando las partículas son atraídas hacia puntos de referencia al cargar una imagen. Este comportamiento funciona a partir del brillo de los píxeles: las zonas con mayor luminosidad atraen una mayor cantidad de partículas, permitiendo reconstruir la imagen.

## Normalidad

Este estado ocurre cuando no se genera ruido externo. La mayoría de las partículas permanecen en su posición asignada, permitiendo que la imagen se reconstruya con un alto nivel de fidelidad.

## Excepción

La excepción puede observarse al inicio de la experiencia y también durante la carga de una imagen, cuando una partícula rompe su patrón habitual de movimiento. Este comportamiento se produce mediante un **vuelo de Lévy**, representado visualmente por una onda de color dorado.

## Influencia

La influencia ocurre cuando el usuario introduce un sonido o genera ruido durante la experiencia. Esta acción altera el comportamiento de todo el sistema de partículas, simulando el efecto que tiene la contaminación acústica sobre los ecosistemas marinos y la forma en que los cetáceos perciben su entorno.

# Evidencias de iteraciones

<img width="1917" height="842" alt="imagen" src="https://github.com/user-attachments/assets/e8b4ef28-8946-4d9f-b39f-1431b8f0536c" />

Aqui tuve varios problemas ya que comence utilizando la IA de Claude y al comieno queria solo crear un sistema de particulas que reconociera una imagen, para que se pareciera a la exposición de Ecos del oceano, pero hubo un problema grande que era que cada vez que subia una imagen nunca se imitaba la imagen.
Así que tome la decición de pasarme a la IA de gemini y de aqui en adelante todo funciono mucho mejor ya que anteriormente tambien plantee algunos prompts para que funcionara la influencia que la pense como el microfono y que cuando se hiciera ruido las particulas rompian su sistema de movimiento probabilistico y su mapa de probabilidad y cuando volviera en silencio las particulas volvian a su movimiento normalizado.

## Codigo de Claude
```html
<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<title>Ecos del Océano</title>
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
<link rel="stylesheet" href="style.css">
</head>
<body>

<!-- Panel de control: discreto, esquina superior izquierda -->
<div id="ui">

  <label class="file-btn">
    Subir imagen (mapa perceptual)
    <input type="file" id="imageInput" accept="image/*">
  </label>

  <label class="file-btn">
    Subir audio propio
    <input type="file" id="audioInput" accept="audio/*">
  </label>

  <div class="toggle-group">
    <button id="useMicBtn" class="toggle-btn active" type="button">Micrófono</button>
    <button id="useFileBtn" class="toggle-btn" type="button">Archivo subido</button>
  </div>

  <button id="startBtn" class="start-btn" type="button">Activar audio</button>

  <div id="status">esperando imagen y audio…</div>
</div>

<!-- p5.js y p5.sound desde CDN -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.9.4/p5.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.9.4/addons/p5.sound.min.js"></script>
<script src="sketch.js"></script>
</body>
</html>
```
```java
/* ============================================================
   ECOS DEL OCÉANO
   ------------------------------------------------------------
   Un sistema de partículas intenta reconstruir, de forma
   probabilística, una imagen subida por el usuario (interpretada
   como un mapa perceptual — traducción visual artística, no
   biológica literal, de lo que "percibe" un cetáceo).

   El audio (micrófono o archivo subido) funciona como variable
   de contaminación acústica: cuanta más energía tiene, más se
   degrada la fidelidad del sistema respecto a la imagen.

   Conceptos matemáticos combinados en el MISMO modelo de
   movimiento (no en funciones aisladas):
     - Distribución de probabilidad -> el destino de cada
       partícula se sortea a partir del brillo de los píxeles
       de la imagen (probabilityMap).
     - Ruido Perlin -> campo de flujo continuo de base, siempre
       presente (sustrato de "posibilidad").
     - Caminata aleatoria correlacionada -> la velocidad de cada
       partícula se actualiza mezclando su valor anterior con las
       fuerzas nuevas (lerp), nunca saltos discretos.
     - Distribución normal -> jitter angular (randomGaussian),
       su sigma se controla con la energía de audio.
     - Lévy flight -> saltos largos de cola pesada, cuya
       probabilidad sube con el audio sostenido y con picos
       abruptos de volumen.

   Mapeo interno de los 5 momentos (comentarios, no texto visible):
     - posibilidad: sin imagen cargada, o antes de cualquier
       influencia -> solo ruido Perlin, todas las direcciones
       parecen viables.
     - tendencia: la atracción hacia el probabilityMap se
       refuerza mientras el audio se mantiene bajo.
     - normalidad: sigma angosta -> la mayoría de las
       trayectorias quedan cerca de su destino asignado.
     - excepción: picos de audio disparan Lévy flight -> una
       partícula rompe su patrón y explora territorio ajeno
       a la imagen.
     - influencia: el audio nunca dibuja nada directamente,
       solo altera sigma, el peso de atracción y la
       probabilidad de salto.
   ============================================================ */

// ---------- pantalla completa: el canvas ocupa toda la ventana ----------
// (nota: el enunciado original pedía 9:16; este cambio prioriza ver la
// imagen reconocible a tamaño completo de escritorio, a pedido explícito)

// ---------- sistema de partículas ----------
const N_PARTICLES = 3500;
let particles = [];

// ---------- mapa de probabilidad derivado de la imagen ----------
const MAP_COLS_BASE = 100; // resolución base; las filas se ajustan al aspecto real de la ventana
let MAP_COLS = MAP_COLS_BASE;
let MAP_ROWS = MAP_COLS_BASE;
let cellW = 1, cellH = 1;
let cumulativeWeights = null; // prefix sums para muestreo ponderado
let totalWeight = 0;
let probabilityMapReady = false;
let colorGrid = null; // [h, s, b] por celda, tomado del color real de la imagen
let lastLoadedImage = null; // se conserva para reconstruir el mapa si la ventana cambia de tamaño

// ---------- audio ----------
let mic;
let soundFile = null;
let amp;                 // p5.Amplitude, analiza la fuente activa
let audioSource = "mic";  // "mic" | "file"
let audioReady = false;
let audioEnergy = 0;      // 0..1, normalizado y suavizado
let smoothedEnergy = 0;
let prevSmoothedEnergy = 0;
let energyDelta = 0;
const MIC_GAIN = 3.2; // el nivel crudo del mic suele ser bajo; se amplifica

// ---------- parámetros del modelo (ajustables) ----------
// Modelo de movimiento: cada partícula es atraída (tipo resorte) hacia su
// destino asignado en la imagen -> esto es lo que la hace reconocible.
// Sobre esa atracción se superponen: una onda (oleaje) siempre presente,
// turbulencia de ruido Perlin, jitter de distribución normal, y saltos
// de Lévy poco frecuentes. El audio degrada la fidelidad de la atracción.
const MAX_SPEED = 4.2;            // velocidad máxima de acercamiento al destino
const VELOCITY_SMOOTH = 0.07;     // memoria de la velocidad -> caminata correlacionada
const WAVE_FREQ = 0.55;           // frecuencia del oleaje
const WAVE_AMPLITUDE = 22;        // amplitud del oleaje en píxeles
const CURRENT_STRENGTH = 0.35;    // turbulencia Perlin ambiental, siempre presente
const SIGMA_MIN = 0.15;           // jitter (distribución normal) en silencio, en píxeles
const SIGMA_MAX = 6.5;            // jitter con audio alto -> se pierde fidelidad
const LEVY_BASE = 0.0006;
const LEVY_MAX = 0.012;
const LEVY_SPIKE_BOOST = 0.06; // refuerzo extra ante picos abruptos de volumen
const LEVY_CAP = 0.06;
const PARTICLE_MIN_SIZE = 2;   // partículas de tamaños variados (algunas chicas...
const PARTICLE_MAX_SIZE = 16;  // ...otras grandes), asignado por partícula, no solo por atracción

// ---------- eventos de excepción (Lévy flight) ----------
let pings = [];

// ---------- rastro persistente ----------
let trailLayer;

// ---------- tiempo interno (evoluciona solo: onda + ruido Perlin) ----------
let t = 0;

function setup() {
  let cnv = createCanvas(windowWidth, windowHeight);
  cnv.parent(document.body);
  updateGridDimensions();

  // semilla nueva cada ejecución -> variación sin perder identidad
  let seed = floor(random(1000000));
  randomSeed(seed);
  noiseSeed(seed);

  colorMode(HSB, 1, 1, 1, 1);
  noStroke();

  trailLayer = createGraphics(width, height);
  trailLayer.colorMode(HSB, 1, 1, 1, 1);
  trailLayer.background(0.6, 0.55, 0.025, 1);

  for (let i = 0; i < N_PARTICLES; i++) {
    particles.push({
      x: random(width), y: random(height),
      vx: 0, vy: 0,
      destX: null, destY: null,
      // tamaño y fase de onda propios de cada partícula -> variedad visual
      // y desfase para que el oleaje no se vea sincronizado/artificial
      baseSize: random(PARTICLE_MIN_SIZE, PARTICLE_MAX_SIZE),
      wavePhase: random(TWO_PI),
      noiseSeedX: random(1000)
    });
  }

  setupAudioObjects();
  bindUI();
}

function windowResized() {
  resizeCanvas(windowWidth, windowHeight);
  updateGridDimensions();

  // se recrea el rastro al nuevo tamaño (se pierde el rastro previo)
  trailLayer = createGraphics(width, height);
  trailLayer.colorMode(HSB, 1, 1, 1, 1);
  trailLayer.background(0.6, 0.55, 0.025, 1);

  // si ya había una imagen cargada, se reconstruye el mapa con la
  // proporción correcta de la nueva ventana en vez de estirar el viejo
  if (lastLoadedImage) buildProbabilityMap(lastLoadedImage, 'imagen (reescalada)');
}

function updateGridDimensions() {
  MAP_COLS = MAP_COLS_BASE;
  MAP_ROWS = max(20, round(MAP_COLS_BASE * (height / width)));
  cellW = width / MAP_COLS;
  cellH = height / MAP_ROWS;
}

// ============================================================
// AUDIO
// ============================================================

function setupAudioObjects() {
  mic = new p5.AudioIn();
  amp = new p5.Amplitude();
  amp.setInput(mic); // por defecto, mic (aunque no arrancado hasta "Activar audio")
}

function bindUI() {
  document.getElementById('imageInput').addEventListener('change', onImageSelected);
  document.getElementById('audioInput').addEventListener('change', onAudioFileSelected);
  document.getElementById('startBtn').addEventListener('click', onStartAudio);
  document.getElementById('useMicBtn').addEventListener('click', () => setAudioSource('mic'));
  document.getElementById('useFileBtn').addEventListener('click', () => setAudioSource('file'));
}

function onStartAudio() {
  // el navegador exige un gesto explícito del usuario para iniciar audio
  userStartAudio().then(() => {
    if (audioSource === 'mic') {
      mic.start(() => {
        audioReady = true;
        setStatus('micrófono activo');
      }, () => {
        setStatus('no se pudo acceder al micrófono');
      });
    } else if (soundFile) {
      soundFile.loop();
      audioReady = true;
      setStatus('reproduciendo audio subido');
    } else {
      setStatus('sube un archivo de audio primero, o cambia a micrófono');
    }
  });
}

function setAudioSource(source) {
  audioSource = source;
  document.getElementById('useMicBtn').classList.toggle('active', source === 'mic');
  document.getElementById('useFileBtn').classList.toggle('active', source === 'file');

  if (source === 'mic') {
    if (soundFile && soundFile.isPlaying()) soundFile.stop();
    amp.setInput(mic);
    if (audioReady) mic.start();
  } else {
    mic.stop();
    if (soundFile) {
      amp.setInput(soundFile);
      if (audioReady) soundFile.loop();
    }
  }
}

function onAudioFileSelected(e) {
  let file = e.target.files[0];
  if (!file) return;
  let url = URL.createObjectURL(file);
  setStatus('cargando audio…');
  soundFile = loadSound(url, () => {
    setStatus('audio propio cargado (' + file.name + ')');
    if (audioSource === 'file') {
      amp.setInput(soundFile);
      if (audioReady) soundFile.loop();
    }
  });
}

function updateAudioEnergy() {
  if (!audioReady) {
    audioEnergy = 0;
    return;
  }
  let raw = amp.getLevel(); // 0..~0.4 típicamente
  let normalized = constrain(raw * MIC_GAIN, 0, 1);
  smoothedEnergy = lerp(smoothedEnergy, normalized, 0.15);
  energyDelta = max(0, smoothedEnergy - prevSmoothedEnergy);
  prevSmoothedEnergy = smoothedEnergy;
  audioEnergy = smoothedEnergy;
}

// ============================================================
// IMAGEN -> MAPA DE PROBABILIDAD
// ============================================================

function onImageSelected(e) {
  let file = e.target.files[0];
  if (!file) return;
  let url = URL.createObjectURL(file);
  setStatus('procesando imagen…');
  loadImage(url, (img) => {
    lastLoadedImage = img;
    buildProbabilityMap(img, file.name);
  });
}

function buildProbabilityMap(img, name) {
  // se reescala la imagen a la resolución de la grilla de muestreo,
  // que ahora se ajusta a la proporción real de la ventana (no 9:16 fijo)
  let pg = createGraphics(MAP_COLS, MAP_ROWS);
  pg.image(img, 0, 0, MAP_COLS, MAP_ROWS);
  pg.loadPixels();

  let weights = new Array(MAP_COLS * MAP_ROWS);
  colorGrid = new Array(MAP_COLS * MAP_ROWS);
  let sum = 0;

  for (let j = 0; j < MAP_ROWS; j++) {
    for (let i = 0; i < MAP_COLS; i++) {
      let idx = (j * MAP_COLS + i) * 4;
      let r = pg.pixels[idx], g = pg.pixels[idx + 1], b = pg.pixels[idx + 2];
      let brightness = (r + g + b) / 3 / 255; // 0..1
      // más oscuro/marcado = mayor probabilidad de ser destino
      let w = pow(1 - brightness, 2) + 0.01; // +0.01 evita ceros absolutos
      weights[j * MAP_COLS + i] = w;
      sum += w;

      // color real de la celda, convertido a HSB para pintar partículas
      colorGrid[j * MAP_COLS + i] = rgbToHsb(r, g, b);
    }
  }

  cumulativeWeights = new Array(weights.length);
  let acc = 0;
  for (let k = 0; k < weights.length; k++) {
    acc += weights[k];
    cumulativeWeights[k] = acc;
  }
  totalWeight = acc;
  probabilityMapReady = true;

  // fuerza a todas las partículas a re-sortear su destino con el nuevo mapa
  for (let p of particles) { p.destX = null; p.destY = null; }

  setStatus('imagen cargada (' + name + ') — reconstruyendo patrón');
}

function sampleDestination() {
  // muestreo ponderado por búsqueda binaria sobre la suma acumulada:
  // esto ES la distribución de probabilidad actuando como regla de
  // movimiento, no como decoración visual.
  let r = random(totalWeight);
  let lo = 0, hi = cumulativeWeights.length - 1;
  while (lo < hi) {
    let mid = (lo + hi) >> 1;
    if (cumulativeWeights[mid] < r) lo = mid + 1; else hi = mid;
  }
  let col = lo % MAP_COLS;
  let row = floor(lo / MAP_COLS);
  return {
    x: (col + 0.5) * cellW + random(-cellW * 0.4, cellW * 0.4),
    y: (row + 0.5) * cellH + random(-cellH * 0.4, cellH * 0.4)
  };
}

function assignDestination(p) {
  if (!probabilityMapReady) return;
  let d = sampleDestination();
  p.destX = d.x; p.destY = d.y;
}

function rgbToHsb(r, g, b) {
  // conversión estándar RGB(0..255) -> HSB(0..1), para que coincida
  // con el colorMode(HSB,1,1,1,1) usado en trailLayer
  r /= 255; g /= 255; b /= 255;
  let maxV = max(r, g, b), minV = min(r, g, b);
  let delta = maxV - minV;
  let h = 0;
  if (delta > 0.0001) {
    if (maxV === r) h = ((g - b) / delta) % 6;
    else if (maxV === g) h = (b - r) / delta + 2;
    else h = (r - g) / delta + 4;
    h /= 6;
    if (h < 0) h += 1;
  }
  let s = maxV === 0 ? 0 : delta / maxV;
  let brightness = maxV;
  return [h, s, brightness];
}

function colorAtPosition(x, y) {
  // busca la celda del colorGrid correspondiente a una posición del canvas
  let col = constrain(floor(x / cellW), 0, MAP_COLS - 1);
  let row = constrain(floor(y / cellH), 0, MAP_ROWS - 1);
  return colorGrid[row * MAP_COLS + col];
}

function colorAtDestination(p) {
  return colorAtPosition(p.destX, p.destY);
}

// ============================================================
// LÉVY FLIGHT
// ============================================================

function levyStep(minStep, maxStep, alpha) {
  let u = random(0.0008, 1);
  let step = minStep / pow(u, 1 / alpha);
  return min(step, maxStep);
}

function angleLerp(a, b, amt) {
  let diff = ((b - a + PI) % TWO_PI + TWO_PI) % TWO_PI - PI;
  return a + diff * amt;
}

// ============================================================
// LOOP PRINCIPAL
// ============================================================

function draw() {
  background(0);
  t += 0.0035;

  updateAudioEnergy();
  updateParticles();

  trailLayer.noStroke();
  trailLayer.fill(0.6, 0.5, 0.02, 0.05); // desvanecido leve del rastro
  trailLayer.rect(0, 0, width, height);

  image(trailLayer, 0, 0, width, height);
  drawPings();
}

function updateParticles() {
  for (let p of particles) {

    let attractionWeight = 0;
    let ax = 0, ay = 0; // aceleración deseada hacia el destino (resorte)

    if (probabilityMapReady) {
      if (p.destX === null) assignDestination(p);

      let dx = p.destX - p.x, dy = p.destY - p.y;
      let d = sqrt(dx * dx + dy * dy);

      // el audio degrada la atracción hacia la imagen -> "contaminación"
      attractionWeight = constrain(1 - audioEnergy * 1.15, 0, 1);

      if (d > 0.5) {
        // velocidad deseada: se frena suavemente al acercarse (fácil de leer,
        // sin overshoot violento) y escala con la fuerza de atracción
        let desiredSpeed = min(d * 0.05, MAX_SPEED) * attractionWeight;
        ax = (dx / d) * desiredSpeed;
        ay = (dy / d) * desiredSpeed;
      }

      // al llegar cerca de su destino, ocasionalmente re-sortea uno nuevo:
      // mantiene el sistema vivo y en variación continua
      if (d < 4 && random() < 0.006) assignDestination(p);
    }

    // --- oleaje: siempre presente, más notorio cuando hay poca atracción
    // (posibilidad/excepción) y sutil cuando el patrón está asentado
    // (normalidad). Esto reemplaza el arrastre direccional anterior por
    // un vaivén sin dirección neta acumulada.
    let waveInfluence = lerp(1, 0.25, attractionWeight);
    let waveX = sin(t * WAVE_FREQ + p.wavePhase) * WAVE_AMPLITUDE * waveInfluence * 0.02;
    let waveY = cos(t * WAVE_FREQ * 0.8 + p.wavePhase) * WAVE_AMPLITUDE * waveInfluence * 0.03;

    // --- ruido Perlin: turbulencia ambiental, siempre presente (funciona
    // aunque nadie interactúe) -> sustrato de "posibilidad"
    let n = noise(p.noiseSeedX, t * 0.6);
    let currentAngle = n * TWO_PI * 2;
    let currentStrength = CURRENT_STRENGTH * lerp(1, 2.4, audioEnergy);
    let curX = cos(currentAngle) * currentStrength;
    let curY = sin(currentAngle) * currentStrength;

    // --- caminata aleatoria correlacionada: la velocidad se actualiza
    // mezclando su valor anterior con las fuerzas nuevas (memoria corta)
    p.vx = lerp(p.vx, ax + waveX + curX, VELOCITY_SMOOTH);
    p.vy = lerp(p.vy, ay + waveY + curY, VELOCITY_SMOOTH);

    // --- distribución normal: jitter de posición, sigma gobernada por audio
    let sigma = lerp(SIGMA_MIN, SIGMA_MAX, audioEnergy);
    let jitterX = randomGaussian() * sigma;
    let jitterY = randomGaussian() * sigma;

    // --- Lévy flight: probabilidad sube con audio sostenido y con picos ---
    let pLevy = constrain(
      lerp(LEVY_BASE, LEVY_MAX, audioEnergy) + energyDelta * LEVY_SPIKE_BOOST,
      0, LEVY_CAP
    );
    let isException = random() < pLevy;
    let levyX = 0, levyY = 0;
    if (isException) {
      let step = levyStep(16, 200, 1.5);
      let ang = random(TWO_PI);
      levyX = cos(ang) * step;
      levyY = sin(ang) * step;
      pings.push({ x: p.x, y: p.y, age: 0 });
    }

    p.x += p.vx + jitterX + levyX;
    p.y += p.vy + jitterY + levyY;

    // mundo toroidal (solo relevante sin imagen cargada o tras un salto grande)
    if (p.x < 0) p.x += width; if (p.x > width) p.x -= width;
    if (p.y < 0) p.y += height; if (p.y > height) p.y -= height;

    // --- color: se toma del destino asignado (estable), no de la posición
    // instantánea -> evita que el oleaje/jitter mezcle colores al pasar
    let hue, sat, bright;
    if (probabilityMapReady && colorGrid && p.destX !== null) {
      let c = colorAtDestination(p);
      hue = c[0];
      sat = constrain(c[1] * 1.15, 0, 1);
      bright = constrain(c[2] * 0.6 + 0.3, 0, 1);
    } else {
      hue = 0.58; sat = 0.5;
      bright = lerp(0.3, 0.95, attractionWeight);
    }
    if (isException) { hue = 0.13; sat = 0.7; } // el destello de excepción siempre resalta en dorado

    trailLayer.fill(hue, sat, bright, 0.7);
    let size = p.baseSize * lerp(0.6, 1.15, attractionWeight);
    if (isException) size *= 1.5;
    trailLayer.ellipse(p.x, p.y, size);
  }
}

function drawPings() {
  noFill();
  for (let i = pings.length - 1; i >= 0; i--) {
    let pg = pings[i];
    pg.age += 1;
    let life = pg.age / 50;
    if (life >= 1) { pings.splice(i, 1); continue; }
    stroke(0.13, 0.6, 1, (1 - life) * 0.8);
    strokeWeight(1.6);
    ellipse(pg.x, pg.y, life * 90);
  }
  noStroke();
}

function setStatus(msg) {
  let el = document.getElementById('status');
  if (el) el.innerText = msg;
}
```
```css
html, body {
  margin: 0;
  padding: 0;
  overflow: hidden;
  width: 100%;
  height: 100%;
  background: #010308;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
}

canvas {
  display: block;
  touch-action: none;
}

/* ---------- panel de control ---------- */

#ui {
  position: fixed;
  top: 14px;
  left: 14px;
  z-index: 20;
  display: flex;
  flex-direction: column;
  gap: 8px;
  padding: 12px 14px;
  background: rgba(6, 14, 28, 0.55);
  border: 1px solid rgba(120, 180, 255, 0.15);
  border-radius: 10px;
  backdrop-filter: blur(6px);
  max-width: 220px;
}

.file-btn {
  position: relative;
  display: inline-block;
  padding: 7px 10px;
  font-size: 12px;
  color: rgba(190, 220, 255, 0.85);
  background: rgba(120, 180, 255, 0.08);
  border: 1px solid rgba(120, 180, 255, 0.25);
  border-radius: 6px;
  cursor: pointer;
  text-align: center;
  transition: background 0.2s ease;
}

.file-btn:hover {
  background: rgba(120, 180, 255, 0.18);
}

.file-btn input[type="file"] {
  position: absolute;
  inset: 0;
  opacity: 0;
  cursor: pointer;
}

.toggle-group {
  display: flex;
  gap: 4px;
}

.toggle-btn {
  flex: 1;
  padding: 6px 4px;
  font-size: 11px;
  color: rgba(160, 200, 255, 0.6);
  background: rgba(255, 255, 255, 0.03);
  border: 1px solid rgba(120, 180, 255, 0.15);
  border-radius: 5px;
  cursor: pointer;
}

.toggle-btn.active {
  color: rgba(200, 230, 255, 0.95);
  background: rgba(120, 180, 255, 0.25);
  border-color: rgba(160, 210, 255, 0.5);
}

.start-btn {
  padding: 8px 10px;
  font-size: 12px;
  color: rgba(255, 240, 200, 0.9);
  background: rgba(255, 200, 90, 0.12);
  border: 1px solid rgba(255, 200, 90, 0.35);
  border-radius: 6px;
  cursor: pointer;
}

.start-btn:hover {
  background: rgba(255, 200, 90, 0.22);
}

#status {
  font-size: 10.5px;
  color: rgba(140, 180, 220, 0.55);
  line-height: 1.4;
  margin-top: 2px;
}
```

## Utilizando la IA de Gemini

Con esta IA pude conversar mucho más y mas fluido, además de que entendiera mucho más la idea que tenia en mente y así poder plasmarla mas facilmente

### Resultados
<img width="1917" height="916" alt="imagen" src="https://github.com/user-attachments/assets/0b89a8be-dab4-4730-a6e9-675656b1233b" />

<img width="1916" height="917" alt="imagen" src="https://github.com/user-attachments/assets/bef8a20f-6c53-41ae-83d9-44c4ffdd0ccb" />

Ademas de cuando ponia algun sonido precargado o activaba el microfono para gritar se podia ver el efecto que este hacia:

<img width="1917" height="916" alt="imagen" src="https://github.com/user-attachments/assets/7ea6d790-8ed6-400a-b6e6-2b5bdc9fde6b" />

Y asi pude llegar a mi resultado final



