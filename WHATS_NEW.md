# What's New? 🚀

Here is a detailed list of all improvements, fixes, and features added to the Hero Emulator project.

## 🛠️ Critical Bug Fixes

### 1. NPC 20374 (Five Element Manager) Fix
- **Issue:** Players experienced a "Client Lock" (game freeze) when an Advanced Fusion attempt failed.
- **Cause:** The server was not sending the player's updated gold amount in the failure packet, causing the client to wait indefinitely.
- **Fix:** Updated `database/characters.go` to append the correct gold data to the `FUSION_FAILED` packet.

### 2. Co-Production (Jangbo) Panic Fix
- **Issue:** The server would crash (Panic) if a player attempted to craft an item when:
    - The recipe ID did not exist in the database.
    - A required material was not in the player's inventory.
- **Fix:**
    - Modified `npc/jangbo.go` to check for recipe existence before processing.
    - Added a safety check for `FindItemInInventory` returning `-1`. If an item is missing, the craft is gracefully cancelled instead of crashing the server.

### 3. Inventory Index Out of Range
- **Issue:** Accessing `slots[-1]` caused immediate server termination.
- **Fix:** Added bounds checking in inventory logic to prevent this crash.

---

## ✨ New Features & Content

### 1. Divine Weapon Crafting (Dark Black Dragon Series)
- **Implemented:** Full crafting recipes for **12 weapons**:
    - Dark Black Dragon Blade, Bow, Charm, Spear, Rod, Axe, Claw, Dual Sword, Dual Rod, Pentachord, Fist Blade, Staff.
- **Method:** Created and executed SQL scripts to populate the `craft_items` table with correct Material IDs, Production IDs, and Costs.

### 2. Protocol Layer Refactoring
- **Goal:** Decouple the networking logic from the game database logic.
- **Changes:**
    - Created `hero-emulator/network` package.
    - Moved `Packet` logic to `network.Packet`.
    - Created `network.Connection` to handle TCP I/O in separate goroutines.
    - Abstracted `database.Socket` to use this new networking layer.

### 3. Log Management
- **Feature:** The server now **truncates** (clears) `Log.txt` on every startup.
- **Benefit:** Prevents the log file from growing infinitely and consuming all disk space/memory over time.

### 4. Automatic Account Registration
- **Feature:** Seamless Login/Register flow.
- **How it works:** If a user attempts to log in with a username that does not exist, the server **automatically creates** a new account with the provided password and logs them in immediately.If a user attempts to log in with a username that does not exist, the server **automatically creates** a new account with the provided password and logs them in immediately.
- **Benefit:** Removes the need for a separate registration website or manual database entry for testing/new players.
- **Limitation:**
    - **IP Limit:** Max 3 Accounts per IP.
    - **Time Throttle:** 30 Seconds cooldown between new registrations per IP.

### 5. Client Security (Strict Mode)
- **Feature:** Added `StrictMode` configuration for v216 clients.
- **Protection:** 
    - Validates packet integrity (Header: `0xAA 0x55`, Footer: `0x55 0xAA`, Length Check).
    - **New:** Automatically disconnects clients sending **Unknown Opcodes** (potential exploit attempts).
- **Action:** Directly disconnects the malicious connection to protect server stability.

---

## 🔧 GM Tools & Commands

### 1. New `/clear` Command
- **Usage:** Chat command `/clear`.
- **Effect:** Instantly deletes all items in the player's inventory (Slots 11-255).
- **Use Case:** Essential for GMs to quickly reset their inventory while testing item generation and crafting.

### 2. Clickable Chat IDs
- **Feature:** Item IDs and Mob IDs printed in the chat window are now clickable.
- **Benefit:** GMs can easily copy/paste IDs for commands like `/item` or `/mob`.

### 3. Admin Panel Improvements
- **URL:** `http://localhost:9999`
- **Dashboard:** Provides real-time server statistics (Online Users, Uptime).
- **Modules:**
    - **Upgrade Rate Editor:**
        - **Feature:** Modify success/failure probabilities for weapon/armor upgrades (+1 to +15).
        - **Function:** Updates `common/upgrade_rates.json` directly from the web interface.
        - **Drop Rate Config:** Adjust Global Drop Rate multipliers without restarting.
    - **User Management:**
        - **Search:** Find users by username.
        - **Edit:** Modify NCash, Bank Gold, and User Type (GM/Normal).
        - **Delete All:** One-click database wipe for testing purposes (with confirmation).
    - **Mob Editor:**
        - **Feature:** Search and edit Mob stats (HP, Level, XP etc.).
        - **Edit:** Modify Drop tables directly.
    - **Character Editor:**
        - **Feature:** Teleport characters, change maps, or edit coordinates.
    - **Cash Shop Editor:**
        - **Feature:** Add/Remove items from the in-game Cash Shop.
- **UI:** Dark Mode enabled by default for better visibility.

---

*This document serves as a historical record of the "Rescue Mission" to stabilize and enhance the Hero Emulator.*
