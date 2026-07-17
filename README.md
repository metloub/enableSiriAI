<p align="center">
    <img src="assets/aieu_logo.png" width="250">
</p>

<h1 align="center">Enable Apple Intelligence in the EU</h1>

<p align="center">
  A simple guide to unlock Apple Intelligence in the EU... until Apple finally does it themselves..
</p>
<p align="center">
  <img src="https://img.shields.io/badge/macOS-000000?logo=apple&logoColor=F0F0F0" alt="macOS" />
</p>

---

> [!NOTE]
> This method hasn't been fully tested yet. If it doesn't work, please open an issue with your device, iOS version, and what happened. If it *does* work... consider giving the repo a ⭐

---

If you are running a macOS version with stricter location checks, or if the standard method fails, developers bypass the restrictions by overriding the `eligibilityd` daemon.

This daemon calculates access based on the plist file inside: `/private/var/db/os_eligibility/eligibility.plist`.

### 1. Disabling System Integrity Protection (SIP)

To modify system files or inject code into system processes like `eligibilityd`, you must disable SIP:
- Boot the Mac into Recovery Mode (hold `Cmd + R` on Intel, or hold the Power button on Apple Silicon).
- Open the Terminal from the Utilities menu.
- Run: `csrutil disable` (or `csrutil enable --without nvram` depending on the specific script requirement).
- Reboot normally.

### 2. Overriding `eligibility.plist`

Once SIP is disabled, you can manipulate how features are evaluated:
- The Features: Apple Intelligence is internally code-named `GreyMatter` (tracked under Feature ID `95`), and the Xcode LLM feature is tracked under Feature ID `96`.
- Direct File Editing: Some users edit `/private/var/db/os_eligibility/eligibility.plist` to change the status of feature `95` and `96` to an eligible state (`status: 4`), and then prevent the OS from overwriting it by making the file immutable:
```bash
sudo chflags uchg /private/var/db/os_eligibility/eligibility.plist
```
- Process Injection (LLDB/Scripts): Tools on GitHub (such as [XcodeLLMEligible](https://github.com/Kyle-Ye/XcodeLLMEligible) or [enableAppleAI](https://github.com/kanshurichard/enableAppleAI)) use debugger injection (`lldb`) or local launch daemons to hook into the `eligibilityd` process memory when it starts up, forcing it to return "Eligible" for all location checks.

> [!WARNING]
> - Disabling SIP removes a core security layer of macOS, leaving the system more vulnerable to malware and privilege escalation.
> - Apple regularly patches `eligibilityd` and changes how these restrictions are validated. A minor macOS system update will likely break the technical override, requiring you to re-patch it.
> - Bypassing location daemons can sometimes cause Siri or Xcode predictive features to crash if they fail to establish a connection with Apple's generative AI servers.
