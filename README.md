# VitalParser

[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

**VitalParser** se conecta en tiempo real a archivos `.vital` generados por Vital Recorder, extrae datos del cliente (tanto señales tabulares como de onda), aplica modelos de machine learning preentrenados y exporta los resultados acumulados en formato Excel.

## Características

- **Procesado tabular:** Lectura de datos numéricos recientes y predicción mediante modelos ML de ventana deslizante o de un solo paso.
- **Procesado de onda:** Segmentación y remuestreo de señales (ECG, presión arterial, etc.) para aplicar modelos deep learning periódicamente.
- **Modelos configurables:** Definición flexible de parámetros y rutas mediante archivo `model.json`.
- **Exportación en Excel:** Resultados acumulativos generados en hojas Excel sin sobrescribir datos previos.
- **Interfaz gráfica:** Basada en Tkinter para selección de archivos `.vital`, modo de procesamiento y visualización de resultados en tiempo real.
- **CLI rápida:** Ejecución mediante script rápido (`start.bat`).
- **Arquitectura modular:** Código separado claramente en módulos para facilitar mantenimiento y expansión.

## Instalación

```bash
git clone https://github.com/ramsestein/VitalParser.git
cd VitalParser
python -m venv env
```
# Linux/Mac:

```bash
source env/bin/activate
```
# Windows:
```bash
.\env\Scripts\activate
```
```python
pip install -r requeriments.txt
```

## Estructura del Proyecto

```
VitalParser/
├── models/                    # Modelos ML preentrenados (.joblib, .h5, .pt…)
├── parser/
│   ├── arr.py                 # Funciones de procesamiento de señales
│   ├── model_loader.py        # Cargador de modelos ML (sklearn, keras, etc.)
│   ├── vital_processor.py     # Procesamiento principal y generación Excel
│   └── vital_utils.py         # Funciones auxiliares para archivos vital
├── model.json                 # Archivo configuración modelos
├── requeriments.txt           # Dependencias Python
├── vitalParserLearning_GUI.py # Lanzador GUI Tkinter
├── start.bat                  # Lanzador rápido (Windows)
└── LICENSE                    # Licencia MIT
```

## Uso

### Interfaz Gráfica (Tkinter)

```bash
python vitalParserLearning_GUI.py
```

Selecciona en la interfaz:

- **Recordings Dir:** Directorio raíz con archivos `.vital`.
- **Modo:** `Tabular` o `Wave`.
- **Start/Stop:** Inicia o detiene procesamiento periódico.
- **Use Mean:** Mostrar promedio en lugar de último valor.
- **Show Results:** Visualizar resultados actuales.

Los archivos generados se guardan en la carpeta:

```
results/<nombre>_tabular.xlsx
results/<nombre>_wave.xlsx
```

También puedes arrancar desde Windows usando:

```bash
start.bat
```

### Uso desde script (opcional)

```python
from parser.model_loader import load_ml_model
from parser.vital_processor import VitalProcessor
import os, json

base = os.getcwd()
with open('model.json') as f:
    cfgs = json.load(f)

for c in cfgs:
    c['model'] = load_ml_model(os.path.join(base, c['path']))

proc = VitalProcessor(cfgs, os.path.join(base, 'results'))
df = proc.process_once('ruta/a/grabaciones', mode='tabular')
print(df.tail())
```

## Configuración (`model.json`)

Ejemplo básico de configuración:

```json
[
  {
    "name": "Predicción hTA",
    "path": "models/gb_model_pruebas.joblib",
    "input_type": "tabular",
    "input_vars": ["Demo/ART","Demo/ECG","Demo/CVP","Demo/EEG","Demo/PLETH","Demo/CO2"],
    "output_var": "Prediccion 1",
    "window_size": 1
  },
  {
    "name": "Prueba HPI (wave)",
    "path": "models/model_hpi.joblib",
    "input_type": "wave",
    "signal_track": "Demo/ART",
    "signal_length": 2000,
    "resample_rate": 100,
    "interval_secs": 20,
    "overlap_secs": 10,
    "output_var": "HTI"
  }
]
```

## Contribuciones

1. Haz fork del repositorio.
2. Crea una rama:
    ```bash
    git checkout -b feature/tu-nueva-funcionalidad
    ```
3. Realiza tus modificaciones y commit:
    ```bash
    git commit -m "Añadido: nueva funcionalidad"
    ```
4. Envía un Pull Request con los cambios.

## Licencia

Licencia MIT, ver [LICENSE](LICENSE).
