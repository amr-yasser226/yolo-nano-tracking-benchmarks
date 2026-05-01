# YOLOv10n vs YOLOv11n vs YOLOv12n — Tracking Speed Report

## 1. Setup

- Video: `/kaggle/input/datasets/amryasser226/test-video/Test.mp4`
- Resolution: 1280x720, 30.00 fps source, 942 frames (~31.4 s)
- Device: `0`  |  half precision: `True`  |  imgsz: `640`
- Tracker: `bytetrack.yaml` (held constant across all detectors)
- Vehicle classes only: [2, 3, 5, 7] (COCO car/motorcycle/bus/truck)
- Warmup frames discarded: 30
- Ultralytics: 8.4.45, Torch: 2.10.0+cu128, GPU: Tesla T4

## 2. Headline numbers

| Model | Params | Predict FPS | Track FPS | Predict mean ms | Track mean ms | Track p95 ms | Tracks |
|---|---:|---:|---:|---:|---:|---:|---:|
| YOLOv10n | 2,775,520 | 85.1 | 64.2 | 11.78 | 16.16 | 17.17 | 200 |
| YOLOv11n | 2,624,080 | 78.3 | 60.6 | 12.81 | 16.55 | 17.99 | 184 |
| YOLOv12n | 2,603,056 | 55.3 | 47.6 | 18.10 | 21.09 | 22.87 | 149 |

## 3. Relative to baseline (YOLOv10n)

| Model | Predict ms diff | Track ms diff |
|---|---:|---:|
| YOLOv10n | +0.0% | +0.0% |
| YOLOv11n | +8.7% | +2.4% |
| YOLOv12n | +53.6% | +30.5% |

## 4. Honest analysis

- **Pure detector cost (`predict ms`)** is the cleanest signal. The track pass adds ByteTrack association, Kalman update, ID bookkeeping, annotation drawing, and `mp4` encode — collectively non-trivial and roughly constant across detectors.
- **Variance matters.** Look at `p95` and the boxplot. A model with a slightly worse mean but tighter distribution is more useful in production than one with a great mean and a long tail.
- **Detection counts** between the three models should be roughly similar. If one model diverges sharply, treat its FPS gap with suspicion — it may be doing less work, not faster work.
- **Single-video benchmarks are weak.** These numbers are valid for *this* clip on *this* hardware. Do not generalize to absolute performance statements.

## 5. Artifacts

- `speed_comparison.json` — full machine-readable summary
- `comparison_table.csv` — headline table
- `per_frame_logs/*.csv` — every frame timing for both passes
- `*_tracked.mp4` — annotated tracking video per model
- `plots/*.png` — all figures
- `sample_frames/*.jpg` — mid-video annotated frames