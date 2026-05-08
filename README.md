# Groww Android Application – Unsafe WebView URL Handling & Weak Client-Side App Lock Enforcement

## Summary

During security testing of the \uE000entity\uE002["mobile_app","Groww Android App","com.nextbillion.groww"]\uE001, an internal WebView activity was observed to load arbitrary external URLs when invoked from a privileged ADB/debug environment.

The activity allowed rendering of externally controlled content inside the application WebView. JavaScript execution and outbound communication to external infrastructure were successfully demonstrated using a controlled testing environment with \uE000entity\uE002["software","Beeceptor","API mock server tool"]\uE001.

Additionally, weak enforcement of the client-side application lock allowed navigation into portions of the application interface without passcode re-validation after activity invocation.

---

# Affected Components

* Internal WebView Activity
* Client-side App Lock Validation

---

# Security Impact

Under a privileged debugging/ADB environment, the following behaviors were observed:

* Arbitrary external URLs rendered inside the application WebView
* JavaScript execution within trusted application context
* Outbound communication to external infrastructure
* Weak enforcement of local app-lock/passcode validation

Potential security implications include:

* UI redressing
* Phishing-style abuse
* User deception within trusted application context

No server-side authentication bypass or direct account compromise was identified during testing.

---

# Attack Requirements

* Physical or debugging access to the device
* ADB-enabled environment
* Existing authenticated user session

---

# Proof of Concept

## 1. Launch Internal WebView Activity

```bash
adb shell am start -n com.nextbillion.groww/.genesys.common.activities.WebActivity
```

### Observation

The application navigates into internal UI flows without triggering app-lock re-validation.

---

## 2. Arbitrary WebView URL Loading

```bash
adb shell am start -n com.nextbillion.groww/.genesys.common.activities.WebActivity --es url "https://example.com"
```

### Observation

Externally controlled content renders successfully inside the application WebView.

---

## 3. JavaScript Execution & External Communication

A controlled demonstration page was loaded inside the WebView.

The page executed JavaScript and transmitted non-sensitive user-provided input to an externally controlled endpoint using \uE000entity\uE002["software","Beeceptor","API mock server tool"]\uE001.

### Observed Request Header

```http
X-Requested-With: com.nextbillion.groww
```

This confirmed outbound communication originating from within the application WebView context.

---

# Root Cause

* Insufficient validation of externally supplied WebView URLs
* Weak client-side app-lock enforcement between activity transitions
* Internal activity behavior accessible under privileged debugging conditions

---

# Recommendations

* Restrict WebView URL loading to trusted allowlisted domains
* Enforce passcode validation before access to sensitive application flows
* Harden internal activity navigation validation
* Reduce exposure of debugging behaviors in production builds

---

# Severity Assessment

### Severity: Low

The issue is considered low severity under standard mobile threat models due to:

* Requirement for privileged/ADB access
* No externally triggerable attack path demonstrated
* No server-side authentication bypass identified
* No direct account compromise observed

---

# Disclosure Timeline

* Vulnerability reported to vendor
* Vendor reviewed and reproduced behavior
* Vendor classified the issue as requiring privileged/ADB access and outside the standard threat model

---

# Proof of Concept Video

Google Drive PoC:

https://drive.google.com/drive/folders/1r9t4AuG747PmRbgLmY2CztsX5PTjQL19?usp=sharing

---

# Credits

### Researcher

Soyam Arya","ethical hacker and security researcher"

### Alias

honest_corrupt

### Role

Ethical Hacker | Mobile Application Security Researcher | Penetration Tester

### CVE IDs

* CVE-2025-5154
* CVE-2025-6748
* CVE-2026-5682

### GitHub

\honest_corrupt GitHub\uE002https://github.com/honestcorrupt\

---

# Disclaimer

This research was conducted in a controlled testing environment for educational, defensive, and security research purposes only.

No real user credentials, financial information, or sensitive user data were targeted, accessed, or collected during testing.
