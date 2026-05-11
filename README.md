# CONNECTML Trainer Kit

Earn cryptocurrency rewards by contributing your compute power to train AI models on the DECONNECT decentralized network.

## Overview

DECONNECT Trainer Kit allows you to:
- **Earn SOL** by training machine learning models
- **Choose what to train** with flexible dataset filters and presets
- **Run automatically** with daemon mode that finds and joins rounds
- **Stay in control** with configurable rewards, datasets, and training parameters

## Quick Start

### 1. Install

```bash
pip install -e . --break-system-packages
```

### 2. Setup (one-time)

```bash
deconnect-trainer setup
```

This interactive wizard will configure:
- Your Solana wallet (private key or keypair file)
- Which datasets you want to train
- Minimum/maximum reward filters
- IPFS credentials (Pinata) for uploading gradients

### 3. Start Earning

```bash
deconnect-trainer start
```

That's it! The daemon will automatically:
1. 👀 Monitor for new training rounds
2. 🎯 Join rounds matching your filters
3. 🏋️ Train models and submit gradients
4. 💰 Claim your rewards

## How It Works

```
┌─────────────────────────────────────────────────────────────┐
│  1. Creator posts a training round with reward             │
│  2. Validator claims the round                              │
│  3. YOU join and train the model locally                    │
│  4. Submit your gradients (model improvements)              │
│  5. Validator evaluates contributions                       │
│  6. Rewards distributed based on contribution quality       │
└─────────────────────────────────────────────────────────────┘
```

Your data stays local. Only gradients (weight updates) are uploaded.

---

## Commands Reference

### `deconnect-trainer setup`

Interactive setup wizard. Configures wallet, datasets, rewards, and IPFS.

```bash
deconnect-trainer setup
```

### `deconnect-trainer start`

**Main command.** Starts the training daemon that automatically finds, joins, trains, and claims rewards.

```bash
# Basic start (uses saved config)
deconnect-trainer start

# With preset
deconnect-trainer start --preset image-small

# With filters
deconnect-trainer start --dataset cifar10,mnist --min-reward 0.01

# All options
deconnect-trainer start \
  --preset image-all \
  --dataset cifar10 \
  --min-reward 0.01 \
  --max-reward 1.0 \
  --epochs 2 \
  --max-parallel 3 \
  --no-auto-train \
  --no-auto-claim
```

| Option | Description |
|--------|-------------|
| `--preset NAME` | Use a preset configuration |
| `--dataset LIST` | Comma-separated datasets to train |
| `--min-reward SOL` | Minimum reward to accept |
| `--max-reward SOL` | Maximum reward to accept |
| `--epochs N` | Training epochs per round |
| `--max-parallel N` | Max simultaneous rounds |
| `--no-auto-train` | Only join, don't train automatically |
| `--no-auto-claim` | Don't auto-claim rewards |

### `deconnect-trainer config`

View and edit configuration.

```bash
# Show current config
deconnect-trainer config --show

# Set a value
deconnect-trainer config --set epochs=3
deconnect-trainer config --set min_reward=0.01
deconnect-trainer config --set datasets=cifar10,mnist

# Apply a preset
deconnect-trainer config --preset image-small

# List available presets
deconnect-trainer config --preset list

# Add dataset mapping (when creator requests X, use Y)
deconnect-trainer config --add-mapping custom:cifar10

# Add custom dataset path
deconnect-trainer config --add-path cifar10:/data/my_cifar

# Reset to defaults
deconnect-trainer config --reset
```

### `deconnect-trainer list`

List available training rounds.

```bash
# All rounds
deconnect-trainer list

# Filter by status
deconnect-trainer list --status training
```

### `deconnect-trainer join <round_id>`

Manually join a specific round.

```bash
deconnect-trainer join 42
```

### `deconnect-trainer train <round_id>`

Manually train and submit gradients for a round.

```bash
# Default (1 epoch)
deconnect-trainer train 42

# Multiple epochs
deconnect-trainer train 42 --epochs 3

# Quick test (limited batches)
deconnect-trainer train 42 --max-batches 50
```

### `deconnect-trainer claim <round_id>`

Claim your reward after round completion.

```bash
deconnect-trainer claim 42
```

### `deconnect-trainer status <round_id>`

Check the status of a specific round.

```bash
deconnect-trainer status 42
```

---

## Presets

Presets are pre-configured templates for common use cases.

| Preset | Description | Datasets |
|--------|-------------|----------|
| `image-all` | All image datasets | cifar10, cifar100, mnist, fashionmnist, emnist, kmnist, svhn |
| `image-small` | Fast image training | mnist, fashionmnist, cifar10 |
| `text-all` | Text classification | imdb, sst2, agnews |
| `cifar-only` | CIFAR datasets | cifar10, cifar100 |
| `mnist-only` | MNIST-like datasets | mnist, fashionmnist, emnist, kmnist |
| `high-reward` | High paying rounds only | any (min 0.1 SOL) |
| `quick-test` | Quick testing | cifar10, mnist (50 batches max) |

```bash
# Apply preset permanently
deconnect-trainer config --preset image-small

# Or use for single session
deconnect-trainer start --preset mnist-only
```

---

## Configuration

### Config File Location

`~/.deconnect-trainer/config.json`

### All Settings

| Setting | Default | Description |
|---------|---------|-------------|
| `private_key` | - | Base58 private key |
| `keypair_path` | ~/.config/solana/id.json | Path to Solana keypair |
| `datasets` | [] (any) | Datasets to accept |
| `min_reward` | 0 | Minimum reward in SOL |
| `max_reward` | 0 (unlimited) | Maximum reward in SOL |
| `epochs` | 1 | Training epochs |
| `batch_size` | 32 | Training batch size |
| `learning_rate` | 0.001 | Learning rate |
| `max_batches` | 0 (all) | Max batches per epoch |
| `max_concurrent_rounds` | 1 | Parallel rounds |
| `auto_train` | true | Auto-train after joining |
| `auto_claim` | true | Auto-claim rewards |
| `poll_interval` | 5 | Seconds between checks |
| `pinata_api_key` | - | Pinata API key |
| `pinata_secret_key` | - | Pinata secret |

### Dataset Mappings

When a creator requests a dataset you don't have, map it to one you do:

```bash
# Creator asks for "custom" → train on cifar10
deconnect-trainer config --add-mapping custom:cifar10

# Creator asks for "proprietary_data" → train on mnist
deconnect-trainer config --add-mapping proprietary_data:mnist
```

### Custom Dataset Paths

Use your own dataset locations:

```bash
deconnect-trainer config --add-path cifar10:/data/datasets/cifar
deconnect-trainer config --add-path mnist:/home/user/ml/mnist
```

---

## Wallet Setup

Three ways to configure your wallet:

### Option 1: Use Solana CLI keypair (recommended)

```bash
# During setup, select option 1
deconnect-trainer setup

# Or set path manually
deconnect-trainer config --set keypair_path=~/.config/solana/id.json
```

### Option 2: Private key in config

```bash
deconnect-trainer config --set private_key=YOUR_BASE58_PRIVATE_KEY
```

### Option 3: Environment variable

```bash
export deconnect_PRIVATE_KEY="your_base58_key"
deconnect-trainer start
```

### Get private key from Solana CLI

```bash
node -e "
const fs = require('fs');
const bs58 = require('bs58');
const key = JSON.parse(fs.readFileSync('$HOME/.config/solana/id.json'));
console.log(bs58.encode(Buffer.from(key)));
"
```

---

## IPFS Setup (Required)

Gradients must be uploaded to IPFS. We recommend Pinata (free tier available).

### Get Pinata Keys

1. Go to https://app.pinata.cloud/keys
2. Create new API key
3. Copy API Key and Secret

### Configure

```bash
# Option 1: During setup
deconnect-trainer setup
# → Select "Setup IPFS (Pinata)"

# Option 2: Environment variables
export PINATA_API_KEY="your_key"
export PINATA_SECRET_KEY="your_secret"

# Option 3: Config
deconnect-trainer config --set pinata_api_key=YOUR_KEY
deconnect-trainer config --set pinata_secret_key=YOUR_SECRET
```

---

## Example Workflows

### Passive Income Mode

Set it and forget it:

```bash
# One-time setup
deconnect-trainer setup
deconnect-trainer config --preset image-all
deconnect-trainer config --set min_reward=0.005

# Run in background
nohup deconnect-trainer start > trainer.log 2>&1 &
```

### Selective Training

Only train specific datasets with good rewards:

```bash
deconnect-trainer start \
  --dataset cifar10,mnist \
  --min-reward 0.01 \
  --epochs 2
```

### Testing

Quick test with limited training:

```bash
deconnect-trainer start --preset quick-test
```

### Manual Mode

Full control over each step:

```bash
# List available rounds
deconnect-trainer list

# Join specific round
deconnect-trainer join 42

# Wait for training phase, then train
deconnect-trainer train 42 --epochs 2

# Claim reward after completion
deconnect-trainer claim 42
```

---

## Daemon Output

```
════════════════════════════════════════════════════════════════
  🤖 deconnect Trainer Daemon
════════════════════════════════════════════════════════════════
  Wallet: AdNrB7azAFoDdrKrxkCk...

  ⚙️  Settings:
     Preset:       image-small
     Datasets:     cifar10, mnist, fashionmnist
     Min reward:   0.01 SOL
     Max reward:   unlimited SOL
     Max parallel: 1
     Epochs:       1
     Auto-train:   True
     Auto-claim:   True
════════════════════════════════════════════════════════════════

  👀 Listening for rounds with validators... (Ctrl+C to stop)

[14:23:15] 🎯 NEW ROUND #42
         Dataset: Cifar10
         Reward:  0.0500 SOL
         Trainers: 0
   ✅ Joined! TX: 3qMrrKtv...

  [14:23:20] ⏳ waiting: 1

[14:24:01] 🏋️ Round #42 started training!

📥 Downloading model from IPFS...
🔧 Loading model...
🏋️ Training on Cifar10...
   Epoch 1/1: Loss=1.23, Acc=65%
📤 Uploading gradients...
⛓️ Submitting to blockchain...

✅ Training complete!

[14:28:45] 💰 Round #42 completed! Claiming reward...
   ✅ Claimed! TX: 5xyz...
```

---

## Supported Datasets

### Image Classification
- CIFAR-10, CIFAR-100
- MNIST, Fashion-MNIST, EMNIST, KMNIST
- SVHN

### Text Classification
- IMDB (sentiment)
- SST-2 (sentiment)
- AG News (topic)

Datasets are automatically downloaded via torchvision when needed.

---

## Hardware Requirements

| Component | Minimum | Recommended |
|-----------|---------|-------------|
| CPU | Any modern CPU | Multi-core |
| RAM | 4 GB | 8+ GB |
| GPU | Not required | NVIDIA with CUDA |
| Storage | 5 GB | 20+ GB (for datasets) |
| Network | Stable connection | Fast upload for gradients |

GPU is automatically detected and used if available.

---

## Troubleshooting

### "No private key found"

```bash
# Check config
deconnect-trainer config --show

# Set wallet
deconnect-trainer config --set private_key=YOUR_KEY
# or
deconnect-trainer config --set keypair_path=~/.config/solana/id.json
```

### "Failed to upload gradients"

IPFS not configured:
```bash
deconnect-trainer config --set pinata_api_key=YOUR_KEY
deconnect-trainer config --set pinata_secret_key=YOUR_SECRET
```

### "Dataset not found"

Add a mapping for unknown datasets:
```bash
deconnect-trainer config --add-mapping unknown_dataset:cifar10
```

### Check round status

```bash
deconnect-trainer status 42
```

---

## Security Notes

- Private keys are stored locally in `~/.deconnect-trainer/config.json`
- Only gradients (weight updates) are uploaded, not your data
- Rewards are claimed directly to your wallet
- You can stop the daemon anytime with Ctrl+C

---

## License

MIT
