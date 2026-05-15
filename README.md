# Sim-to-Real Transfer with Isaac Sim Domain Randomization

> **Project** | NVIDIA Isaac Sim · YOLOv8 · Zero-Shot Transfer
> > Domain Randomization · Synthetic Data · Digital Twin · Perception
> >
> > ---
> >
> > ## Overview
> >
> > A **high-fidelity sim-to-real transfer pipeline** using NVIDIA Isaac Sim as a digital twin environment with targeted domain randomization. YOLOv8 was trained entirely on synthetic data, and demonstrated **zero-shot transfer** to a physical robot with no real-world training data — reducing the sim-to-real accuracy gap from ~18% to ~6%.
> >
> > ---
> >
> > ## Key Results
> >
> > | Metric | Value |
> > |---|---|
> > | Sim-to-Real Accuracy Gap | ~18% → **~6%** |
> > | Gap Reduction | ~67% improvement |
> > | Training Data | 100% synthetic (no real-world data) |
> > | Transfer | **Zero-shot** (no fine-tuning on real data) |
> > | Simulation Platform | NVIDIA Isaac Sim |
> >
> > ---
> >
> > ## System Architecture
> >
> > ```
> > NVIDIA Isaac Sim Digital Twin
> >   ├── Randomized Lighting (HDR + point lights)
> >   ├── Randomized Textures (objects, floor, walls)
> >   ├── LiDAR Noise Profiles (custom sim noise model)
> >   └── Camera Perturbations (blur, exposure, distortion)
> >        ↓
> > Synthetic Dataset Generation (automated)
> >        ↓
> > YOLOv8 Training (synthetic data only)
> >        ↓
> > Domain Randomization Tuning Loop
> >        ↓
> > Zero-Shot Deployment on Physical Robot
> >        ↓
> > 18% → 6% sim-to-real gap
> > ```
> >
> > ---
> >
> > ## Features
> >
> > - **High-fidelity Isaac Sim digital twin** of a real indoor environment
> > - - **Comprehensive domain randomization**: lighting, textures, LiDAR noise, camera parameters
> >   - - **YOLOv8** perception model trained entirely on synthetic data
> >     - - **Zero-shot transfer** to physical robot with no real-world training data
> >       - - Sim-to-real accuracy gap reduced from ~18% to ~6% (67% improvement)
> >         - - **Automated dataset generation** pipeline for rapid iteration
> >           - - Configurable randomization parameters for domain adaptation tuning
> >            
> >             - ---
> >
> > ## Tech Stack
> >
> > | Category | Tools / Libraries |
> > |---|---|
> > | Simulation | NVIDIA Isaac Sim |
> > | Detection Model | YOLOv8 (Ultralytics) |
> > | Training Framework | PyTorch, Ultralytics |
> > | Domain Randomization | Isaac Sim Python API |
> > | Programming | Python |
> > | Sensors Simulated | Camera, 3D LiDAR |
> > | Deployment | Physical robot (ROS 2) |
> >
> > ---
> >
> > ## Repository Structure
> >
> > ```
> > sim_to_real_ws/
> > ├── isaac_sim/
> > │   ├── scenes/
> > │   │   └── indoor_environment.usd          # Isaac Sim scene file
> > │   ├── domain_randomization/
> > │   │   ├── lighting_randomizer.py          # HDR + point light randomization
> > │   │   ├── texture_randomizer.py           # Material & texture randomization
> > │   │   ├── lidar_noise_randomizer.py       # LiDAR noise model randomization
> > │   │   └── camera_randomizer.py            # Camera parameter randomization
> > │   └── data_generation/
> > │       ├── synthetic_dataset_gen.py        # Automated dataset generation
> > │       ├── annotation_exporter.py          # YOLO format annotation export
> > │       └── dataset_config.yaml
> > ├── training/
> > │   ├── train_yolov8.py                     # YOLOv8 training script
> > │   ├── configs/
> > │   │   └── yolov8_synthetic.yaml           # Training config
> > │   └── evaluate.py                         # Sim + real evaluation
> > ├── sim_to_real_gap/
> > │   ├── gap_analysis.py                     # Quantify sim-to-real gap
> > │   └── randomization_tuning.py             # Iterative gap reduction
> > ├── deployment/
> > │   ├── ros2_detection_node.py              # ROS 2 real-robot deployment
> > │   └── zero_shot_eval.py                   # Zero-shot evaluation script
> > └── results/
> >     ├── sim_accuracy/
> >     └── real_accuracy/
> > ```
> >
> > ---
> >
> > ## Installation
> >
> > ```bash
> > # Clone the repository
> > git clone https://github.com/DKrishna007/sim-to-real-isaac-sim-domain-randomization.git
> > cd sim-to-real-isaac-sim-domain-randomization
> >
> > # Install Python dependencies
> > pip install ultralytics torch torchvision
> >
> > # Install Isaac Sim Python API
> > # Follow NVIDIA Isaac Sim installation guide:
> > # https://docs.omniverse.nvidia.com/isaacsim/latest/installation/install_python.html
> > ```
> >
> > ---
> >
> > ## Usage
> >
> > ```bash
> > # Generate synthetic dataset with domain randomization
> > python isaac_sim/data_generation/synthetic_dataset_gen.py \
> >   --num_images 10000 \
> >   --output_dir ./dataset/synthetic
> >
> > # Train YOLOv8 on synthetic data
> > python training/train_yolov8.py \
> >   --data dataset/synthetic \
> >   --model yolov8m.pt \
> >   --epochs 100
> >
> > # Evaluate sim-to-real gap
> > python sim_to_real_gap/gap_analysis.py \
> >   --sim_results results/sim_accuracy \
> >   --real_results results/real_accuracy
> >
> > # Deploy zero-shot on physical robot
> > ros2 run deployment ros2_detection_node.py
> > ```
> >
> > ---
> >
> > ## Domain Randomization Parameters
> >
> > | Parameter | Range |
> > |---|---|
> > | Lighting Intensity | 0.3 – 2.5x baseline |
> > | Light Position | ±2.0 m from nominal |
> > | Texture Variation | 50+ materials per object |
> > | LiDAR Noise Std | 0.01 – 0.05 m |
> > | Camera Blur | 0 – 3 px Gaussian |
> > | Camera Exposure | ±30% of nominal |
> >
> > ---
> >
> > ## Author
> >
> > **Krishna Digamarthi** | Robotics Engineer | University of Delaware
> > 📧 shivasaikrishna23@gmail.com | [GitHub](https://github.com/DKrishna007)
