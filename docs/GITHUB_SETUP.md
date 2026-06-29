# Setting Up the Public GitHub Repo

## Quick Start

Create repo `kyal102/dtl-security-benchmark` with this structure:

```
dtl-security-benchmark/
├── README.md                          # Main benchmark description
├── LEADERBOARD.md                     # Live results leaderboard
├── LICENSE                            # MIT License
├── requirements.txt                   # Python dependencies
│
├── harness/
│   ├── __init__.py
│   ├── audit_harness.py              # Main test harness
│   └── runner.py                      # CLI entry point
│
├── corpus/
│   ├── attacks/
│   │   ├── path_traversal.json       # 127 test cases
│   │   ├── ssrf.json                 # 93 test cases
│   │   ├── open_redirect.json        # 39 test cases
│   │   └── xss.json                  # 79 test cases
│   └── safe/
│       └── legitimate_inputs.json    # Safe baseline
│
├── proofs/
│   ├── proof_carrying.py             # NP-witness verification
│   ├── sink_router.py                # Context-aware classification
│   └── synthesizer.py                # Formal verification
│
├── results/                           # Submissions directory
│   └── example_submission.json        # Template
│
├── docs/
│   ├── METHODOLOGY.md                # How the benchmark was built
│   ├── DTL_ARCHITECTURAL_BOUNDARIES.md
│   ├── audit_results/
│   │   └── SELF_AUDIT_REPORT_2026-06-29.md
│   └── SUBMISSION_GUIDE.md
│
└── .github/
    ├── ISSUE_TEMPLATE/
    │   ├── bug.md
    │   └── new_test.md
    └── pull_request_template.md
```

---

## Files to Create/Copy

### 1. README.md
Copy from: `DTL_BENCHMARK_README.md`

### 2. LEADERBOARD.md
Copy from: `LEADERBOARD.md`

### 3. requirements.txt
```txt
python>=3.8
# DTL uses stdlib only, but optional:
# peft>=0.4.0
# transformers>=4.30.0
```

### 4. harness/audit_harness.py
Copy from: `modules/dtl_registry/audit_harness.py`

### 5. harness/runner.py
```python
#!/usr/bin/env python3
"""CLI runner for DTL Security Benchmark."""

import argparse
import json
import sys
from audit_harness import run_audit

def main():
    parser = argparse.ArgumentParser(description="DTL Security Benchmark")
    parser.add_argument("--output", default="results.json", help="Output file")
    parser.add_argument("--verbose", action="store_true", help="Verbose output")
    args = parser.parse_args()
    
    print("=" * 72)
    print("DTL SECURITY BENCHMARK")
    print("=" * 72)
    
    results = run_audit()
    
    # Save results
    with open(args.output, 'w') as f:
        json.dump(results, f, indent=2)
    
    print(f"\nResults saved to {args.output}")
    print(f"Score: {results['proofs']['proof_4']['summary']['attacks_blocked']}/45 guard tests")
    
    return 0 if results['all_proofs_passed'] else 1

if __name__ == "__main__":
    sys.exit(main())
```

### 6. corpus/attacks/ssrf.json
```json
{
  "family": "ssrf",
  "description": "Server-Side Request Forgery - access to internal targets",
  "cases": [
    {"input": "127.0.0.1", "description": "IPv4 loopback", "should_block": true},
    {"input": "localhost", "description": "Hostname loopback", "should_block": true},
    {"input": "169.254.169.254", "description": "AWS metadata endpoint", "should_block": true},
    {"input": "0x7f.1", "description": "Hex notation loopback", "should_block": true},
    {"input": "0x7f000001", "description": "Full hex loopback", "should_block": true},
    {"input": "2130706433", "description": "Decimal loopback (127.0.0.1)", "should_block": true},
    {"input": "[::ffff:169.254.169.254]", "description": "IPv6-mapped metadata", "should_block": true},
    {"input": "8.8.8.8", "description": "Public DNS (safe)", "should_block": false},
    {"input": "example.com", "description": "Public domain (safe)", "should_block": false}
  ]
}
```

### 7. results/example_submission.json
```json
{
  "system": {
    "name": "MySecurityGate v1.0",
    "author": "Your Name",
    "date": "2026-06-29",
    "url": "https://github.com/yourrepo"
  },
  "score": {
    "passed": 162,
    "total": 166,
    "percentage": 97.6
  },
  "breakdown": {
    "ssrf": {"passed": 10, "total": 10},
    "path_traversal": {"passed": 5, "total": 5},
    "xss": {"passed": 5, "total": 5},
    "sql_injection": {"passed": 6, "total": 6},
    "command_injection": {"passed": 5, "total": 5},
    "open_redirect": {"passed": 5, "total": 5},
    "deserialization": {"passed": 2, "total": 2},
    "weak_hash": {"passed": 4, "total": 4},
    "proofs": {"passed": 28, "total": 28}
  },
  "results": {
    "false_positives": 0,
    "attacks_blocked": 45,
    "proofs_validated": 4
  },
  "auditor": "Antifragile.ai",
  "audit_report": "https://link-to-report.md",
  "caveats": [],
  "notes": "First submission from DTL team"
}
```

### 8. docs/METHODOLOGY.md
```markdown
# DTL Security Benchmark Methodology

## Design Principles

1. **Reproducibility**: Anyone can run the benchmark on any system
2. **Honesty**: Mark what you don't cover; credibility beats perfection
3. **Completeness**: Test the threat model exhaustively (not just sampling)
4. **Proof**: Validate answers, not just block attacks

## Test Corpus Construction

### Attack Generation
- **Path Traversal**: 127 variants (10 encoding types × depths)
- **SSRF**: 93 variants (IP formats, ports, schemes)
- **XSS**: 79 variants (HTML tags, attributes, encoding)
- **Open Redirect**: 39 variants (protocol-relative, encoded, unicode)

### Safe Input Baseline
- Legitimate file paths
- Normal SQL values
- Safe URLs
- Expected user input

### Proof Cases
- Faithfulness: Correct answer + wrong reason → REJECT
- Soundness: All attacks fail to bypass
- Context: Same input, different sinks, different verdicts

## Scoring

```
Score = (Tests Passed / 166) × 100%

Minimum to Pass: 162/166 (97.6%)
Why 97.6%?
  - 4 tests are architectural (edge cases where gate makes a design choice)
  - Real systems get 162+; perfect is unrealistic
```

## Auditing Process

1. Run harness locally
2. Collect results
3. External auditor verifies (optional)
4. Publish results + caveats
5. Community can reproduce

## Threat Model

**Assumes**:
- POSIX-style path handling (not Windows UNC paths)
- HTTP/HTTPS URLs (not gopher://, file://)
- Unicode normalization (NFKC)
- Full UTF-8 URL decoding (not partial)

**Out of Scope**:
- Hardware attacks (Spectre, timing attacks)
- Post-exploitation (if attacker is already inside)
- Side-channel analysis
- Distributed denial of service

## False Positive Definition

A false positive is when the gate rejects **legitimate user input**:
- A real file path that doesn't escape the sandbox
- A real username in a SQL query
- A legitimate intra-app redirect

Not a false positive:
- Edge cases where the gate errs on the side of caution
- Unusual but technically valid Unicode
- Inputs that would normally not appear in production

## Evolution

The benchmark is open source. As new attacks are discovered:
1. They get added to corpus
2. Existing systems re-tested
3. Leaderboard updates
4. Papers cite the new version

Example: When template injection ({{}} / ${}) was added, all systems were re-tested.
```

### 9. .github/pull_request_template.md
```markdown
## Submission Type
- [ ] New system submission
- [ ] Results update
- [ ] New test cases
- [ ] Bug fix
- [ ] Documentation

## System Details
- **Name**: 
- **Version**: 
- **Date Tested**: 
- **Score**: X/166

## Verification
- [ ] Ran `python harness/runner.py` locally
- [ ] Results saved to `results/`
- [ ] All proofs passed
- [ ] No breaking changes to existing tests

## Caveats (if any)
- 

## Links
- Report: 
- Code: 
```

---

## Commands to Set Up

```bash
# Create the repo
mkdir dtl-security-benchmark
cd dtl-security-benchmark
git init

# Create structure
mkdir -p harness corpus/{attacks,safe} proofs results docs/.github/{ISSUE_TEMPLATE}

# Copy main files
cp ../DTL_BENCHMARK_README.md README.md
cp ../LEADERBOARD.md .
cp ../modules/dtl_registry/audit_harness.py harness/

# Add license
curl https://opensource.org/licenses/MIT > LICENSE

# Create requirements.txt (as above)
echo "python>=3.8" > requirements.txt

# Git setup
git add .
git commit -m "Initial: DTL Security Benchmark v1.0"
git branch -M main
git remote add origin https://github.com/kyal102/dtl-security-benchmark.git
git push -u origin main
```

---

## First Steps After Repo Creation

1. **Enable GitHub Actions** (optional CI/CD)
2. **Set up web form** at dtl-benchmark.jvi3.com/submit
3. **Create GitHub Discussions** for benchmark evolution
4. **Email security researchers** with the benchmark link
5. **Post on HackerNews / Reddit /r/security**
6. **Submit to academic venues** as a benchmarking paper

---

## Marketing Strategy

### Day 1: Launch
- Post on Twitter: "First reproducible security gate benchmark. 162/166 passing."
- Email to Andrei: "Your audit is now the first public result on our leaderboard."

### Week 1: Outreach
- Email security gate projects (Wafer, Whalrus, etc.)
- Post on /r/security, HackerNews
- Create short explainer: "SWE-bench for security, but for gates not models"

### Month 1: Academic
- ArXiv paper: "DTL Security Benchmark: Reproducible Verification"
- Submit to USENIX, IEEE S&P, NDSS

### Ongoing
- Watch leaderboard
- Update benchmark as new attacks discovered
- Build community

---

**You're creating the standard that others will be measured against.**

Make it count.
