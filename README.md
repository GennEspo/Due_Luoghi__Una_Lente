# Due Luoghi, Una Lente

SUPSI 2025\
Corso d'interaction design, CV428.01\
Docenti: A. Gysin, G. Profeta

Elaborato 1: Visualizzazione dati fotografici\
Autore: Gennaro Esposito\
🔗 [Due Luoghi, Una Lente](INSERISCI_LINK_GITHUB_PAGES)

---

## Introduzione e tema

**Due Luoghi, Una Lente** è un sito interattivo che esplora l'evoluzione del mio sguardo fotografico attraverso due periodi della mia vita: **Napoli (2017–2022)** e **Ticino (dal 2022 a oggi)**.\
Attraverso l'analisi dei metadati tecnici di oltre 4.000 immagini (ISO, tempo di esposizione, tinta dominante, luminosità e emozione), il progetto costruisce una narrazione visuale immersiva e comparativa.\
L'obiettivo è trasformare dati tecnici in esperienze esplorabili e significative, mostrando come i numeri possano raccontare cambiamenti interiori e ambientali.

---

## Riferimenti progettuali

Il progetto si basa su:

- Metadati EXIF estratti dalle fotografie personali
- Analisi cromatiche ed emozionali su immagini selezionate
- Sistemi di rappresentazione sferica e comparazione visiva
- Approcci ispirati a creative coding e data visualization

---

## Design dell'interfaccia e modalità di interazione

L'interfaccia si sviluppa in due fasi principali:

### 1. **Esplorazione generale**

Una sfera 3D dinamica rappresenta tutte le fotografie aggregate. Ogni tubo visualizzato nello spazio è generato in base alla tinta dominante (Hue), luminosità e tempo di esposizione dello scatto.\
L'utente può ruotare la sfera, zoomare e cliccare per vedere i dettagli.

### 2. **Modalità di confronto**

Una sezione dedicata permette di confrontare visivamente i due periodi. Sono disponibili le seguenti visualizzazioni comparate:

- **Numero di foto al mese** → luce pulsante proporzionale alla frequenza
- **ISO medio** → reso visivamente come grana (rumore digitale)
- **Tempo di esposizione** → simulato tramite movimento e nitidezza
- **Hue dominante** → distribuzione cromatica segmentata su sfera
- **Emozioni percepite** → sfera colorata divisa in percentuali emozionali

Le etichette dinamiche e il box testuale contestualizzano ogni confronto.

---

## Tecnologia usata

- **HTML, CSS, JavaScript**
- **Three.js** per visualizzazioni e interazione 3D
- Dataset `.json` derivati da metadati e analisi esterne
- Transizioni fluide e animazioni con sinusoidi, jitter, rotazioni

### 📌 Mappatura sferica dei tubi fotografici (visualizzazione generale)

```js
const theta = THREE.MathUtils.degToRad(hsl.h);
const phi = brightness * Math.PI;
const r = rMin + Math.log10(1 / (exposure + 0.0001)) * 100;

const x = r * Math.sin(phi) * Math.cos(theta);
const y = r * Math.cos(phi);
const z = r * Math.sin(phi) * Math.sin(theta);
```

Ogni fotografia viene rappresentata come un tubo 3D nello spazio. La tinta dominante determina la direzione angolare, la luminosità l'inclinazione, e il tempo di esposizione la distanza dal centro. Questo crea una "costellazione" tridimensionale ordinata ma organica.

### 🔢 Analisi emozionale (pre-processing)

Per estrarre le emozioni dalle immagini, è stato utilizzato uno script Python con la libreria `fer`:

```python
from fer import FER
import cv2, os, json

detector = FER(mtcnn=True)
results = []

for filename in os.listdir("immagini"):
    if filename.endswith(".jpg"):
        img = cv2.imread(os.path.join("immagini", filename))
        emotion = detector.top_emotion(img)
        if emotion:
            results.append({
                "filename": filename,
                "emotion": emotion[0],
                "confidence": emotion[1]
            })

with open("emotion_analysis_results.json", "w") as f:
    json.dump(results, f, indent=2)
```

Questo file JSON viene poi utilizzato nella modalità di confronto del sito.

### 🎩 Rappresentazione emozionale (modalità di confronto)

```js
emotions.forEach(e => {
  const fraction = percents[e];
  const thetaStart = start * Math.PI * 2;
  const thetaLength = fraction * Math.PI * 2;
  start += fraction;

  const geometry = new THREE.SphereGeometry(radius, 64, 64, thetaStart, thetaLength, 0, Math.PI);
  const material = new THREE.MeshBasicMaterial({ color: emotionColors[e] });
  const mesh = new THREE.Mesh(geometry, material);
  group.add(mesh);
});
```

Ogni emozione viene rappresentata come uno spicchio colorato su una sfera. La dimensione dello spicchio è proporzionale alla frequenza dell'emozione nei due periodi.

---

## Target e contesto d'uso

Il progetto si rivolge a studenti, designer e appassionati di fotografia e visualizzazione dati.\
Può essere utilizzato in ambito accademico o divulgativo per esplorare come i dati tecnici possano essere tradotti in narrazioni visuali personali.

---

## Media di progetto

- Sfera 3D esplorabile
- Visualizzazioni comparative: ISO, Exposure, Hue, Emozioni
- Dataset composto da 4.392 fotografie personali
- Pre-processing con analisi semantica (emozioni)

---

