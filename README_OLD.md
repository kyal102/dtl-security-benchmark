# DTL Security Benchmark
## The First Reproducible Security Gate Verification Benchmark

**Status**: Open for submissions | **Last Updated**: 2026-06-29

---

## What This Is

A **public leaderboard for security gates** — systems that verify user input is safe before it reaches dangerous operations (database queries, file access, shell execution, etc.).

Unlike traditional security testing (which runs attacks against live systems), **DTL tests the gate itself**:
- Can your gate block path traversal? SQL injection? SSRF? XSS?
- Does it block attacks without falsely rejecting safe input?
- Can weak models emit wrong answers that a correct gate still filters?

**The guarantee**: If your system scores 100/100 on this benchmark, it provably blocks the tested attack classes with zero false positives.

---

## Benchmark Overview

### Test Corpus
- **166 test cases** across 8 security families
- **45 runtime guard tests** (attack blocking + false positive validation)
- **28 proof validation tests** (proof-carrying, sink routing, synthesizer verification)
- **93 fuzzer adversarial cases** (path traversal, SSRF, open redirect, XSS)

### Scoring

```
Total Score = (Tests Passed / Total Tests) × 100

Passing Criteria:
✓ All attack payloads blocked (0 bypasses)
✓ All safe inputs allowed (0 false positives)
✓ All proofs validated (proof-carrying, sink routing, etc.)
```

### Security Families Tested

| CWE | Name | Tests | Attacks | Safe Inputs |
|-----|------|-------|---------|------------|
| CWE-22 | Path Traversal | 18 | 6 attack variants | Legitimate paths |
| CWE-78 | Command Injection | 10 | 5 patterns | Safe arguments |
| CWE-89 | SQL Injection | 15 | 15 patterns | Legitimate values |
| CWE-79 | Cross-Site Scripting (XSS) | 12 | 10 patterns | Safe HTML/text |
| CWE-918 | Server-Side Request Forgery (SSRF) | 25 | 15 internal IPs | External URLs |
| CWE-601 | Open Redirect | 15 | 8 patterns | Same-origin paths |
| CWE-502 | Unsafe Deserialization | 6 | 4 payloads | Safe structures |
| CWE-916 | Weak Password Hash | 8 | - | Hash verification |

---

## How to Submit

### Step 1: Clone the Benchmark
```bash
git clone https://github.com/kyal102/dtl-security-benchmark.git
cd dtl-security-benchmark
```

### Step 2: Run Your System
Point `harness/audit_harness.py` at your security gate:

```python
from harness.audit_harness import run_audit

# Configure your gate
class YourGate:
    def classify(self, value, sink):
        # Return: {"verdict": "malicious" | "safe" | "abstain"}
        pass

# Run audit
results = run_audit(gate=YourGate())
print(f"Score: {results['score']}/166")
```

Or use the command line:
```bash
python harness/audit_harness.py --gate your_system --output results.json
```

### Step 3: Submit Results
**Two options:**

**Option A: GitHub PR**
```bash
# Add your results
cp results.json results/your_system_2026-06-29.json
git add results/
git commit -m "Submit: YourSystem v1.0 - 162/166 (97.6%)"
git push origin your-branch
# Create PR to main
```

**Option B: Submit via Form**
Fill out: `https://dtl-benchmark.jvi3.com/submit`

---

## Leaderboard

See [LEADERBOARD.md](LEADERBOARD.md) for live results.

Current leader:
- **DTL Security System v2026-06-29** — 162/166 (97.6%) | Audited: Antifragile.ai | [Report](docs/audit_results/SELF_AUDIT_REPORT_2026-06-29.md)

---

## Running Locally (60 seconds)

```bash
# Install
pip install -r requirements.txt

# Run audit
python harness/audit_harness.py

# Expected output:
# PASS: proof_1 (gate soundness)
# PASS: proof_2 (proof-carrying)
# PASS: proof_3 (sink router)
# PASS: proof_4 (production guards)
# Score: 162/166 (97.6%)
```

---

## What Makes This Different

### vs. Traditional Security Testing
- ❌ Traditional: "Run attacks against a live app, count what got through"
- ✅ DTL: "Test the gate in isolation; verify it's correct by design"

### vs. Security Linters
- ❌ Linters: "Warn about dangerous patterns in code"
- ✅ DTL: "Verify input validation works at runtime"

### vs. Fuzzing
- ❌ Fuzzing: "Generate random inputs, hope you find bugs"
- ✅ DTL: "Use a formal threat model; prove complete coverage over the model"

### vs. SWE-bench
- ❌ SWE-bench: "Can your model write correct code?"
- ✅ DTL: "Can your gate guarantee safe code executes, even if the model is weak?"

---

## Documentation

- **[Architecture](docs/DTL_ARCHITECTURAL_BOUNDARIES.md)** — Gate vs. Code Review division
- **[Methodology](docs/METHODOLOGY.md)** — How the benchmark was built
- **[Audit Results](docs/audit_results/SELF_AUDIT_REPORT_2026-06-29.md)** — First results from Antifragile.ai
- **[Proof System](modules/dtl_registry/audit_harness.py)** — The actual test harness

---

## FAQ

### Q: Can I game this benchmark?
**A**: Not without changing the fundamental behavior.

The benchmark tests:
- **Attack blocking** (try to exploit it — it should reject your payload)
- **Safe input handling** (try legitimate values — it should accept them)
- **Proof validation** (the gate must prove its answers are correct, not just assert them)

You can't pass without actually implementing a correct gate.

### Q: What if my system uses a different threat model?
**A**: Submit results with a caveat.

Mark your submission as:
```json
{
  "system": "MyGate v1.0",
  "score": 155/166,
  "caveats": [
    "Only tested on HTTP URLs (IPv6 not supported)",
    "Path traversal model assumes POSIX (not Windows)"
  ]
}
```

Transparency builds credibility more than perfection.

### Q: Can I submit a paper based on this?
**A**: Yes, encouraged.

Cite as:
```bibtex
@misc{dtl-security-benchmark,
  title={DTL Security Benchmark: Reproducible Verification of Security Gates},
  author={Kyal and Antifragile.ai},
  year={2026},
  howpublished={\url{https://github.com/kyal102/dtl-security-benchmark}}
}
```

---

## Scoring Philosophy

**This benchmark rewards correctness, not completeness.**

You don't need to handle every edge case. You need to:
1. **Be honest** about what you handle (mark caveats)
2. **Be precise** in what you test (run the harness exactly as documented)
3. **Be transparent** about failures (show your results, good or bad)

A system that scores 60/166 with clear documentation of why is more credible than one claiming 100/100 without evidence.

---

## Contributing

Found a bug in the benchmark? Have a new attack vector?

**[Open an issue](https://github.com/kyal102/dtl-security-benchmark/issues)** with:
- What you found
- Why it matters
- A proof-of-concept

The benchmark evolves with real-world threats.

---

## License

DTL Security Benchmark is open source under the MIT License.

The security gate implementations (guard functions) are under the same license as DTL.

Patent: AU 2026905289 (DTL software system)

---

## Contact

- **Benchmark Issues**: [GitHub Issues](https://github.com/kyal102/dtl-security-benchmark/issues)
- **Submissions**: [Submit Results](https://dtl-benchmark.jvi3.com/submit)
- **Questions**: kyal11105@gmail.com

---

**Made by**: Kyal | **Audited by**: Antifragile.ai | **Open source**: Yes | **Verifiable**: Always

**The first reproducible security gate benchmark. Not a black box. Not theoretical. Real gates, real attacks, real results.**
