# 🔢 Versioning Policy

This guide follows semantic versioning adapted for documentation.

Format:

MAJOR.MINOR.PATCH

Example:
v1.4.0

---

## 🟥 MAJOR Version (X.0.0)

Incremented when:

- Structural reorganization occurs
- Core operational philosophy changes
- Major Arch policy shifts require rewrite
- Threat model assumptions change

Example:
- Redefining the guide’s scope
- Reworking the snapshot model entirely

---

## 🟨 MINOR Version (0.X.0)

Incremented when:

- New major section added
- Driver compatibility matrix updated
- Safe update workflow expanded
- New Arch transition incorporated
- Significant clarification added

Example:
- NVIDIA 590 driver transition integration
- Adding BTRFS appendix
- Adding system profiles

---

## 🟩 PATCH Version (0.0.X)

Incremented when:

- Typos corrected
- Broken links fixed
- Minor command adjustments
- Small clarification edits
- Formatting fixes

Example:
- Fixing incorrect package name
- Updating outdated URL

---

## 📅 “Last Reviewed” Date

The "Last Reviewed Against Arch News" date reflects:

- The most recent Arch News audit
- Confirmation that no major upstream transitions are pending

This date may change without a version bump if no content modification is required.

---

## 🧭 Versioning Philosophy

Versioning communicates:

- Maintenance discipline
- Change transparency
- Reader trust

Frequent patch bumps are normal.
Minor bumps follow upstream Arch transitions.
Major bumps are rare.
