# Thermal Dogs and People: Object Detection Dataset

A copy of the public-domain **Thermal Dogs and People** dataset from Roboflow, exported in Apple **CreateML JSON** format at 416×416. I used it for thermal-image object detection experiments (persons and dogs in infrared images).

![Dataset](https://img.shields.io/badge/data-thermal_infrared-8A2BE2)
![Format](https://img.shields.io/badge/format-CreateML_JSON-555555)
![License](https://img.shields.io/badge/license-Public_Domain-brightgreen)

## Contents

| Split | Image files | Annotation file |
|---|---|---|
| `train/` | 519 | `_annotations.createml.json` |
| `valid/` | 41 | `_annotations.createml.json` |
| `test/` | 20 | `_annotations.createml.json` |

Classes: `person`, `dog`. The main training annotation file covers 142 images with 181 boxes (97 person, 84 dog). The original Roboflow release has 203 images; extra files in `train/` are augmented or duplicate exports.

Annotation format (one entry per image):

```json
{
  "image": "IMG_0094_jpg.rf.03dbd175cdbd5379608debcc783a5361.jpg",
  "annotations": [
    {"label": "person", "coordinates": {"x": 268.5, "y": 226.5, "width": 27, "height": 41.5}},
    {"label": "person", "coordinates": {"x": 247.5, "y": 229, "width": 21.5, "height": 40.5}}
  ]
}
```

`x` and `y` are the box centre in pixels.

## Using it with YOLO

CreateML uses centre-based pixel boxes; YOLO needs normalised `class cx cy w h` per line. Convert with:

```python
import json, pathlib
classes = {"person": 0, "dog": 1}
for split in ["train", "valid", "test"]:
    ann = json.load(open(f"{split}/_annotations.createml.json"))
    for item in ann:
        lines = [
            f"{classes[a['label']]} {a['coordinates']['x']/416:.6f} {a['coordinates']['y']/416:.6f} "
            f"{a['coordinates']['width']/416:.6f} {a['coordinates']['height']/416:.6f}"
            for a in item["annotations"]
        ]
        pathlib.Path(split, pathlib.Path(item["image"]).stem + ".txt").write_text("\n".join(lines))
```

## Housekeeping notes

- `train/` contains seven duplicate annotation files (`_annotations.createml - Copy*.json`). Only `_annotations.createml.json` is needed.
- Images were captured with a Seek Compact XR thermal camera (Spectra palette). See `README.dataset.txt` for the original description.

## Source and license

Roboflow, *Thermal Dogs and People* (resize-416x416), provided by Joseph Nelson. https://public.roboflow.ai/object-detection/thermal-dogs-and-people. Public Domain.
