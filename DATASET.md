# Coffee Roasting Acoustic Crack Detection Dataset (CRAD)

## Overview

This dataset contains 4,301 manually annotated audio recordings of coffee bean crack events during roasting, recorded from three coffee origins across nine roasting trials.

**Key Statistics:**
- Total Audio Events: 4,301
- Total Duration: ~117 minutes of continuous recording
- Roasting Trials: 9 (3 replicas × 3 origins)
- Audio Format: WAV, 44.1 kHz, 16-bit, Mono
- Total File Size: ~113 MB
- Download Time: ~10-30 minutes (depends on connection)

---

## Dataset Composition

### By Class

| Class | Event Type | Count | Percentage | Typical Duration |
|-------|-----------|-------|-----------|-----------------|
| **0** | Background Noise | 2,373 | 55.2% | 50-300 ms |
| **1** | First Crack | 766 | 17.8% | 50-150 ms |
| **2** | Second Crack | 1,162 | 27.0% | 50-150 ms |
| | **Total** | **4,301** | **100%** | - |

### By Origin

| Origin | Location | Trials | Samples | Variety | Density | Moisture |
|--------|----------|--------|---------|---------|---------|----------|
| Kenya | Kiambu | 3 | ~1,420 | SL28/SL34 | 882 g/L | 11.7% |
| Ethiopia | Yirgacheffe | 3 | ~780 | Heirloom | 853 g/L | 11.1% |
| Yunnan | Baoshan | 3 | ~1,101 | Catimor | 843 g/L | 10.5% |

### Data Split

| Split | Samples | Train/Val/Test | Percentage |
|-------|---------|----------------|-----------|
| Training | 3,010 | Train | 70% |
| Validation | 645 | Val | 15% |
| Testing | 646 | Test | 15% |

---

## File Structure

### Directory Layout

```
datasets/
├── train/                    (Training set: 3,010 samples)
│   ├── 0/                   (Background: 1,660 files)
│   ├── 1/                   (First Crack: 540 files)
│   └── 2/                   (Second Crack: 810 files)
├── val/                      (Validation set: 645 samples)
│   ├── 0/                   (Background: 355 files)
│   ├── 1/                   (First Crack: 115 files)
│   └── 2/                   (Second Crack: 175 files)
└── test/                     (Test set: 646 samples)
    ├── 0/                   (Background: 358 files)
    ├── 1/                   (First Crack: 111 files)
    └── 2/                   (Second Crack: 177 files)
```

### File Naming Convention

```
{origin}{trial_id}_{timestamp}s_{index}.wav

Example:  1yunnan_167.61s_0.wav
├─ 1        : Trial ID (1-9)
├─ yunnan   : Origin (yunnan/kenya/ethiopia)
├─ 167.61s  : Timestamp in original recording (seconds)
└─ 0        : Segment index for this timestamp
```

### Metadata in Filenames

- **Origin**: `yunnan`, `kenya`, `ethiopia`
- **Trial ID**: `1` through `9`
- **Timestamp**: Position in original roasting session (float, seconds)
- **Index**: Segment counter at that timestamp

---

## Data Acquisition & Annotation

### Recording Setup

**Equipment:**
- Microphone: BOYA CM-1 cardioid condenser (80 Hz–16 kHz, −47±3 dB)
- Interface: Professional USB audio interface
- Sampling Rate: 44.1 kHz, 16-bit, Mono
- Duration: Full roasting session (~13 minutes each)

**Recording Conditions:**
- Temperature: 25±2°C
- Humidity: 50±5% RH
- Background Calibration: 30s noise sample before each trial
- Microphone Position: 10-15 cm from drum outlet, 45° angle
- Roasting Parameters: 50% burner output, 50% airflow (constant)

### Segmentation Process

#### Stage 1: Automatic Energy-Based Segmentation

Raw continuous audio (117 minutes) → Auto-segmentation

```
Process:
1. Load audio at 44.1 kHz
2. Apply noise reduction (noisereduce library)
3. Band-pass filter (2-16 kHz, 6th order Butterworth)
4. Compute short-time RMS energy (20 ms frames, 10 ms hop)
5. Apply adaptive threshold: mean(energy) + 0.5 × std(energy)
6. Morphological processing (connected component labeling)
7. Extract segments with ≥50 ms duration
8. Add ±100 ms padding buffers

Output: ~5,000 candidate segments
```

**Time Resolution:**
- Frame Length: 20 ms
- Frame Hop: 10 ms (50% overlap)
- Minimum Segment: 50 ms
- Maximum Segment: ~300 ms
- Buffer: ±100 ms (symmetric padding)

#### Stage 2: Three-Rater Manual Annotation

Three SCA-certified professional roasters independently annotated each candidate segment:

**Annotation Process:**

```
For each candidate segment:
│
├─ Visual Inspection (Spectral Analysis)
│  ├─ Display STFT (n_fft=2048, hop=512)
│  ├─ Observe spectral patterns:
│  │  • Cracks: Vertical striations at 2-14 kHz
│  │  • Noise: Horizontal striations, low frequency dominant
│  └─ Initial classification: {crack, noise, uncertain}
│
├─ Auditory Verification (Acoustic Confirmation)
│  ├─ Listen to original audio
│  ├─ Listen to band-pass filtered version (2-16 kHz)
│  ├─ Classify by auditory signature:
│  │  • First Crack: "pop" sound (clear, high-pitched)
│  │  • Second Crack: "snap" sound (sharp, rapid)
│  │  • Noise: "hum/vibration" (continuous, low-pitched)
│  └─ Independent judgment (no communication between raters)
│
└─ Consensus Voting
   ├─ Require: 3/3 raters agree
   ├─ If unanimous → Include in final dataset
   └─ If any disagreement → Exclude from dataset
```

#### Stage 3: Quality Control

**Rejection Criteria:**

```
Rejected Samples:
1. Duration < 50 ms or > 300 ms
2. RMS energy below noise floor
3. Inconsistent annotation (not 3/3 agreement)
4. Uncertain or ambiguous audio

Acceptance Criteria:
1. All three raters classify identically
2. Duration within 50-300 ms
3. Clear spectral signature
4. Unambiguous auditory signal
```

#### Final Dataset

```
Initial Candidates:    ~5,000 segments
After Length Filter:   ~4,500 segments
After Consensus Vote:   4,301 segments ✓
Rejected:               ~700 segments (14%)
Final Annotation Rate:  100% inter-rater agreement (3/3)
```

---

## Audio Characteristics

### Acoustic Properties by Class

**Class 0: Background Noise**
- Frequency Content: Low (< 2 kHz dominant)
- Pattern: Continuous or slowly varying
- Source: Drum rotation, fan noise, airflow
- Energy Profile: Steady, non-transient
- Typical Duration: > 200 ms (often very long)

**Class 1: First Crack**
- Frequency Content: High (2-14 kHz, peak ~4-8 kHz)
- Pattern: Vertical striations in spectrogram
- Source: Bean expansion, inner structure rupture
- Energy Profile: Sharp transient onset
- Typical Duration: 50-150 ms
- Acoustic Character: "pop" or "crackle"
- Temperature Range: ~175-185°C

**Class 2: Second Crack**
- Frequency Content: High (2-14 kHz, peak ~6-10 kHz)
- Pattern: Vertical striations, similar to first crack
- Source: Cellulose matrix degradation
- Energy Profile: Sharp transient, similar to first crack
- Typical Duration: 50-150 ms
- Acoustic Character: "snap" or "rapid crackling"
- Temperature Range: ~220-226°C
- Distinguishing Feature: More uniform, consistent sound

### Spectral Characteristics

**STFT Parameters:**
- FFT Size: 2048 samples (~46 ms @ 44.1 kHz)
- Hop Length: 512 samples (~11.6 ms)
- Window: Hann window
- Frequency Resolution: ~21.5 Hz
- Time Resolution: ~11.6 ms

**Energy Distribution:**
- First Crack: ~60% energy in 4-8 kHz band
- Second Crack: ~55% energy in 6-10 kHz band
- Background: ~80% energy in < 2 kHz

---

## Usage & Validation

### Training & Testing

```python
from pathlib import Path
import librosa
import numpy as np

# Load dataset
train_dir = Path("datasets/train")
val_dir = Path("datasets/val")
test_dir = Path("datasets/test")

# Example: Load all training data
def load_split(split_dir):
    X, y = [], []
    for class_id in [0, 1, 2]:
        class_dir = split_dir / str(class_id)
        for wav_file in class_dir.glob("*.wav"):
            y_audio, sr = librosa.load(wav_file, sr=44100)
            X.append(y_audio)
            y.append(class_id)
    return X, y

X_train, y_train = load_split(train_dir)
X_val, y_val = load_split(val_dir)
X_test, y_test = load_split(test_dir)

print(f"Training: {len(y_train)} samples")
print(f"Validation: {len(y_val)} samples")
print(f"Testing: {len(y_test)} samples")
```

### Reproducibility

**All random operations use fixed seed:**
```python
SEED = 42
np.random.seed(SEED)  # NumPy
random.seed(SEED)     # Python random
torch.manual_seed(SEED)  # PyTorch (if used)
tf.random.set_seed(SEED)  # TensorFlow (if used)
```

**Data Split Strategy:**
- Stratified random split (maintains class proportions)
- 70% train, 15% validation, 15% test
- Same seed for all splits
- No mixing of samples from same roasting trial in train/test

---

## Reference Baseline

A simple baseline classification system is provided in the main repository:

```bash
# Basic RMS + peak detection (baseline)
python three_method_comparison.py

# Expected baseline performance:
# - Pure loudness threshold: ~76% accuracy
# - RMS + peak detection: ~87% accuracy
# - Adaptive energy detection: ~96% accuracy
```

---

## Citation

If you use this dataset in your research, please cite:

```bibtex
@dataset{coffee_roasting_acoustic_2025,
  title={Coffee Roasting Acoustic Crack Detection Dataset},
  author={Shi, Haojun and Li, Zhijian and He, Wenmeng},
  year={2025},
  institution={Beijing Normal-Hong Kong Baptist University},
  doi={10.5281/zenodo.XXXXXXX},  # Will be assigned upon upload
  url={https://github.com/YOUR_USERNAME/coffee-roasting-acoustic-dataset}
}
```

And cite the paper:

```bibtex
@article{shi_coffee_2025,
  title={Augmenting Sensory Perception in Coffee Roasting Automation:
         A Machine Learning Framework for Acoustic-Based Crack Detection},
  author={Shi, Haojun and Li, Zhijian and He, Wenmeng},
  journal={Applied Food Research},
  year={2025}
}
```

---

## License

This dataset is licensed under **Creative Commons Attribution 4.0 International (CC-BY-4.0)**.

You are free to:
- ✅ Share and copy the dataset
- ✅ Adapt and build upon the dataset
- ✅ Use for commercial purposes

Under the conditions:
- ⚠️ You must give appropriate credit
- ⚠️ You must provide a link to the license
- ⚠️ You must indicate if changes were made

See [LICENSE](LICENSE) for full terms.

---

## Acknowledgments

This work was supported by:
- Guangdong Provincial Department of Education Scientific Research Project (Grant No. 2022KQNCX103)
- Guangdong Association for Science and Technology Project (Grant No. GDKP2025-2-026)
- Guangdong Provincial Education Science Planning Project (Grant No. 2025GXJK0625)
- Guangdong Provincial Key Laboratory IRADS (2022B1212010006)

---

## Contribution

Found an issue? Have suggestions for improvements?

Open an issue or submit a pull request on GitHub:
```
https://github.com/YOUR_USERNAME/coffee-roasting-acoustic-dataset
```

---

## Contact

For questions about the dataset, please contact:

**Corresponding Authors:**
- Dr. Wenmeng He (wenmenghe@bnbu.edu.cn)
- Dr. Zhijian Li

Department of Life Science / Department of Statistics
Beijing Normal-Hong Kong Baptist University
Zhuhai, Guangdong, China

---

**Last Updated:** 2025-02-28
**Dataset Version:** 1.0
**Status:** Public Release
