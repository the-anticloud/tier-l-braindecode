# Deploy Guide — L_BRAINDECODE
**Tier:** TIER_7_BIOSIGNALS_NEURO | **Stack:** Python 3.11, braindecode 0.8+, PyTorch 2.10+, PAX 27B, AIOSS_FORMAT
**Air-gap capable after initial setup.**

## Prerequisites
Python 3.11+, braindecode 0.8+, PyTorch 2.10+, T4 GPU for training.

## Environment
T4 GPU. 16GB VRAM for larger models. 16GB RAM. MOABB datasets for training.

## AIOSS Integration
```bash
aioss init --module L_BRAINDECODE --output ./l_braindecode.aioss
aioss append --chain ./l_braindecode.aioss --payload ./output.bin --module L_BRAINDECODE
aioss verify --chain ./l_braindecode.aioss
```

## Air-Gap Setup
```bash
pip download -r requirements.txt -d ./wheels/
pip install --no-index --find-links ./wheels/ -r requirements.txt
```

## PAX 27B Harness Wiring
```python
from anticloud_pax import PAXHarness
harness = PAXHarness(
    model_path="./pax-27b-q4.gguf",
    module="L_BRAINDECODE",
    aioss_chain="./L_BRAINDECODE.aioss",
    classification="L5_NARROW_L2_GENERAL"
)
result = harness.process(input_data)
```

## Verification
```bash
aioss verify --chain ./L_BRAINDECODE.aioss --verbose
python -m L_BRAINDECODE.tests.smoke
```
