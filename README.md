# NazarAi

Monorepo for a city-wide ANPR / multi-camera vehicle tracking platform.

## Repos in this folder

- `Multi-Camera-Vehicle-Tracking-and-Reidentification/` — 2018 AI City Track 3 pipeline (Caffe/GoogLeNet + Joint Bayesian). **Reference only:** unlicensed ("all rights reserved"), Python 2 / Caffe, and superseded.
- `AIC21-MTMC/` — AI City 2021 MTMC pipeline (ResNet-IBN ReID + GPL-3.0 yolov5 detector + MOT tracking). Usable, but the vendored yolov5 is GPL-3.0 (keep it as an isolated component with its LICENSE).

## Recommended open-source alternatives (no license issues, actively maintained)

These are clean, permissively-licensed (MIT) upgrades. Use them in new code paths instead of building on the repos above.

### Pipeline-level — replace `Multi-Camera-Vehicle-Tracking-and-Reidentification` / `AIC21-MTMC`

- **AIC22-MCVT** — `github.com/coder-wangzhen/AIC22-MCVT` (MIT)
  - 2nd place, AI City Challenge 2022. Same design as the AIC21 folder (yolov5 + ResNet-IBN ReID + tracking/association) but newer and stronger.
  - Replaces the 2018 repo and most of AIC21 in one go.

### Model-level — swap out the ReID model

- **TransReID** — `github.com/damo-cv/TransReID` (MIT)
  - The standard modern vehicle-ReID model (transformer-based). State-of-the-art on VeRi776 / VehicleID vs the older ResNet-IBN and 2018 GoogLeNet/Joint-Bayesian.
  - Clean drop-in for the ReID feature-extraction stage of either pipeline.
  - Note: TransReID-SSL (same org, also MIT) is person-ReID-focused; for vehicle use **TransReID**.
- **AIC22-MCVT** (listed above) can also serve as the modern detector+tracker baseline if you keep your own ReID choice.

## Which to use when

- **New production build:** use **TransReID** for appearance ReID + **AIC22-MCVT** as the pipeline baseline.
- **Quick replacement of the 2018 folder:** AIC22-MCVT only — it drops Caffe/Python-2 entirely (both pipelines here need Caffe, which is the biggest setup blocker).
- **Hardware note:** TransReID's transformer backbones benefit from a GPU. This machine is CPU-only with ~14 GB RAM, so plan hardware accordingly.

## License status of what's in this folder

| Component | License | Notes |
|---|---|---|
| `AIC21-MTMC` (own code) | MIT (`AIC21-MTMC/LICENSE`) | Safe to use |
| `AIC21-MTMC/detector/yolov5` | GPL-3.0 | Keep isolated; copyleft if distributed as a merged binary |
| `Multi-Camera-Vehicle-Tracking-and-Reidentification` | None ("all rights reserved") | Do not redistribute; needs author permission |
| `AIC21-MTMC/tracker/.../MinCostPerfMatch` | MIT | Safe |
