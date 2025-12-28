# LuxFHE Threshold

**Threshold FHE for Distributed Decryption**

[![License](https://img.shields.io/badge/license-Lux%20Research-blue.svg)](LICENSE)

---

## ⚠️ Important IP Notice

**This references Lux's independent threshold implementation:**

- ✅ Core implementation: `lux/node/vms/kmsvm/` (Go)
- ✅ TFHE library: `github.com/luxfi/tfhe` (Go)
- ✅ Post-quantum: ML-KEM (FIPS 203) via `github.com/luxfi/crypto`
- ✅ NO code derived from Zama threshold or tfhe-rs
- ✅ Protected by Lux Industries patent portfolio

---

## Overview

Threshold FHE enables N validators to hold shares of a decryption key, requiring t-of-N collaboration to decrypt. This provides:

- **No single point of failure** - No single party can decrypt
- **Censorship resistance** - Any t validators can participate
- **Quantum safety** - ML-KEM key distribution

## Architecture

Lux implements threshold FHE directly in the node software:

```
github.com/luxfi/node/vms/kmsvm/   <- Go KMS VM
github.com/luxfi/tfhe/             <- Go TFHE library
github.com/luxfi/crypto/mlkem/     <- Post-quantum KEM
github.com/luxfi/lattice/          <- Lattice primitives
```

## Key Innovation: Consensus-Integrated Decryption (PAT-FHE-001)

Instead of separate threshold protocol rounds, Lux integrates decryption shares directly into consensus votes:

```
Traditional Approach:
  Consensus Round 1 → Block Finality
  Threshold Round 2 → Collect Shares
  Threshold Round 3 → Combine & Decrypt

Lux Approach:
  Single Round → Block Finality + Decryption
  (Shares piggybacked on consensus votes)
```

This reduces latency and leverages existing stake-weighted validator sampling.

## Usage

Threshold operations are accessed via the K-Chain VM RPC:

```typescript
import { LuxKMS } from '@luxfhe/kms';

const kms = new LuxKMS({ network: 'mainnet' });

// Generate threshold key (distributed to validators)
const { publicKey, keyId } = await kms.generateKey({
  threshold: 67,
  totalShares: 100,
});

// Request decryption (collects shares automatically)
const plaintext = await kms.decrypt(ciphertext, { keyId });
```

## Security Parameters

| Network | Threshold | Validators | Security |
|---------|-----------|------------|----------|
| Testnet | 5-of-10 | 10 | Testing |
| Mainnet | 67-of-100 | 100 | Production |

## Academic Foundation

Based on threshold cryptography research:
- Shamir Secret Sharing (1979)
- Threshold signatures (multiple papers)
- Our own innovations (PAT-FHE-001 through PAT-FHE-009)

## License

**Lux Research License with Patent Reservation**

- ✅ Free for research and academic use
- ✅ Free on Lux Network (mainnet/testnet)
- 📧 Commercial license required for other networks

Contact: oss@lux.network

## Related

| Repository | Description |
|------------|-------------|
| [luxfi/node](https://github.com/luxfi/node) | Contains kmsvm |
| [luxfi/tfhe](https://github.com/luxfi/tfhe) | Go TFHE library |
| [luxfi/crypto](https://github.com/luxfi/crypto) | ML-KEM |

---

© 2020-2025 Lux Industries Inc. All rights reserved.
