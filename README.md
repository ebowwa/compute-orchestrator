# Compute Orchestrator 🚀

Intelligent compute orchestration system that automatically acquires and manages GPU/CPU resources across multiple providers at the lowest possible cost.

## 🎯 Purpose

This system **acquires and accesses** compute resources, while [new-development-machine-setup](https://github.com/ebowwa/new-development-machine-setup) **configures** them once acquired.

## 📋 Architecture Flow

```
1. ACQUIRE → This Repo (compute-orchestrator)
   ├── Find cheapest compute
   ├── Provision instances
   ├── Manage lifecycle
   └── Handle billing

2. CONFIGURE → new-development-machine-setup
   ├── Install Tailscale
   ├── Setup Claude Code
   ├── Configure GitHub
   └── Join mesh network

3. EXECUTE → Your Workload
   └── Run on configured compute
```

## 🛠 Core Components

### Resource Acquisition
- **Spot Instance Manager**: AWS, GCP, Azure spot bidding
- **Free Tier Optimizer**: Kaggle, Colab, Gradient rotation
- **Fixed Price Monitor**: Hetzner, OVH, DigitalOcean
- **P2P Network**: Vast.ai, Salad.io integration

### Orchestration Engine
```python
class ComputeOrchestrator:
    def acquire_compute(self, requirements):
        # 1. Check free tiers first
        # 2. Compare spot prices
        # 3. Evaluate fixed options
        # 4. Select optimal provider
        # 5. Provision resource
        # 6. Return connection details
        pass
```

### Provider Adapters
- `providers/aws/` - EC2 Spot Fleet API
- `providers/gcp/` - Preemptible VM management
- `providers/kaggle/` - Kernel automation
- `providers/colab/` - Notebook orchestration
- `providers/hetzner/` - Cloud API integration

## 🚦 Quick Start

```bash
# Install
git clone https://github.com/ebowwa/compute-orchestrator
cd compute-orchestrator
pip install -r requirements.txt

# Configure providers
cp .env.example .env
# Add your API keys

# Acquire compute
python orchestrator.py acquire --cpus 4 --ram 16 --gpu t4
```

## 💰 Cost Optimization

| Provider | Type | GPU | Cost/hr | Free Tier |
|----------|------|-----|---------|-----------|
| Kaggle | Free | P100 | $0 | 30hr/week |
| Colab | Free | T4 | $0 | 12hr/day |
| AWS Spot | Spot | T4 | ~$0.10 | No |
| GCP Spot | Spot | T4 | ~$0.15 | No |
| Hetzner | Fixed | None | ~$0.005 | No |

## 🔗 Integration

Once compute is acquired, use [new-development-machine-setup](https://github.com/ebowwa/new-development-machine-setup) to:
1. Join Tailscale network
2. Enable Claude Code
3. Setup GitHub persistence
4. Configure development environment

## 📊 Features

- [x] Multi-provider support
- [x] Cost optimization algorithm
- [x] Automatic failover
- [ ] Web dashboard
- [ ] REST API
- [ ] Kubernetes operator
- [ ] Terraform provider
- [ ] Usage analytics

## 🤝 Contributing

This project is part of the larger compute marketplace ecosystem. See [Issue #48](https://github.com/ebowwa/ebowwa/issues/48) for discussion.

## 📄 License

MIT
