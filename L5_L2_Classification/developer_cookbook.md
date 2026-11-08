# Developer Cookbook — L_BRAINDECODE
**Stack:** Python 3.11, braindecode 0.8+, PyTorch 2.10+, PAX 27B, AIOSS_FORMAT
**Domain:** BrainDecode: deep learning EEG decoding for Anticloud BCI clinical systems

## Train EEGNet for motor imagery
```python
from l_braindecode import BrainDecodeTrainer

trainer = BrainDecodeTrainer(
    architecture="EEGNet",
    n_channels=8,
    n_classes=4,  # left, right, feet, tongue
    pax_model="./pax-27b-q4.gguf",
    aioss_chain="./braindecode.aioss"
)

result = trainer.train(
    train_data=train_epochs,  # MNE Epochs
    valid_data=valid_epochs,
    n_epochs=50
)
print(f"Validation accuracy: {result.valid_accuracy:.3f}")
print(f"Certified model: {result.model_path}")
print(f"Chain: {result.chain_hash}")
```

## Real-time inference
```python
pred = trainer.predict(eeg_epoch)
print(f"Class: {pred.class_name}, Confidence: {pred.confidence:.2f}")
```

## AIOSS Chain Append
```python
import hashlib, time

def aioss_append(chain_path, payload: bytes, module_id: str):
    entry_hash = hashlib.sha3_256(payload).digest()
    ts = int(time.time_ns()).to_bytes(8, 'big')
    with open(chain_path, 'rb') as f:
        f.seek(-32, 2); prev_hash = f.read(32)
    new_hash = hashlib.sha3_256(prev_hash + entry_hash + ts).digest()
    with open(chain_path, 'ab') as f:
        f.write(ts + entry_hash + new_hash)
    return new_hash.hex()
```
