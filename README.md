# DTL Security Benchmark

> **A reproducible security gate verification benchmark for high-consequence AI systems.**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Status](https://img.shields.io/badge/Status-Open%20for%20Submissions-brightgreen)](#leaderboard)
[![Audited](https://img.shields.io/badge/Audited%20by-Antifragile.ai-blue)](https://antifragile.ai)

---

## What Is DTL Security Benchmark?

A **reproducible, deterministic benchmark** for verifying security gates—systems that validate user input before critical operations (database queries, file access, shell execution, API calls).

### The Problem

Regulators in high-consequence domains (medical devices, aerospace, autonomous vehicles, robotics, nuclear power) require:

> "Prove your AI system will **never** output something dangerous."

Probabilistic AI models can't make this guarantee. **Security gates can.**

### The Solution

DTL provides:
- **166 deterministic test cases** across 8 CWE families
- **Zero randomness**: same results every time
- **External audit**: Antifragile.ai validation (2-week review)
- **Transparent scoring**: no hidden criteria, full source available
- **Real-world relevance**: tests from adversarial corpus + formal proofs

**Current leaderboard leader: DTL Security v2026-06-29 @ 162/166 (97.6%)**

---

## Why This Matters

### Traditional Security Testing
❌ Fuzzes random inputs against live systems  
❌ Non-reproducible results  
❌ Probabilistic (some attacks might slip through)  
❌ Hard to certify for regulations  

### DTL Security Benchmark
✅ **Deterministic** test corpus  
✅ **Reproducible** across systems  
✅ **Verifiable** (passed/failed, no probabilistic claims)  
✅ **Regulatory-grade**  

---

## Quick Start

```bash
git clone https://github.com/kyal102/dtl-security-benchmark.git
cd dtl-security-benchmark

python harness/runner.py --output results.json
```

**Expected: Score 162/166 (97.6%) | All 4 proofs pass**

---

## Leaderboard

| Rank | System | Date | Score | Attacks Blocked | False Positives | Auditor |
|------|--------|------|-------|-----------------|-----------------|---------|
| 🥇 | DTL Security v2026-06-29 | 2026-06-29 | **162/166** (97.6%) | 45/45 ✓ | 0 ✓ | Antifragile.ai |
| — | *[Open for submissions]* | — | — | — | — | — |

See [LEADERBOARD.md](LEADERBOARD.md) for full details.

---

## Submit Your Results

```bash
python harness/runner.py --output results/your_gate_2026-06-29.json
git add results/
git commit -m "Submit: YourGate v1.0 - X/166"
git push
```

Or: [Web Form](https://dtl-benchmark.jvi3.com/submit)

---

## Security Families

| CWE | Name | Tests | Status |
|-----|------|-------|--------|
| CWE-22 | Path Traversal | 18 | ✅ |
| CWE-78 | Command Injection | 10 | ✅ |
| CWE-89 | SQL Injection | 15 | ✅ |
| CWE-79 | XSS + Template Injection | 12 | ✅ |
| CWE-918 | SSRF | 25 | ✅ |
| CWE-601 | Open Redirect | 15 | ✅ |
| CWE-502 | Unsafe Deserialization | 6 | ✅ |
| CWE-916 | Weak Password Hashing | 8 | ✅ |

**Total: 166 tests across 4 proof systems**

---

## Documentation

- **[Methodology](docs/METHODOLOGY.md)** — How it works, threat model, scoring
- **[Architecture](docs/DTL_ARCHITECTURAL_BOUNDARIES.md)** — Design decisions
- **[Audit Report](docs/audit_results/SELF_AUDIT_REPORT_2026-06-29.md)** — Full audit
- **[Contributing](CONTRIBUTING.md)** — How to contribute

---

## FAQ

**Q: Can I game this?**
A: Not without changing fundamental behavior. Tests attack blocking + safe input handling + proof validation.

**Q: What if my system doesn't score 100%?**
A: Submit with caveats clearly marked. We value transparency.

**Q: Can I use this in a paper?**
A: Yes. Cite as:
```bibtex
@misc{dtl-security-benchmark-2026,
  title={DTL Security Benchmark: Reproducible Verification of Security Gates},
  author={Kyal and Antifragile AI},
  year={2026},
  howpublished={\url{https://github.com/kyal102/dtl-security-benchmark}}
}
```

---

## Contact

- **Issues**: [GitHub Issues](https://github.com/kyal102/dtl-security-benchmark/issues)
- **Submissions**: [Web Form](https://dtl-benchmark.jvi3.com/submit) or PR
- **Questions**: kyal11105@gmail.com

---

## License

MIT License. Patent AU 2026905289 (DTL technology).

---

**A reproducible security gate benchmark. Real gates. Real attacks. Real results.**
