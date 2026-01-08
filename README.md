# Lab Guide: Tesla Fleet API + Home Assistant


## 1. Summary

This document outlines the procedure for integrating Tesla vehicles with Home Assistant using the 2026 Tesla Fleet API standards. This method utilizes **End-to-End Encryption** via a Virtual Key pair (NIST P-256) and public key hosting via GitHub Pages.

## 2. Phase I: Cryptographic Key Generation

Tesla requires a specific Elliptic Curve key (prime256v1). Generate these on a local machine (Windows or Mac).

**Command (Terminal/PowerShell):**

```bash
ssh-keygen -t ecdsa -b 256 -m PEM -f tesla_key
```

- **Private Key:** The file `tesla_key` (Move this to your HA `/config/` folder and rename to `tesla_fleet.key`).
- **Public Key:** Run `ssh-keygen -e -m PEM -f tesla_key` and copy the output.

## 3. Phase II: Public Key Infrastructure (GitHub)

Tesla's servers must verify your identity by "crawling" a public domain to find your public key.

1. **Repository:** Create a public repo named `yourusername.github.io`.
2. **Pathing:** Create folders: `/.well-known/appspecific/`.
3. **File:** Create `com.tesla.3p.public-key.pem` inside that folder and paste your **Public Key** text.
4. **Bypass Jekyll:** Create a blank file named `.nojekyll` in the root folder.
5. **Verify:** Confirm the link is live: `https://yourusername.github.io/.well-known/appspecific/com.tesla.3p.public-key.pem`

## 4. Phase III: Tesla Developer Portal

Register your application at [developer.tesla.com](https://developer.tesla.com).

| Field                  | Configuration Value                  |
|-----------------------|--------------------------------------|
| **Allowed Origin URL** | `https://yourusername.github.io`     |
| **Allowed Redirect URI** | `https://my.home-assistant.io/redirect/oauth` |
| **Scopes**            | `vehicle_device_data, vehicle_cmds`  |

**Note:** Save your **Client ID** and **Client Secret** immediately.

## 5. Phase IV: Home Assistant Integration

1. Ensure `tesla_fleet.key` is in the `/config/` or `/homeassistant` directory.
2. Restart Home Assistant.
3. Navigate to **Settings > Devices & Services > Add Integration**.
4. Search for **Tesla Fleet**.
5. Enter Credentials:
   - **Client ID / Secret:** From the Tesla Portal.
   - **Domain:** `yourusername.github.io`

## 6. Phase V: Vehicle Handshake (Phone)

1. Home Assistant will generate a pairing link.
2. Open this link on a mobile device with the Tesla App signed in.
3. Approve the **Virtual Key** on the vehicle screen or via the app (may require a physical key card tap in the car).

## 7. Operational Security (OpSec)

To prevent accidental triggers (e.g., opening the trunk from your pocket):

- **Disable Entities:** In HA, go to Entity Settings for Trunk/Frunk and select "Disabled."
