<img width="1482" height="423" alt="image" src="https://github.com/user-attachments/assets/9ce76a6a-1bc1-45c4-9d4a-762af6bf2ba2" />
<img width="1489" height="421" alt="image" src="https://github.com/user-attachments/assets/3adf5716-c294-4990-b10e-99e9ba94af11" />
<img width="1491" height="453" alt="image" src="https://github.com/user-attachments/assets/ed8b92c8-b4ff-4dd6-8b53-517f20fd44fe" />

# Multi-Byte Path Hashes & Path Diagnostics Improvements

## Overview
With MeshCore v1.14.0 firmware, the network now supports multi-byte path hashes for improved routing and diagnostics. This update also introduces enhanced loop detection to prevent packet storms.

---

## Multi-Byte Path Hashes
- **Path Hash Sizes:**
  - 1-byte (legacy)
  - 2-byte
  - 3-byte
- **How It Works:**
  - Origin nodes select the path hash size for flood packets.
  - Repeaters on v1.14.0+ append their ID/hash using the specified size.
  - Older repeaters (pre-v1.14.0) drop packets with 2- or 3-byte hashes.
  - 1-byte hashes remain fully supported for backward compatibility.
- **Gradual Migration:**
  - Networks can mix hash sizes; full compatibility requires most repeaters to update.

---
## CLI Changes
- New command: `set path.hash.mode {0,1,2}`
  - 0 = 1-byte (legacy)
  - 1 = 2-byte
  - 2 = 3-byte
- Repeaters, room servers, and sensors use this preference for flood adverts and messages.

---

## Loop Detection Feature
- **Preference:** `loop.detect` (off, minimal, moderate, strict)
- **Purpose:** Prevents packet storms by rejecting packets with excessive duplicate IDs/hashes in the path.
- **Logic:** Allowed duplicates depend on path hash size and detection mode.

---

## Summary
Multi-byte path hashes improve routing accuracy and diagnostics, but require updated firmware for full network support. Loop detection further enhances network stability by preventing routing loops.

---

**Reference:**
- [Path Diagnostics Improvements](https://buymeacoffee.com/ripplebiz/path-diagnostics-improvements)


