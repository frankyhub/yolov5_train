# yolov5_train

## Roboflow train

In Roboflow kannst du Bilder hochladen, labeln und exportieren.

### 1. Daten hochladen
   
• Lade deine Bilder und Labels in Roboflow hoch:

• Gehe zu deinem Roboflow-Projekt.

• Klicke auf „+ Add Data“ und lade deine Bilder hoch.

• Stelle sicher, dass Labels korrekt zugeordnet sind.


### 2. Daten aufteilen (Train/Val/Test-Split erstellen)

Roboflow ermöglicht es, deine Daten in Trainings-, Validierungs- und Testdaten aufzuteilen. Das kannst du folgendermaßen machen:

#### 2.1. Während der Projekterstellung

• Nach dem Hochladen der Bilder wirst du gefragt, wie du die Daten aufteilen möchtest.

• Gib den Split in Prozent an, z. B.:

• Train: 70%

• Validation (Val): 20%

• Test: 10%

• Roboflow verteilt die Bilder automatisch in die entsprechenden Sets.


#### 2.2. Nachträglich

Falls du die Splits nachträglich ändern möchtest:

• Gehe zu deinem Projekt-Dataset.

• Wähle „Settings“ > „Dataset Splits“.

• Passe die Prozentsätze für Train/Val/Test an.


Hinweis: Roboflow aktualisiert die Splits basierend auf den neuen Prozentwerten. Bereits existierende Splits werden überschrieben.


### 3. Export der Daten
   
Nach dem Setzen der Splits kannst du die Daten exportieren:

1. Gehe zu „Versions“ und wähle die gewünschte Version.
2. Klicke auf „Generate“ und wähle das YOLOv5-Format.
3. Lade die ZIP-Datei herunter.

Die heruntergeladene Datei enthält dann die Daten strukturiert in:

![Bild](pic/datenstruktur.png)

### 4. Überprüfung der Splits

• Train: Enthält den Großteil der Daten (für das Training).

• Validation (Val): Enthält Daten, die während des Trainings verwendet werden, um die Leistung zu überprüfen.

• Test: Optional, wird nach dem Training verwendet, um die endgültige Modellleistung zu testen.

### 5. Tipps

• Stratifizierte Splits: Roboflow versucht, die Verteilung der Klassen in den Splits gleichmäßig zu halten.

• Manuelles Anpassen: Falls bestimmte Bilder manuell in „val“ oder „test“ verschoben werden sollen, kannst du dies in der Bildübersicht von Roboflow tun.

----

## Roboflow Export

### 1. Export aus Roboflow:

• Stelle sicher, dass der Export aus Roboflow im YOLOv5-Format erfolgt.

• Wähle beim Export „YOLOv5 PyTorch“ aus.

• Lade die ZIP-Datei herunter, die Bilder und Annotations enthält.

### 2. Struktur des Datasets:

Die Datenstruktur für YOLOv5 sollte wie folgt aussehen:

![Bild](pic/datenstruktur.png)


### 3. Daten korrekt einbinden:

#### 3.1. Daten entpacken und strukturieren

• Entpacke die heruntergeladene ZIP-Datei von Roboflow.

• Stelle sicher, dass die Ordnerstruktur wie oben beschrieben eingerichtet ist. Roboflow liefert oft bereits eine fertige Struktur, du musst sie nur überprüfen.

#### 3.2. data.yaml Datei erstellen

Die Datei data.yaml definiert die Pfade zu deinen Trainings- und Validierungsdaten und listet die Klassen auf. Ein Beispiel:

train: dataset/train/images

val: dataset/val/images

nc: 2  # Anzahl der Klassen

names: ['class1', 'class2']    # Ersetze classx mit deinen Klassennamen


• train: Pfad zu den Trainingsbildern.

• val: Pfad zu den Validierungsbildern.

• nc: Anzahl der Klassen.

• names: Liste der Klassennamen.

Hinweis: Roboflow exportiert oft die Datei data.yaml automatisch. Überprüfe, ob sie korrekt ist.

### 4. YOLOv5-Training starten:

#### 4.1. YOLOv5 Repository klonen

Falls noch nicht geschehen, klone das YOLOv5-Repository:

git clone https://github.com/ultralytics/yolov5.git

cd yolov5

#### 4.2. Abhängigkeiten installieren

Installiere die notwendigen Pakete:

pip install -r requirements.txt

#### 4.3. Training starten

Starte das Training mit folgendem Befehl:


```
python train.py --img 640 --batch 16 --epochs 50 --data dataset/data.yaml --weights yolov5s.pt
```

• --img: Bildgröße (640 ist Standard).

• --batch: Batch-Größe (abhängig von der GPU-Kapazität).

• --epochs: Anzahl der Trainingsdurchläufe.

• --data: Pfad zur data.yaml Datei.

• --weights: Vortrainierte Gewichte (z. B. yolov5s.pt).


### 5. Ergebnisse überprüfen

Nach Abschluss des Trainings findest du die Ergebnisse im Ordner runs/train/exp. Darin befinden sich:

• Trainingsstatistiken (results.png).

• Gewichte (best.pt und last.pt).



 ---

 ## *.pt -Dateien

Gewichte (Weights) wie z. B. best.pt oder last.pt sind gespeicherte Modelle, die die Parameter (Gewichte und Biases) eines neuronalen Netzes enthalten. Sie repräsentieren den Zustand des Modells nach dem Training. Im Kontext von YOLOv5 haben diese Dateien folgende Bedeutung:

### 1. best.pt

• Dies sind die besten Gewichte, die während des Trainings erreicht wurden.

• Basierend auf der Validierungsleistung (z. B. mAP, Präzision, Recall) wird diese Datei gespeichert, sobald das Modell eine bessere Performance erzielt.

• Du kannst best.pt verwenden, um das Modell für Inferenz zu nutzen, da es die bestmögliche Leistung bietet.

### 2. last.pt

• Dies sind die Gewichte des Modells nach dem letzten Trainingsepoch.

• Es kann sein, dass diese nicht die beste Leistung repräsentieren, wenn das Modell während des Trainings zu stark angepasst wurde (Overfitting).

• Diese Datei ist nützlich, wenn du ein Training fortsetzen oder debuggen möchtest.

### 3. Wofür werden die Gewichte verwendet?

#### 3.1. Für die Inferenz (Vorhersagen)

Die Gewichte werden verwendet, um das trainierte Modell auf neue Daten anzuwenden. Zum Beispiel:

python detect.py --weights best.pt --img 640 --conf 0.5 --source path/to/images

• --weights: Gibt die Gewichtedatei an (best.pt oder eine andere .pt-Datei).

• --source: Ordner oder Datei mit Bildern/Videos, die analysiert werden sollen.

• Das Modell verwendet die gespeicherten Gewichte, um Objekte zu erkennen.

#### 3.2. Für das Fine-Tuning

Wenn du ein vortrainiertes Modell weiter trainieren möchtest, kannst du die Gewichte als Ausgangspunkt verwenden:

python train.py --img 640 --batch 16 --epochs 20 --data data.yaml --weights best.pt

• Dies spart Rechenzeit, da das Modell bereits eine solide Grundlage hat.

### 4. Wie wird best.pt generiert?

Während des Trainings wird nach jeder Epoche die Leistung des Modells auf den Validierungsdaten evaluiert. Wenn das Modell in einer Epoche besser abschneidet als zuvor, speichert YOLOv5 die neuen Gewichte als best.pt.

Zusammengefasst:

• best.pt: Beste Leistung während des Trainings.

• last.pt: Letzte gespeicherte Gewichte am Ende des Trainings.

• Beide können für Vorhersagen oder Fine-Tuning verwendet werden.

---
## winget
### Wenn noch nicht installiert, installiere das winget-Tool und gib den Befehl in die Eingabeaufforderung oder PowerShell ein:

```
winget install --id Git.Git -e --source winget
```

[Hilfe_winget](https://learn.microsoft.com/de-de/windows/package-manager/winget)

---
## Download yolov11

```
from ultralytics import YOLO

# Load a model
model = YOLO("yolo11n.pt")

# Train the model
train_results = model.train(
    data="coco8.yaml",  # path to dataset YAML
    epochs=100,  # number of training epochs
    imgsz=640,  # training image size
    device="cpu",  # device to run on, i.e. device=0 or device=0,1,2,3 or device=cpu
)

# Evaluate model performance on the validation set
metrics = model.val()

# Perform object detection on an image
results = model("path/to/image.jpg")
results[0].show()

# Export the model to ONNX format
path = model.export(format="onnx")  # return path to exported model
```

| Modell | Dateinamen | Aufgabe |
| -------- | -------- | -------- |
| YOLO11        | yolo11n.pt yolo11s.pt yolo11m.pt yolo11l.pt yolo11x.pt        | Erkennung        |
| YOLO11-seg       | yolo11n-seg.pt yolo11s-seg.pt yolo11m-seg.pt yolo11l-seg.pt yolo11x-seg.pt        | Instanz-Segmentierung       |
| YOLO11-pose        | yolo11n-pose.pt yolo11s-pose.pt yolo11m-pose.pt yolo11l-pose.pt yolo11x-pose.pt        | Pose/Keypoints	       |
| YOLO11-obb       | yolo11n-obb.pt yolo11s-obb.pt yolo11m-obb.pt yolo11l-obb.pt yolo11x-obb.pt        | Orientierte Erkennung	        |
| YOLO11-obb       | yolo11n-cls.pt yolo11s-cls.pt yolo11m-cls.pt yolo11l-cls.pt yolo11x-cls.pt        | Klassifizierung       |
| -------- | -------- | -------- |

- Objekt-Erkennung: Identifizierung und Lokalisierung von Objekten innerhalb eines Bildes.
- Instanz-Segmentierung: Erkennen von Objekten und Abgrenzen ihrer Grenzen.
- Bildklassifizierung: Einteilung von Bildern in vordefinierte Klassen.
- Schätzung der Körperhaltung: Erkennung und Verfolgung von Schlüsselpunkten auf menschlichen Körpern.
- Orientierte Objekterkennung (OBB): Erkennung von Objekten mit Drehung für höhere Präzision.


## coco
In **YOLOv11** (oder in den vorherigen Versionen wie YOLOv4, YOLOv5), bezieht sich der Begriff **COCO** auf den **COCO-Datensatz (Common Objects in Context)**, der häufig für das Training und Testen von Objekterkennungsmodellen verwendet wird. Der COCO-Datensatz enthält eine große Anzahl von annotierten Bildern mit verschiedenen Objekten in realen Kontexten, was bedeutet, dass Objekte in verschiedenen Szenarien und Positionen erscheinen.

Die Aufgabe von **COCO** im Kontext von YOLO (You Only Look Once) ist in erster Linie, als **Trainings- und Testdatensatz** zu dienen. Konkret hat COCO die folgenden Aufgaben:

1. **Bereitstellung von Trainings- und Testdaten**: Der COCO-Datensatz besteht aus Millionen von Bildern und verschiedenen Objektkategorien, die genutzt werden, um Modelle wie YOLO zu trainieren. Diese Bilder enthalten unterschiedliche Objekte (z.B. Personen, Autos, Tiere) in verschiedenen Szenarien und Kontexten.

2. **Evaluation des Modells**: COCO bietet eine standardisierte Möglichkeit, die Leistung von Objekterkennungsmodellen wie YOLO zu bewerten. Es stellt Metriken wie **mAP (mean Average Precision)** zur Verfügung, die die Genauigkeit des Modells bei der Objekterkennung messen.

3. **Unterstützung bei der Modellentwicklung**: COCO ist eine wichtige Referenz, um die Fortschritte in der Objekterkennung und den allgemeinen Fortschritt von Computer Vision-Modellen wie YOLO zu verfolgen. Durch den Einsatz von COCO können Entwickler sicherstellen, dass ihre Modelle auf realistische Szenarien getestet werden.

Insgesamt wird COCO verwendet, um YOLO-Modelle auf eine breite und vielfältige Menge von Objekten und Szenarien zu trainieren, was deren Leistung und Robustheit in realen Anwendungen verbessert.

## yolo
**YOLO** steht für **"You Only Look Once"**. Es handelt sich um ein beliebtes und effizientes Verfahren zur **Objekterkennung** in Computer Vision. Der Name spiegelt das Hauptprinzip des Modells wider: Statt ein Bild in mehreren Schritten oder Teilen zu analysieren, erfolgt die Objekterkennung in **einem einzigen Durchgang** (also "once"), was zu einer deutlich schnelleren Verarbeitung führt.

### Kerneigenschaften von YOLO:
1. **Schnelligkeit**: YOLO kann in Echtzeit Objekte erkennen, was es besonders für Anwendungen mit niedriger Latenz und in Echtzeit (z. B. Videoüberwachung, autonomes Fahren) geeignet macht.
   
2. **End-to-End-Erkennung**: YOLO betrachtet das Bild als Ganzes und teilt es in ein Gitter auf, wobei jedes Gitter eine Bounding Box und eine Wahrscheinlichkeit für bestimmte Objekte vorhersagt. Dies bedeutet, dass das Modell in einem Schritt sowohl die Objekte als auch deren Positionen und Klassifikationen vorhersagt.

3. **Genauigkeit**: Obwohl YOLO ursprünglich nicht so präzise wie andere Modelle war (z. B. Faster R-CNN), hat sich die Genauigkeit in neueren Versionen (wie YOLOv3, YOLOv4, YOLOv5) stark verbessert, sodass es eine gute Balance zwischen Geschwindigkeit und Genauigkeit bietet.

### Funktionsweise:
- Das Bild wird in ein Gitter unterteilt.
- Jede Zelle im Gitter prediziert mehrere Bounding Boxes und deren Wahrscheinlichkeit, dass ein Objekt darin enthalten ist.
- Es wird eine einzige Vorhersage für jedes Objekt gemacht, die Position und Klassifikation gleichzeitig enthält.

YOLO hat eine Reihe von Versionen, die im Laufe der Jahre weiterentwickelt wurden, um sowohl die Leistung als auch die Genauigkeit zu verbessern.

---
---


