# Sim-to-Real Transfer Evaluation

## Experimental Setup

- **Simulator**: NVIDIA Isaac Sim 2023.1
- **Real Environment**: Warehouse floor with varying lighting
- **Detector**: YOLOv8-m trained **exclusively on synthetic data**
- **Test Set**: 500 real-world images (never seen during training)
- **Hardware**: Jetson AGX Orin 64GB

---

## Performance Comparison

### Detection Accuracy (mAP@0.5)

| Configuration | Synthetic Test | Real-World Test | Gap |
|---|---|---|---|
| No domain randomization | 91.2% | 73.4% | 17.8% |
| Lighting randomization only | 91.0% | 78.1% | 12.9% |
| Texture randomization only | 90.8% | 79.3% | 11.5% |
| Physics randomization only | 90.5% | 76.2% | 14.3% |
| Full domain randomization (ours) | 90.3% | **84.1%** | **6.2%** |

### Per-Class Breakdown (Full Randomization)

| Class | Synthetic mAP | Real mAP | Notes |
|---|---|---|---|
| Forklift | 93.1% | 87.4% | High-contrast, easy |
| Pallet | 89.7% | 83.2% | Floor texture sensitive |
| Person | 91.4% | 85.6% | Pose variation helps |
| Box stack | 88.2% | 80.1% | Shadow confusion |
| Conveyor | 92.0% | 84.2% | Metallic reflection |

---

## Domain Randomization Impact

### Randomization Parameters Tested

| Parameter | Range | Impact on Real mAP |
|---|---|---|
| Ambient light intensity | 0.3–1.8 lux | +4.2% |
| Directional light angle | ±45° | +2.1% |
| Floor texture variation | 50 textures | +3.7% |
| Object albedo jitter | ±30% | +1.8% |
| Camera exposure noise | σ=0.05 | +0.9% |
| LiDAR noise (Gaussian) | σ=0.02m | +0.6% |
| Object placement jitter | ±0.3m | +1.4% |

### Training Data Statistics

| Split | Synthetic Images | Real Images |
|---|---|---|
| Training | 12,400 | 0 (zero-shot) |
| Validation | 2,100 | 0 |
| Test | 0 | 500 |

---

## Inference Performance (Jetson AGX Orin)

| Model | Precision | FPS | mAP@0.5 (real) | Memory |
|---|---|---|---|---|
| YOLOv8-m | FP32 | 28 | 84.1% | 4.2 GB |
| YOLOv8-m | FP16 (TRT) | 47 | 83.8% | 2.1 GB |
| YOLOv8-s | FP16 (TRT) | 71 | 79.4% | 1.1 GB |

**Deployed configuration**: YOLOv8-m FP16 TensorRT @ 47 FPS

---

## Gap Analysis

### Before vs After Domain Randomization

```
Baseline (no randomization):
  Synthetic: 91.2% --[18% gap]--> Real: 73.4%

  With Full Domain Randomization:
    Synthetic: 90.3% --[6% gap]--> Real: 84.1%

    Gap reduction: 18% → 6% (3x improvement)
    ```

    ### Failure Mode Analysis

    | Failure Type | Frequency | Root Cause | Mitigation |
    |---|---|---|---|
    | False positives in bright sunlight | 8.3% | Overexposure | Add sun angle randomization |
    | Missed detections near walls | 6.1% | Occlusion | Increase occlusion aug |
    | Pallet misclassification | 4.7% | Floor texture | More texture diversity |
    | Person at distance | 3.2% | Scale mismatch | Multi-scale training |

    ---

    ## Conclusion

    Training exclusively on Isaac Sim synthetic data with comprehensive domain randomization achieved **84.1% mAP@0.5** on unseen real-world data — a **zero-shot transfer** with only a 6.2% accuracy gap compared to 17.8% without randomization.

    This validates the hypothesis that rich domain randomization in simulation can substitute for real-world labeled data collection in structured indoor environments.
