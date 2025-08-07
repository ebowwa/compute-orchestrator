# Compute Orchestrator 🚀

> Intelligent system for acquiring and managing compute resources at the lowest possible cost across spot, fixed, and free-tier providers.

## The Problem

GPU compute costs $3+/hour on-demand. But the same compute is available for:
- **$0/hour** on free tiers (Kaggle, Colab, Gradient)
- **$0.10-0.30/hour** on spot instances
- **$0.005/hour** for CPU orchestration on Hetzner

This system automatically finds and acquires the cheapest compute for your workload.

## The Solution

```mermaid
graph LR
    A[Workload Request] --> B{Orchestrator}
    B --> C[Free Tier Check]
    B --> D[Spot Price Check]
    B --> E[Fixed Price Check]
    C --> F[Kaggle/Colab/Gradient]
    D --> G[AWS/GCP/Azure Spot]
    E --> H[Hetzner/OVH/DO]
    F --> I[Provision & Connect]
    G --> I
    H --> I
    I --> J[Return Credentials]
```

## 🎯 Core Philosophy

**This repo handles ACQUISITION** - getting compute resources  
**[new-development-machine-setup](https://github.com/ebowwa/new-development-machine-setup) handles CONFIGURATION** - setting them up

Together they create a complete compute management pipeline.

## 🚀 Quick Start

```bash
# Clone the repository
git clone https://github.com/ebowwa/compute-orchestrator
cd compute-orchestrator

# Install dependencies
pip install -r requirements.txt

# Configure your providers
cp .env.example .env
# Edit .env with your API keys

# Acquire compute
python orchestrator.py acquire \
  --type gpu \
  --model t4 \
  --max-price 0.50 \
  --duration 2h
```

## 📊 Provider Matrix

| Provider | Type | GPU Models | CPU | Price | Limits |
|----------|------|------------|-----|-------|--------|
| **Kaggle** | Free Tier | P100 | 4 cores | $0/hr | 30 hrs/week |
| **Colab** | Free Tier | T4 | 2 cores | $0/hr | ~12 hrs/day |
| **Gradient** | Free Tier | Various | 8 cores | $0/hr | 6 hrs/session |
| **Lightning AI** | Free Tier | T4 | 4 cores | $0/hr | 30 hrs/month |
| **AWS Spot** | Spot | T4, V100, A100 | Variable | $0.10-2.00/hr | Until preempted |
| **GCP Spot** | Spot | T4, V100, A100 | Variable | $0.15-2.50/hr | 24hr max |
| **Azure Spot** | Spot | T4, V100 | Variable | $0.20-2.00/hr | Until evicted |
| **Hetzner** | Fixed | CPU only | 2-48 cores | $0.005-0.10/hr | None |
| **Vast.ai** | P2P | Various | Variable | $0.10-1.00/hr | None |
| **RunPod** | Fixed/Spot | 3090, A100 | Variable | $0.20-2.00/hr | None |

## 🏗 Architecture

### Core Components

```python
orchestrator/
├── providers/           # Provider adapters
│   ├── free_tier/      # Kaggle, Colab, Gradient
│   ├── spot/           # AWS, GCP, Azure
│   ├── fixed/          # Hetzner, OVH, DO
│   └── p2p/            # Vast.ai, Salad.io
├── scheduler/          # Job scheduling & queue
├── monitor/            # Price & availability monitoring
├── allocator/          # Resource allocation logic
└── api/                # REST API & SDK
```

### Key Features

- **🔍 Price Discovery**: Real-time price monitoring across all providers
- **🎯 Smart Routing**: Automatically selects cheapest option for workload
- **♻️ Automatic Failover**: Seamlessly moves workloads when spot instances are preempted
- **📈 Usage Optimization**: Maximizes free tier usage before paid options
- **🔐 Credential Management**: Secure handling of multi-provider credentials
- **📊 Cost Analytics**: Track spending and savings across providers

## 💻 Usage Examples

### Basic GPU Request
```python
from orchestrator import ComputeOrchestrator

orch = ComputeOrchestrator()
instance = orch.acquire(
    gpu="t4",
    duration_hours=2,
    max_price_per_hour=0.50
)
print(f"Acquired {instance.provider} instance at ${instance.price}/hr")
print(f"SSH: ssh {instance.user}@{instance.ip}")
```

### Batch Processing with Failover
```python
# Process 100 jobs using cheapest available compute
jobs = load_jobs("jobs.json")
results = orch.process_batch(
    jobs=jobs,
    parallel=10,
    use_free_tier=True,
    failover=True
)
```

### Kubernetes Integration
```yaml
apiVersion: compute.io/v1
kind: ComputeRequest
metadata:
  name: training-job
spec:
  resources:
    gpu: v100
    cpu: 8
    memory: 32Gi
  maxPrice: 0.75
  duration: 4h
  providers:
    - kaggle
    - aws-spot
    - gcp-spot
```

## 🔧 Configuration

### Environment Variables
```bash
# Free Tier Providers
KAGGLE_USERNAME=your_username
KAGGLE_KEY=your_api_key
COLAB_COOKIES=your_cookies  # For automation

# Cloud Providers
AWS_ACCESS_KEY_ID=your_key
AWS_SECRET_ACCESS_KEY=your_secret
GCP_SERVICE_ACCOUNT_JSON=path/to/key.json
AZURE_SUBSCRIPTION_ID=your_id

# Fixed Providers
HETZNER_API_TOKEN=your_token
DIGITALOCEAN_TOKEN=your_token

# P2P Networks
VAST_API_KEY=your_key
RUNPOD_API_KEY=your_key
```

## 🎬 Real-World Scenarios

### Scenario 1: ML Training
```bash
# Start with free Kaggle GPU (30 hrs/week)
# Overflow to AWS Spot ($0.10/hr)
# Failover to GCP if preempted
python orchestrator.py train --model llama2 --fallback-chain kaggle,aws-spot,gcp-spot
```

### Scenario 2: Batch Inference
```bash
# Use all free tiers in parallel
# Scale to spot instances as needed
python orchestrator.py batch --input data/ --parallel 20 --optimize-cost
```

### Scenario 3: Development
```bash
# Get cheapest available GPU for development
python orchestrator.py dev --gpu any --max-price 0.25
```

## 📈 Cost Savings

| Workload | Traditional | With Orchestrator | Savings |
|----------|-------------|-------------------|---------|
| 100 hrs ML training | $300 (AWS on-demand) | $15 (Kaggle + Spot) | 95% |
| 24/7 API serving | $2,160/mo | $108/mo (Hetzner + cache) | 95% |
| Batch processing | $500 | $25 (Free tiers + spot) | 95% |

## 🤝 Integration with Ecosystem

1. **Acquire** (This Repo): Get compute resources
2. **Configure** ([new-development-machine-setup](https://github.com/ebowwa/new-development-machine-setup)): Setup Tailscale, Claude, GitHub
3. **Execute** (Your Code): Run workloads
4. **Monitor** (Coming Soon): Track usage and costs

## 🛠 Development Status

- [x] Core orchestration engine
- [x] Hetzner provider
- [ ] Kaggle automation
- [ ] Colab automation
- [ ] AWS Spot integration
- [ ] GCP Preemptible integration
- [ ] Web dashboard
- [ ] REST API
- [ ] Python SDK
- [ ] Kubernetes operator
- [ ] Terraform provider

## 📚 Documentation

- [Getting Started Guide](docs/getting-started.md)
- [Provider Setup](docs/providers.md)
- [API Reference](docs/api.md)
- [Architecture Deep Dive](docs/architecture.md)
- [Contributing Guide](CONTRIBUTING.md)

## 🤝 Contributing

We welcome contributions! See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

Key areas needing help:
- Provider integrations
- Documentation
- Testing
- UI/Dashboard
- Kubernetes operator

## 📄 License

MIT - See [LICENSE](LICENSE) for details.

## 🔗 Related Projects

- [new-development-machine-setup](https://github.com/ebowwa/new-development-machine-setup) - Configure acquired compute
- [GPU Orchestration Discussion](https://github.com/ebowwa/ebowwa/issues/48) - Original concept discussion
- [Brev.dev](https://brev.dev) - Similar commercial solution
- [RunPod](https://runpod.io) - GPU marketplace
- [Vast.ai](https://vast.ai) - P2P GPU marketplace

## 💡 Vision

Making compute as accessible as electricity - you shouldn't think about where it comes from, just that it's available when you need it at the lowest possible cost.

---

Built with ❤️ by the open-source community. Star ⭐ this repo if you find it useful!