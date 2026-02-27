# Coffee Roasting Acoustic Crack Detection Dataset

A comprehensive multi-origin coffee roasting audio dataset with manual annotations for automated crack detection research.

## Dataset Overview

- **Total Samples**: 4,301 annotated audio events
- **Total Duration**: ~117 minutes of continuous recording  
- **Roasting Trials**: 9 (3 replicas × 3 origins)
- **File Format**: WAV (44.1 kHz, 16-bit, mono)
- **Total Size**: ~113 MB

## Quick Start

```bash
git clone https://github.com/Theseus-427/coffee-roasting-acoustic-dataset.git
cd coffee-roasting-acoustic-dataset
```

## Dataset Structure

```
datasets/
├── train/  (70% - 3,010 samples)
│   ├── 0/  (Background - 1,660 files)
│   ├── 1/  (First Crack - 540 files)  
│   └── 2/  (Second Crack - 810 files)
├── val/    (15% - 645 samples)
│   ├── 0/, 1/, 2/
└── test/   (15% - 646 samples)
    ├── 0/, 1/, 2/
```

## Sample Distribution

| Class | Event Type | Count | % |
|-------|-----------|-------|---|
| 0 | Background Noise | 2,373 | 55.2% |
| 1 | First Crack | 766 | 17.8% |
| 2 | Second Crack | 1,162 | 27.0% |

## Documentation

- [DATASET.md](DATASET.md) - Detailed dataset documentation
- [Reviewer_Response_R3_Q2.md](Reviewer_Response_R3_Q2.md) - Data segmentation methodology

## Citation

If you use this dataset in your research, please cite:

```bibtex
@dataset{coffee_roasting_acoustic_2025,
  title={Coffee Roasting Acoustic Crack Detection Dataset},
  author={Shi, Haojun and Li, Zhijian and He, Wenmeng},
  year={2025},
  institution={Beijing Normal-Hong Kong Baptist University},
  url={https://github.com/Theseus-427/coffee-roasting-acoustic-dataset}
}
```

## License

Creative Commons Attribution 4.0 International (CC-BY-4.0)

See [LICENSE](LICENSE) for details.

## Contact

For questions, contact: wenmenghe@bnbu.edu.cn
