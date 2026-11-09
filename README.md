# Helix Model Catalog

Machine-readable catalog data for Line 6 Helix models, including model identifiers, categories, parameter metadata, display behavior, defaults, ranges, DSP-load fields, and related cabinet/IR references.

The repository is designed for developers building search, browsing, documentation, editor, or analysis tools around Helix model metadata. The catalog is data only; it is not an audio-processing engine or a replacement for Line 6 documentation.

## Catalog snapshot

The bundled `helix-full-catalog.json` reports:

- **681** model records
- **301** display types
- **6,861** parameter records
- **19** category labels, including `AMP`, `PREAMP`, `CAB`, `CAB_MIC_IR`, `DISTORTION`, `MODULATION`, `DELAY`, `REVERB`, `PITCH`, `FILTER`, `EQ`, and `SYNTH`
- **2026-04-07** generation timestamp (`generated_at`: `2026-04-07T02:23:06.432786+00:00`)

The category distribution in this snapshot is:

| Category | Models |
| --- | ---: |
| AMP | 113 |
| PREAMP | 111 |
| CAB_MIC_IR | 92 |
| DISTORTION | 61 |
| MODULATION | 56 |
| DELAY | 48 |
| CAB | 41 |
| PITCH | 30 |
| REVERB | 25 |
| COMPRESSOR_GATE | 20 |
| SEND_RETURN | 18 |
| FILTER | 15 |
| WAH | 11 |
| EQ | 8 |
| VOLUME_PAN | 5 |
| LOOPER | 4 |
| IR | 3 |
| SYNTH | 1 |
| Unclassified (`null`) | 19 |

Counts describe this particular JSON snapshot and may change when the catalog is regenerated.

## File

`helix-full-catalog.json` is a single JSON object with these top-level fields:

| Field | Description |
| --- | --- |
| `source` | Names or paths for the source datasets and metadata overlay used by the catalog build. |
| `generated_at` | ISO 8601 timestamp for the generated catalog. |
| `model_count` | Declared number of records in `models` (681 in this snapshot). |
| `display_type_count` | Declared number of display types (301 in this snapshot). |
| `models` | Array of model records. |

Each model record includes fields such as:

- `symbolic_id`: stable-looking machine identifier, for example `HD2_AmpGermanMahadeva`
- `helix_name`: user-facing model name, for example `German Mahadeva`
- `category` and `category_name`: numeric/category labels
- `source_dataset`: source/provenance tag
- `based_on`, `aliases`, and `characteristics`: descriptive metadata when available
- `mono`, `stereo`, `load_mono`, and `load_stereo`: channel and load-related fields when present
- `cablink` and `ircablink`: related cabinet or IR identifiers when present
- `source_url` and `notes`: source references and catalog notes when available
- `category_catalog`: nested category, subcategory, and model metadata when available
- `param_count` and `params`: parameter count and detailed parameter records

Each item in `params` can include:

| Field | Description |
| --- | --- |
| `symbolic_id` | Parameter identifier. |
| `name` | Display name. |
| `value_type` | Catalog value-type code. |
| `display_type` | Display/control type, such as `generic_knob`, `volume`, `frequency`, or `off_on`. |
| `minimum`, `maximum`, `default` | Numeric or boolean bounds and default value. |
| `assign` | Assignment metadata when present. |
| `allowable` | Continuous-range, enum, boolean, or other allowable-value description. |
| `display` | Formatting and display-scale metadata. |
| `gui` | Normalized GUI/control metadata, including kind, labels, scale, format, and steps. |

Fields may be `null`, and not every model has the same parameter set. Treat the JSON schema as intentionally record-oriented rather than assuming every field is populated for every model.

## Usage examples

### Find a model by symbolic ID

```python
import json

with open("helix-full-catalog.json", encoding="utf-8") as f:
    catalog = json.load(f)

model = next(
    item for item in catalog["models"]
    if item["symbolic_id"] == "HD2_AmpGermanMahadeva"
)

print(model["helix_name"])       # German Mahadeva
print(model["category_name"])    # AMP
print(model["param_count"])      # 14
```

### Read parameter ranges and defaults

```python
for parameter in model["params"]:
    print(
        parameter["name"],
        "default=", parameter["default"],
        "range=", (parameter["minimum"], parameter["maximum"]),
        "display=", parameter["display_type"],
    )
```

### Build a category index

```python
from collections import defaultdict

by_category = defaultdict(list)
for item in catalog["models"]:
    by_category[item.get("category_name")].append(item)

for category, items in sorted(by_category.items(), key=lambda pair: str(pair[0])):
    print(category, len(items))
```

## Provenance and limitations

The `source` and per-record metadata identify source files, Line 6 URLs, and—in some records—the Helix 3.80 Owner's Manual. Those references describe where the catalog data was obtained or cross-referenced; they do not imply that Line 6 endorses, maintains, or validates this repository.

This project does not claim that the catalog is complete, current for every Helix firmware or product release, technically authoritative, or free of transcription and interpretation errors. Consumers should verify behavior against the applicable device, firmware, and official documentation before relying on a value in production or safety-critical work.

No license grant for Line 6 names, trademarks, manuals, or other third-party material is implied by this repository. Check the relevant rights and terms before redistributing source-derived data or building a commercial product.

Unless otherwise stated, this repository is provided without a general reuse license. Third-party and source-derived material remains subject to its respective rights and terms.

## Independent project

This is an independent, community-maintained data project. It is **not affiliated with, sponsored by, endorsed by, or officially connected to Line 6 or Yamaha Guitar Group**. “Helix” and other product names are used only to identify the products and models described by the data.

## Contributing

Issues and pull requests are welcome for corrections, missing metadata, parser improvements, and reproducible catalog updates. When proposing a change, include the affected `symbolic_id`, the reason for the change, and a source or reproduction note where one is available.
