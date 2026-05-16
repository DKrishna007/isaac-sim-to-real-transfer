# Proof Guide: Isaac Sim to Real Transfer

This document describes the evidence and artifacts that demonstrate the sim-to-real transfer performance of this project.

---

## 1. Core Claim

> YOLOv8-m trained **exclusively on Isaac Sim synthetic data** with domain randomization achieves **84.1% mAP@0.5** on real warehouse images — a sim-to-real accuracy gap of only **6.2%** (down from 18% baseline).

**Zero real training images were used.**

---

## 2. Reproducibility Steps

### 2.1 Environment Setup

```bash
# Install Isaac Sim 2023.1.1 (requires NVIDIA Omniverse)
# https://developer.nvidia.com/isaac-sim

# Clone repo
git clone https://github.com/DKrishna007/isaac-sim-to-real-transfer
cd isaac-sim-to-real-transfer

# Install Python dependencies
pip install ultralytics==8.0.196 torch==2.1.0 torchvision==0.16.0
pip install omni.isaac.core

# Verify Isaac Sim connection
python src/domain_randomizer.py --dry-run
```

### 2.2 Generate Synthetic Dataset

```bash
# Run domain randomizer to generate 12,400 training images
python src/domain_randomizer.py \
  --config domain_randomization/randomization_params.yaml \
    --output datasets/synthetic_warehouse/ \
      --num_images 12400 \
        --seed 42

        # Verify dataset structure
        ls datasets/synthetic_warehouse/
        # Should show: images/ labels/ dataset.yaml
        ```

        ### 2.3 Train YOLOv8 on Synthetic Data

        ```bash
        yolo train \
          model=yolov8m.pt \
            data=datasets/synthetic_warehouse/dataset.yaml \
              epochs=100 \
                imgsz=640 \
                  batch=16 \
                    name=isaac_sim_warehouse

                    # Training takes ~4 hours on RTX 4090
                    # Best model saved to: runs/detect/isaac_sim_warehouse/weights/best.pt
                    ```

                    ### 2.4 Evaluate on Real Images

                    ```bash
                    # Evaluate on 500 real-world test images (included in repo)
                    yolo val \
                      model=runs/detect/isaac_sim_warehouse/weights/best.pt \
                        data=datasets/real_test/dataset.yaml \
                          split=test

                          # Expected output: mAP50=0.841, mAP50-95=0.612
                          ```

                          ---

                          ## 3. Key Result Files

                          | File | Content | Key Metric |
                          |---|---|---|
                          | `evaluation/sim_vs_real.md` | Full accuracy comparison table | mAP 84.1% real-world |
                          | `domain_randomization/randomization_params.yaml` | All DR parameters used | 7 randomization axes |
                          | `src/domain_randomizer.py` | Isaac Sim DR implementation | 12,400 synthetic images |

                          ---

                          ## 4. Validation Checklist

                          - [x] Model trained on 0 real images
                          - [x] 500-image real test set held out from training
                          - [x] Full domain randomization applied (lighting, texture, placement, physics)
                          - [x] Comparison with no-DR baseline (73.4% vs 84.1%)
                          - [x] Per-class breakdown provided
                          - [x] FPS benchmarks on Jetson AGX Orin
                          - [x] Failure mode analysis documented

                          ---

                          ## 5. Hardware Used

                          | Stage | Hardware |
                          |---|---|
                          | Simulation (data gen) | Workstation: RTX 4090, 64GB RAM |
                          | Training | Same workstation |
                          | Inference testing | Jetson AGX Orin 64GB |

                          ---

                          ## 6. Dataset Split

                          | Split | Images | Source |
                          |---|---|---|
                          | Train | 12,400 | Isaac Sim (synthetic) |
                          | Val | 2,100 | Isaac Sim (synthetic) |
                          | Test | 500 | Real warehouse (unseen) |

                          ---

                          ## 7. Contact

                          Krishna Digamarthi — Robotics MS, University of Delaware  
                          Specialization: Perception, Autonomous Systems, Edge AI
                          
