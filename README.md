# Robinhood Chain Multi-Wallet Bot

Bot otomatis untuk menjalankan beberapa action on-chain secara berbobot (weighted), dengan multi-wallet, retry, dan logging.

## Fitur

- Multi-wallet dari `wallets.json`
- Weighted action selection (persentase aksi bisa diatur)
- Random cooldown antar aksi
- Retry dengan backoff
- Helper transaksi (nonce sync, gas handling, error handling)
- Logging terstruktur di console + file log

## Action yang tersedia

- `simple-payment`
- `batch-payment`
- `domain-register`
- `send-gm`
- `deploy-contract`
- `mint-nft`

## Requirement

- Node.js 18+ (disarankan LTS)
- npm 9+
- RPC endpoint Robinhood Chain Testnet aktif

## Instalasi

```bash
npm install
npm run build
npm start
```

## Konfigurasi

### 1. File `.env`

Project ini membaca konfigurasi dari `.env`.
Gunakan `.env.example` sebagai acuan.

Variabel penting:

- Network: `RPC_ENDPOINT`, `RPC_WSS`, `CHAIN_ID`, `NETWORK_NAME`
- Contract: `DOMAIN_REGISTRY_ADDRESS`, `BATCH_PAYMENT_ADDRESS`, `GM_FACTORY_ADDRESS`
- Amount: `MIN_AMOUNT`, `MAX_AMOUNT`, `BATCH_MIN_TOKEN_AMOUNT`, `BATCH_MAX_TOKEN_AMOUNT`
- Delay/Retry: `MIN_DELAY`, `MAX_DELAY`, `MAX_RETRIES`, `RETRY_DELAY`, `RETRY_BACKOFF_MULTIPLIER`
- Weights: `WEIGHT_SIMPLE_PAYMENT`, `WEIGHT_BATCH_PAYMENT`, `WEIGHT_DOMAIN_REGISTER`, `WEIGHT_SEND_GM`, `WEIGHT_DEPLOY_CONTRACT`, `WEIGHT_MINT_NFT`

Contoh bobot (total 100):

```env
WEIGHT_SIMPLE_PAYMENT=20
WEIGHT_BATCH_PAYMENT=40
WEIGHT_DOMAIN_REGISTER=10
WEIGHT_SEND_GM=10
WEIGHT_DEPLOY_CONTRACT=10
WEIGHT_MINT_NFT=10
```

### 2. File `wallets.json`

Isi wallet di file `wallets.json`:

```json
[
  {
    "name": "Main Wallet",
    "privateKey": "0x..."
  },
  {
    "name": "Wallet 2",
    "privateKey": "0x..."
  }
]
```

## Menjalankan Bot

### Development

```bash
npm run dev
```

### Production

```bash
npm run build
npm run start
```

## Catatan Windows / WSL

Jika PowerShell memblok `npm`, gunakan:

```bash
npm.cmd run build
npm.cmd run start
```

Di WSL, kalau koneksi RPC bermasalah, cek endpoint:

```bash
curl -sS -X POST "$RPC_ENDPOINT" \
  -H 'content-type: application/json' \
  --data '{"jsonrpc":"2.0","id":1,"method":"eth_chainId","params":[]}'
```

## Logging

- Console: status realtime
- File log: `src/logs/combined.log`

Format waktu sudah ditampilkan dalam detik untuk delay utama.

## Struktur Project

```text
src/
  actions/
  bot/
  utils/
  client.ts
  config.ts
  executor.ts
  index.ts
  wallet-manager.ts
```

## Keamanan (Wajib Dibaca)

- Jangan commit private key ke GitHub.
- Pastikan `.env` dan `wallets.json` di-ignore oleh git.
- Jika private key sudah terlanjur terekspos, segera rotate/ganti wallet.

## Troubleshooting Singkat

- `No eligible wallets available`
: biasanya balance belum kebaca / RPC down / wallet cooldown.

- `JsonRpcProvider failed to detect network`
: endpoint RPC tidak reachable dari environment kamu.

- `intrinsic gas too low`
: sudah ditangani helper gas, tapi tetap cek RPC/node compatibility.

- Action revert
: beberapa kontrak memang punya rule/cooldown on-chain; cek explorer + trace tx.

copyright by #Mikasa_Enix
