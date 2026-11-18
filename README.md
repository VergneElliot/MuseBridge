# MuseBridge

Local print bridge for [MuseBar](https://mosehxl.com). Polls the cloud print queue over HTTPS and sends ESC/POS jobs to thermal printers on your LAN.

```text
MuseBar Cloud → bridge (this app) → printer(s) at 192.168.x.x:9100
```

## Requirements

- Node.js 18+
- Same Wi‑Fi/LAN as your printer(s)
- Outbound HTTPS to your MuseBar API
- Bridge key from **MuseBar → Settings → Printer** (MuseBar Bridge provider)

## Quick setup

1. Copy `.env.example` to `.env` and fill in values from MuseBar Settings.
2. Install and build:

```bash
npm install
npm run build
```

3. Start the bridge:

```bash
npm start
```

Expected output: `MuseBar Print Bridge started`

## Android phone (Termux) — interim setup

1. Install [Termux from F-Droid](https://f-droid.org/en/packages/com.termux/).
2. In Termux:

```bash
pkg update && pkg upgrade -y
pkg install -y nodejs-lts git
termux-wake-lock
git clone https://github.com/VergneElliot/MuseBridge.git
cd MuseBridge
cp .env.example .env
# edit .env with nano — paste values from MuseBar Settings → Printer
nano .env
npm install
npm run build
npm start
```

3. Android **Settings → Apps → Termux → Battery → Unrestricted**.
4. Keep the phone on charger and on the same Wi‑Fi as the printer.

## Raspberry Pi / Linux (recommended production)

```bash
git clone https://github.com/VergneElliot/MuseBridge.git
cd MuseBridge
cp .env.example .env
# edit .env
npm install
npm run build
npm start
```

For 24/7 operation, run under `systemd` (see MuseBar docs) or a process manager.

## Configuration

| Variable | Description |
|----------|-------------|
| `MUSEBAR_API_URL` | Cloud API base URL (e.g. `https://mosehxl.com`) |
| `ESTABLISHMENT_ID` | Establishment UUID |
| `BRIDGE_KEY` | Secret from printer settings |
| `PRINTER_HOST` | Default printer IP (receipts, pickup slips) |
| `PRINTER_PORT` | Usually `9100` |
| `PRINTERS_JSON` | Optional kitchen printer routing by slug |

## Cash drawer (Epson DK)

If the cash drawer kick cable is plugged into the printer **DK** port (not LAN),
MuseBar Cloud can enqueue `cash_drawer_open` jobs. This bridge sends them to
`PRINTER_HOST:9100` like a receipt — the Epson pulses DK and the drawer opens.

No extra `.env` keys. Keep the drawer on **DK**, bridge running, and enable
« Ouvrir le tiroir sur paiement espèces » in MuseBar → Paramètres → Imprimante.

Logs include `cashDrawerKick: true` when such a job is processed.

## Kitchen printers

If you use kitchen command tickets, set `PRINTERS_JSON` with slugs matching **Menu → Kitchen printers** in MuseBar.

## Development

```bash
npm test
npm run lint
npm run type-check
```

## Related

This package is extracted from the [MOSEHXL](https://github.com/VergneElliot/MOSEHXL) monorepo (`MuseBar/bridge`). Feature changes should eventually be synced between both locations.
