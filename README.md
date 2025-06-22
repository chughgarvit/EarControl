# EarControl: Towards Earable-Based Hands-Free Interaction for Mobile Devices

## 📖 Overview

EarControl is a lightweight, on-device framework for real-time gesture recognition using the IMU in commercial earables (e.g., AirPods Pro). It enables discrete, hands-free control of mobile devices via head, facial and periocular gestures—ideal for scenarios where touch or voice are impractical (driving, cooking, exercising).

Key features:

* **10 primary gestures**: nod, tilt left/right, shake, wink left/right, eyebrow raise, squint, smile, frown
* **Semi-supervised contrastive learning** for robust recognition with minimal labels
* **On-device iOS demo app** mapping gestures to music controls
* **Cross-context evaluation** on in-house (“EarSet”), FatigueSet, and public datasets

## 🗂 Repository Structure

```
EarControl/
├── README.md
├── LICENSE
├── IMUSensing/                    # iOS demo app (Swift + CoreBluetooth)
│   ├── AirPodsIMU/                # IMU data acquisition module
│   └── EarControlApp/             # Gesture-to-action mapping UI
├── model/
│   ├── train.py                   # Training & semi-supervised pipeline
│   ├── evaluate.py                # Evaluation on test splits & public sets
│   └── augment.py                 # IMU data augmentation transforms
└── EarControl Demo Video.mp4      # Demo Video
```

## 🚀 Getting Started

### Prerequisites

* **Git** ≥ 2.25 + **Git LFS** for large IMU/video assets
* **Xcode** ≥ 13 (iOS 15) for the demo app
* **Python** ≥ 3.8

### Installation

1. Clone the repo and enable LFS:

   ```bash
   git clone https://github.com/chughgarvit/EarControl.git
   cd EarControl
   git lfs install
   git lfs track "*.zip" "*.mp4"
   git pull
   ```

2. Create & activate a virtual environment:

   ```bash
   python3 -m venv venv
   source venv/bin/activate
   ```

3. Install Python dependencies:

   ```bash
   pip install -r requirements.txt
   ```

## ⚙️ Usage

### 1. Data Collection

* Open `app/AirPodsIMU` in Xcode, build & run on your iPhone
* Pair AirPods Pro and record IMU streams to CSV

### 2. Preprocessing & Augmentation

```bash
python scripts/preprocess.py --input data/inhouse/raw.csv --output data/inhouse/filtered.npy
python scripts/augment.py --input data/inhouse/filtered.npy --output data/inhouse/augmented.npy
```

### 3. Train & Evaluate

```bash
# Train semi-supervised model
python model/train.py \
  --labeled data/inhouse/filtered.npy \
  --unlabeled data/inhouse/augmented.npy \
  --out-dir model/checkpoints/

# Evaluate on in-house & public sets
python model/evaluate.py --checkpoint model/checkpoints/best.pth \
  --datasets inhouse EarSet FatigueSet
```

### 4. Demo App

* Open `IMUSensing` target in Xcode
* In Settings, point to your trained model’s REST endpoint or embed CoreML conversion
* Run on iPhone to control music playback with gestures (see Fig. 9 in the paper)

## 📊 Results & Benchmarks

| Dataset    | Macro-F1 | Precision | Recall |
| ---------- | -------: | --------: | -----: |
| In-House   |     0.85 |      0.87 |   0.83 |
| EarSet     |     0.82 |      0.84 |   0.80 |
| FatigueSet |     0.80 |      0.82 |   0.79 |

*For full metrics and ablations, see Section 6 of the paper.*

## 📝 Citation

If you use EarControl in your research, please cite:

> Garvit Chugh, Indrajeet Ghosh, Abu Zaher Md Faridee, Nirmalya Roy,
> Sandip Chakraborty & Suchetana Chakraborty.
> *EarControl: Towards Earable-Based Hands-Free Interaction for Mobile Devices.*

```bibtex
@article{chugh2025earcontrol,
  author    = {Chugh, Garvit and Ghosh, Indrajeet and Faridee, Abu Zaher Md  
               and Roy, Nirmalya and Chakraborty, Sandip and Chakraborty, Suchetana},
  title     = {EarControl: Towards Earable-Based Hands-Free Interaction for Mobile Devices},
  year      = {2025},
  volume    = {xx},
  number    = {x},
  pages     = {1--xx},
  doi       = {10.1145/xxxxxxx}
}
```

## ⚖️ License

This project is released under the [MIT License](LICENSE).
