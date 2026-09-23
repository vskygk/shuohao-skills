# Production Bible Schema Guide

`production-bible.json` is intentionally a compact project contract, not a second copy of the drama database. Omit sections that do not affect the project.

```json
{
  "version": "1.0",
  "project": "Project title",
  "sourceOfTruth": {
    "characters": "cast.json",
    "art": "art.json",
    "script": "script.json",
    "storyboard": "storyboard.json"
  },
  "visualProduction": {
    "cameraStaging": [],
    "imageQuality": [],
    "controlledAgeing": []
  },
  "identityContinuity": {
    "groups": []
  },
  "spatialContinuity": {
    "scenes": []
  },
  "videoDelivery": {
    "referenceMode": "",
    "keyframePolicy": [],
    "audioReferencePolicy": []
  },
  "assetLifecycle": {
    "sourceFiles": [],
    "generatedOutputs": [],
    "replacementPolicy": [],
    "gitPolicy": []
  }
}
```

## Section guidance

### `visualProduction`

Use short observable statements. `cameraStaging` can describe permitted relationship to the lens; `imageQuality` can distinguish clean image construction from a sterile setting; `controlledAgeing` can specify where wear is permitted and which artifacts remain forbidden.

### `identityContinuity.groups`

Each group should name the member IDs, immutable identifying features, and permitted differentiators. This supports clones, relatives, disguises, transformation forms, and recurring costumes without requiring the generic character skill to know the project lore.

### `spatialContinuity.scenes`

Use existing scene IDs. Record only information a shot cannot safely infer: access route, fixed screen-direction anchors, entry/exit logic, and discrete state arcs such as a hatch changing from sealed to open. Do not repeat the full art card.

### `videoDelivery`

Record the target model convention and production packaging. For reference-driven clips, state that each supplied keyframe must map to a shot and a timestamp; for audio, distinguish reference timbre from direct signal reuse. Upload instructions belong in delivery metadata or a header outside the model prompt body.

### `assetLifecycle`

Name sources of truth and generated outputs. Define what must be regenerated after an asset replacement, how obsolete images are handled, and which reviewed generated assets may enter Git. Do not encode absolute paths that make the bible non-portable unless the project explicitly needs local-only operation.
