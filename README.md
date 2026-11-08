# @ikyerza/baileys

Library WebSocket berbasis TypeScript/JavaScript untuk berinteraksi dengan WhatsApp Web. Fork dari [Baileys](https://github.com/WhiskeySockets/Baileys) dengan penyesuaian oleh **ikyerza**.

[![npm version](https://img.shields.io/npm/v/@ikyerza/baileys?logo=npm)](https://www.npmjs.com/package/@ikyerza/baileys)
[![npm downloads](https://img.shields.io/npm/dm/@ikyerza/baileys)](https://www.npmjs.com/package/@ikyerza/baileys)
[![GitHub](https://img.shields.io/badge/GitHub-ikyerza%2Fbaileys-181717?logo=github)](https://github.com/ikyerza/baileys)

> ⚠️ **Disclaimer**
> Project ini tidak berafiliasi, disetujui, atau terhubung dengan WhatsApp maupun Meta. Gunakan dengan bijak, jangan untuk spam, stalkerware, atau aktivitas yang melanggar Ketentuan Layanan WhatsApp. Segala risiko (termasuk pemblokiran nomor) ditanggung pengguna.

---

## 📦 Instalasi

Ada dua cara memasang library ini: lewat **npm** (stabil) atau langsung dari **GitHub** (versi terbaru).

### 1. Dari npm

```bash
npm install @ikyerza/baileys
```

```bash
yarn add @ikyerza/baileys
```

```bash
pnpm add @ikyerza/baileys
```

### 2. Dari GitHub

Untuk mendapatkan commit terbaru (bisa belum stabil):

```bash
npm install github:ikyerza/baileys
```

```bash
yarn add github:ikyerza/baileys
```

```bash
pnpm add github:ikyerza/baileys
```

Atau tambahkan manual di `package.json`:

```json
{
  "dependencies": {
    "@ikyerza/baileys": "github:ikyerza/baileys"
  }
}
```

### Alias (opsional)

Jika project atau plugin kamu sudah memakai `@whiskeysockets/baileys` dan ingin mengganti tanpa mengubah import:

```bash
# dari npm
npm install @whiskeysockets/baileys@npm:@ikyerza/baileys

# dari GitHub
npm install @whiskeysockets/baileys@github:ikyerza/baileys
```

---

## 🚀 Contoh Penggunaan

```js
import makeWASocket, {
  useMultiFileAuthState,
  DisconnectReason
} from '@ikyerza/baileys'

async function start() {
  const { state, saveCreds } = await useMultiFileAuthState('auth_info')

  const sock = makeWASocket({ auth: state })

  sock.ev.on('creds.update', saveCreds)

  sock.ev.on('connection.update', ({ connection, lastDisconnect, qr }) => {
    if (qr) console.log('Scan QR ini di WhatsApp:', qr)

    if (connection === 'close') {
      const code = lastDisconnect?.error?.output?.statusCode
      if (code !== DisconnectReason.loggedOut) start() // reconnect
    } else if (connection === 'open') {
      console.log('✅ Terhubung ke WhatsApp')
    }
  })

  sock.ev.on('messages.upsert', async ({ messages }) => {
    const msg = messages[0]
    if (!msg.message || msg.key.fromMe) return

    await sock.sendMessage(msg.key.remoteJid, { text: 'Halo dari ikyerza/baileys! 👋' })
  })
}

start()
```

Untuk CommonJS:

```js
const { default: makeWASocket, useMultiFileAuthState } = require('@ikyerza/baileys')
```

---

## 🔑 Login dengan Pairing Code

```js
if (!sock.authState.creds.registered) {
  const code = await sock.requestPairingCode('628xxxxxxxxxx') // nomor tanpa + dan spasi
  console.log('Pairing code:', code)
}
```

---

## ✉️ Mengirim Pesan

```js
// Teks
await sock.sendMessage(jid, { text: 'Halo!' })

// Gambar
await sock.sendMessage(jid, {
  image: { url: './foto.jpg' },
  caption: 'Ini gambarnya'
})

// Video
await sock.sendMessage(jid, { video: { url: './video.mp4' }, caption: 'Video' })

// Audio / voice note
await sock.sendMessage(jid, {
  audio: { url: './audio.mp3' },
  mimetype: 'audio/mp4',
  ptt: true
})

// Dokumen
await sock.sendMessage(jid, {
  document: { url: './file.pdf' },
  mimetype: 'application/pdf',
  fileName: 'file.pdf'
})

// Balas (reply) pesan
await sock.sendMessage(jid, { text: 'Balasan' }, { quoted: msg })
```

---

## 📋 Persyaratan

- Node.js **18** atau lebih baru
- Akun WhatsApp aktif

---

## 🔗 Link

- GitHub: https://github.com/ikyerza/baileys
- npm: https://www.npmjs.com/package/@ikyerza/baileys
- Baileys asli: https://github.com/WhiskeySockets/Baileys

---

## 🤝 Kontribusi

Pull request dan issue sangat diterima. Silakan fork repo ini, buat branch baru, lalu ajukan PR.

## 🙏 Credits

- [WhiskeySockets/Baileys](https://github.com/WhiskeySockets/Baileys) — project asli
- **ikyerza** — maintainer fork ini

## 📄 Lisensi

[MIT](./LICENSE)
