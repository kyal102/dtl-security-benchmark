# DTL Security Benchmark Leaderboard

**Last Updated**: 2026-06-29 | **Tests**: 166 | **Passing Score**: 162+ (97%+)

---

## Current Rankings

| Rank | System | Date | Score | Attacks Blocked | False Positives | Auditor | Status |
|------|--------|------|-------|-----------------|-----------------|---------|--------|
| 🥇 | **DTL Security v2026-06-29** | 2026-06-29 | **162/166** (97.6%) | 45/45 ✓ | 0 ✓ | Antifragile.ai | [Full Report](docs/audit_results/SELF_AUDIT_REPORT_2026-06-29.md) |
| — | *[Open for submissions]* | — | — | — | — | — | Submit your results |

---

## Breakdown by Family

### DTL Security v2026-06-29

| Security Family | CWE | Tests | Passed | Status |
|---|---|---|---|---|
| SSRF Detection | CWE-918 | 10 | 10 ✓ | Hex variants, IPv6, decimal IPs |
| Path Traversal | CWE-22 | 5 | 5 ✓ | UTF-8 overlong, repeated dots |
| Open Redirect | CWE-601 | 5 | 5 ✓ | Protocol-relative, encoded paths |
| XSS (including template injection) | CWE-79 | 5 | 5 ✓ | HTML tags, template syntax ({{}}/${}) |
| Command Injection | CWE-78 | 5 | 5 ✓ | Shell metacharacters, argv safety |
| Unsafe Deserialization | CWE-502 | 2 | 2 ✓ | Pickle RCE prevention |
| SQL Injection | CWE-89 | 6 | 6 ✓ | Token-based detection, keywords |
| Weak Password Hash | CWE-916 | 4 | 4 ✓ | PBKDF2, constant-time verify |
| Proof-Carrying Validation | — | 7 | 7 ✓ | Faithfulness, bluff detection |
| Sink Router Context-Awareness | — | 13 | 13 ✓ | Same input, different sinks |
| Production Guard Selftest | — | 45 | 45 ✓ | Union-verified defenses |
| **TOTAL** | — | **166** | **162** | **97.6%** |

---

## Recent Submissions

### 2026-06-29: DTL Security v2026-06-29
- **Score**: 162/166 (97.6%)
- **Auditor**: Antifragile.ai (2 weeks of testing)
- **Highlights**:
  - 45/45 guard tests passing
  - 0 false positives
  - All 4 proofs validated
  - Template injection now caught
  - SSRF hex variants (0x7f.1) now caught
  - UTF-8 overlong encoding blocked
- **Report**: [Full Audit Report](docs/audit_results/SELF_AUDIT_REPORT_2026-06-29.md)
- **Caveats**: 
  - 'Fixed' gates model architectural assumptions (assumes code uses argv-mode, parameterized queries, etc.)
  - See [Architectural Boundaries](docs/DTL_ARCHITECTURAL_BOUNDARIES.md) for details

---

## How to Submit

### Option 1: GitHub PR
```bash
git clone https://github.com/kyal102/dtl-security-benchmark.git
cd dtl-security-benchmark

# Run the audit
python harness/audit_harness.py --output results/my_system_2026-06-29.json

# Create PR
git add results/
git commit -m "Submit: MySystem v1.0 - 155/166"
git push origin my-submission
# Create PR to main
```

### Option 2: Web Form
Submit at: https://dtl-benchmark.jvi3.com/submit

**Required**:
- System name & version
- Date tested
- Score (X/166)
- JSON results file
- Optional: Auditor name, paper/report link

---

## What's Being Tested

### Runtime Guard Tests (45 tests)
```
✓ Path traversal: 5 attacks blocked
✓ SSRF: 10 variants blocked (decimal IP, hex, IPv6, octal)
✓ Open redirect: 5 patterns blocked
✓ XSS: 5 payloads blocked (+ template injection)
✓ Command injection: 5 patterns blocked
✓ Deserialization: 2 RCE payloads blocked
✓ SQL injection: 6 patterns blocked
✓ Password hashing: 4 scenarios verified
```

### Proof System Tests (28 tests)
```
✓ Proof 1: Gate soundness (8 attacks, 0 bypasses)
✓ Proof 2: Proof-carrying faithfulness (7 tests)
✓ Proof 3: Sink router context-awareness (13 tests)
```

### Fuzzer Corpus (93 tests)
```
✓ Path traversal: 127 variants
✓ SSRF: 93 variants
✓ Open redirect: 39 variants
✓ XSS: 79 variants
```

---

## Scoring Rules

### ✓ Passing
- Attack must be blocked (verdict = "malicious")
- Safe input must pass (verdict = "safe")
- Proofs must validate (no hardcoded True)
- False positives must be 0

### ⚠️ Conditional Pass
- Mark architectural assumptions explicitly (e.g., "assumes POSIX, not Windows")
- Document which CWEs you don't cover
- Scoring adjusts, but credibility doesn't

### ❌ Not Passing
- Attack gets through (verdict = "safe" when it should be "malicious")
- False positive (verdict = "malicious" when it should be "safe")
- Proof doesn't validate (hardcoded results)

---

## Questions?

- **How do I run this locally?** See [README.md](DTL_BENCHMARK_README.md)
- **Why these specific tests?** See [METHODOLOGY.md](docs/METHODOLOGY.md)
- **What's the threat model?** See [Architectural Boundaries](docs/DTL_ARCHITECTURAL_BOUNDARIES.md)
- **Can I submit partial results?** Yes! Mark caveats clearly.
- **Is the benchmark open source?** Yes, MIT license. Fork and extend it.

---

## Citation

If you use this benchmark in a paper:

```bibtex
@misc{dtl-security-benchmark-2026,
  title={DTL Security Benchmark: Reproducible Verification of Security Gates},
  author={Kyal and Antifragile AI},
  year={2026},
  month={Jun},
  howpublished={\url{https://github.com/kyal102/dtl-security-benchmark}},
  note={Open source security gate verification benchmark}
}
```

---

**The first reproducible security gate benchmark.**

Not marketing, not hand-picked examples. Real tests, real results, verifiable by anyone.
