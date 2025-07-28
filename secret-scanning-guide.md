
# 🔐 **Step-by-Step Setup: Local + CI/CD Protection**

Here’s a ready-to-use setup that **combines Gitleaks + git-secrets** for maximum protection against secrets in both **local development** and **CI/CD pipelines**.

## ⚙️ **1. Local Developer Setup (Pre-commit)**

### 📦 **Install `git-secrets`**

```bash
# On Mac
brew install git-secrets

# On Ubuntu/Debian
git clone https://github.com/awslabs/git-secrets.git
cd git-secrets
sudo make install
```

### 🔧 **Enable git-secrets hooks**

```bash
git secrets --install
git secrets --register-aws
git secrets --add 'password\s*=\s*.*'
git secrets --add 'secret(access|_key)?'
```

This prevents committing AWS keys and basic secret patterns.

> 🔁 You can extend this with your own regex patterns as needed.

---

## 🧪 **2. Project-wide Gitleaks Audit (One-time or Ad-hoc)**

### 📦 **Install Gitleaks**

```bash
# Using Homebrew
brew install gitleaks

# OR with Go
go install github.com/gitleaks/gitleaks/v8@latest
```

### 🚨 **Run Full Repo Scan**

```bash
gitleaks detect --source=. --report-path=gitleaks-report.json --verbose
```

You can also use the `--redact` flag to mask secrets in the output:

```bash
gitleaks detect --source=. --report-path=gitleaks.json --redact
```

---

## 🤖 **3. GitHub Actions (CI/CD) Setup**

### 🛠️ `.github/workflows/secret-scan.yml`

```yaml
name: Secret Scan

on:
  push:
    branches: [ main ]
  pull_request:

jobs:
  gitleaks-scan:
    name: Run Gitleaks Scan
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Code
        uses: actions/checkout@v3

      - name: Run Gitleaks
        uses: gitleaks/gitleaks-action@v2
        with:
          args: "--source=. --verbose --redact --report-path=gitleaks.json"

      - name: Upload Scan Report
        uses: actions/upload-artifact@v3
        with:
          name: gitleaks-report
          path: gitleaks.json
```

### ✅ **Add Status Gate (Optional)**

To block PRs with detected secrets:

* Parse `gitleaks.json`
* Fail build if `total findings > 0`

> Gitleaks returns a non-zero exit code automatically when leaks are found.

---

## 🧠 **Optional: Integrate with SonarQube**

* Convert `gitleaks.json` to [Generic Issue Format](https://docs.sonarsource.com/sonarqube/latest/analysis/generic-issue/)
* Upload to Sonar using `sonar.externalIssuesReportPaths`

---

## 📌 **Bonus: Automate for All Developers**

Add to your project’s README:

```bash
# Setup git-secrets on clone
git secrets --install
git secrets --register-aws
```

Or, ship it via a `setup.sh` script or a `pre-commit` config.

---

## ✅ Tools Summary

| Tool          | Use                         | When It Runs         |
| ------------- | --------------------------- | -------------------- |
| `git-secrets` | Blocks commits with secrets | Pre-commit (local)   |
| `gitleaks`    | Finds secrets in full repo  | CI/CD & manual scans |


---

## ✅ **Approaches to Prevent False Positives in Gitleaks**

In CI/CD, **Gitleaks can flag false positives** like dummy API keys or secrets in test files, which may be intentional and safe. Here's how to handle and **safely suppress** those false positives **without compromising security**.


---

### 🔹 **1. Use `.gitleaksignore` File**

Create a `.gitleaksignore` file in your repo root to **ignore specific files or paths**.

```toml
[[rules]]
description = "Ignore test fixtures with dummy keys"
path = "test/fixtures/"
```

You can also use glob patterns or regex:

```toml
[[rules]]
description = "Ignore mock credentials in utils"
regexTarget = "file"
regex = '.*mock.*\.js$'
```

> 💡 You can target by `path`, `commit`, `file`, `line`, or even rule ID.

---

### 🔹 **2. Redact/Flag Dummy Keys with a Comment**

Gitleaks skips secrets marked with specific comments (since v8+):

```js
const DUMMY_KEY = "sk-test-1234567890"; // gitleaks: allow
```

Use `// gitleaks: allow` to **safely whitelist this line only**.

---

### 🔹 **3. Modify Rules with Safe Allowlist Patterns**

In `.gitleaks.toml`, for each rule, add allowlist regexes to skip known dummy patterns:

```toml
[[rules]]
id = "aws-access-token"
description = "AWS Access Key"
regex = '''AKIA[0-9A-Z]{16}'''
tags = ["key", "AWS"]

[allowlist]
  regexes = ["AKIAFAKEKEY.*", "DUMMY_KEY_.*"]
```

This skips matching those **known dummy formats**.

---

### 🔹 **4. Ignore Specific Rules by ID (Selective Ignore)**

If a false positive comes from one rule and isn’t critical:

```toml
[[rules]]
id = "fake-secret"
description = "Fake rule to ignore"
enabled = false
```

Or in the scanner:

```bash
gitleaks detect --exclude-rule=aws-access-token
```

> ⚠️ Be cautious with disabling rules globally. Prefer file-level or line-level ignores.

---

### 🔹 **5. Use CI/CD Conditions to Allow Known Violations**

If you're using GitHub Actions:

```yaml
- name: Run Gitleaks
  id: gitleaks
  run: |
    gitleaks detect --redact --report-path=gitleaks.json || echo "leaks found"

- name: Check Gitleaks Findings
  run: |
    if grep -qv "test/" gitleaks.json; then
      echo "Critical secrets found outside test folders!" && exit 1
    fi
```

---

## 🧪 **Example: Ignore Dummy Keys in `test/mockData.js`**

### File: `test/mockData.js`

```js
export const mockApiKey = "sk-test-FAKETOKEN"; // gitleaks: allow
```

### `.gitleaksignore`

```toml
[[rules]]
description = "Ignore mock data test file"
path = "test/mockData.js"
```

---

## 🧠 Best Practices

| Task                  | Recommendation                                |
| --------------------- | --------------------------------------------- |
| 🔍 Dummy test secrets  | Use `// gitleaks: allow` comments             |
| 📁 Exclude entire dirs | Use `.gitleaksignore` or rule `path` filters  |
| 🔧 Modify rules safely | Add `allowlist.regexes` in `.gitleaks.toml`   |
| 🚫 Avoid full disable  | Don’t disable entire rules unless unavoidable |
| 🔐 Log findings        | Always keep logs for audit (even if ignored)  |

---

## ✅ TL;DR

| Technique                | Use Case                                    |
| ------------------------ | ------------------------------------------- |
| `.gitleaksignore`        | Ignore file paths or patterns               |
| `// gitleaks: allow`     | Suppress single-line dummy secrets          |
| Rule-level `allowlist`   | Whitelist known fake patterns               |
| `--exclude-rule`         | Temporarily skip specific checks            |
| Conditional checks in CI | Allow certain violations only in test paths |
