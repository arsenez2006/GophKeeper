## 🚨 Critical Bug Description

**Target branch Check:** 
- [ ] This PR targets **`main`** (or code will be backported to `dev` immediately after).

**Impact Level:**
- [ ] High (Service Outage) 
- [ ] Medium (Degraded Performance) 
- [ ] Low (Edge Case)

<!-- Describe the bug reproduction here. -->

---

## 🛠️ The Fix & Regression Risk

- [ ] **Minimal Scope:** The fix is isolated and contains *only* the code necessary to patch the bug (no hidden refactoring or styling changes).
- [ ] **Boundary Separation Verification:** Code inside `cli/` does **NOT** import anything from `backend/` and vice versa.

<!-- Descibe how the bug was fixed. -->

---

## 🧪 Emergency Verification

Describe how this fix was verified under pressure (since extensive pipeline testing might be bypassed or manual check is required).

- [ ] **Reproduction Test:** The original bug was successfully reproduced in a local/staging environment, and this patch explicitly fixes it.
- [ ] **Sanity Check:** Basic critical paths (e.g., storing a secret, retrieving a secret) still work flawlessly.

---

## 🔏 Security Check

* [ ] My commits in this PR are **signed** using my SSH key (verified badge will be checked upon review).
* [ ] No plaintext secrets, hardcoded API tokens, or unencrypted private keys are included in this code.