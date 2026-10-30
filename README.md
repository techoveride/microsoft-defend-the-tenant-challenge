# Defend the Tenant: SMB Microsoft 365 Hardening

My entry for Microsoft's **Defend the Tenant Challenge** (October 2026). I hardened a preconfigured Microsoft 365 lab tenant against a 10-control security baseline and verified the result with the challenge's assessment script.

**Result: 10 implemented, 0 not configured, 0 unknown** (final run, 2026-10-30 23:22 UTC). See [`hardening-results.txt`](hardening-results.txt).

## Controls implemented

| # | Control | How |
|---|---|---|
| 1 | MFA for administrative roles | `CA001-MFA-Admins`, which targets admin directory roles and requires MFA |
| 2 | MFA for all users | `CA002-MFA-All-Users`, covering all users and all resources |
| 3 | Block legacy authentication | `CA003-Block-Legacy-Auth`, which blocks Exchange ActiveSync and Other clients, with no exclusions |
| 4 | Sign-in risk protection | `CA004-SignIn-Risk`: medium/high risk, require MFA, sign-in frequency every time |
| 5 | User risk remediation | `CA005-User-Risk`: high risk, require MFA **and** password change, every time |
| 6 | Block user app consent | Enterprise apps → User consent: *Do not allow user consent* |
| 7 | Teams lobby | Global meeting policy: only *People who were invited* bypass the lobby |
| 8 | Teams presenters | Global meeting policy: *Only organizers and co-organizers* |
| 9 | Anonymous meeting join | Global meeting policy: anonymous users can join = **Off** |
| 10 | Self-service password reset | Password reset → Properties → **All** |

All five Conditional Access policies are **enabled**, not report-only.

## Design decisions

- **Conditional Access instead of Security Defaults.** Security Defaults can't do risk-based policies or targeted exclusions, so I disabled it and rebuilt the baseline with Conditional Access.
- **Break-glass account.** A dedicated emergency Global Admin is excluded from the MFA and risk policies so the tenant can't be locked out by a misconfigured policy.
- **No exclusions on the legacy-auth block.** My first assessment run returned UNKNOWN for control 3, because the script only accepts declared exclusions. A break-glass account that signs in with modern authentication is never affected by a legacy-protocol block, so I removed the exclusion from that policy. The rerun passed 10/10.

## Lessons learned

- Read the assessment logic, not just the portal. The checker required MFA **AND** exactly one remediation control for user risk, and both medium and high levels for sign-in risk.
- Configuration is not coverage. The script also reports that most lab accounts haven't registered for MFA or SSPR yet. In a real rollout, a registration campaign has to come before enforcement.

## Files

- `hardening-results.txt`: output of the challenge assessment script (`AA-AssessM365Status.ps1`)

The file contains lab tenant and policy IDs only. The lab tenant was temporary and has been deleted. No credentials are included.

## Author

Yakubu Buba, MSc Information Technology student, Carnegie Mellon University Africa
