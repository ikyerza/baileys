# @ikyerza/baileys

Library WhatsApp Web API berbasis WebSocket untuk Node.js. Paket ini adalah versi modifikasi (fork) dari [Baileys](https://github.com/WhiskeySockets/Baileys), dibuat untuk kebutuhan bot WhatsApp.

> **Catatan:** Proyek ini tidak berafiliasi dengan WhatsApp atau Meta. Gunakan dengan bijak dan patuhi Ketentuan Layanan WhatsApp. Penggunaan untuk spam atau aktivitas melanggar aturan bisa membuat nomor diblokir.

## Instalasi

```bash
npm install @ikyerza/baileys
```

Atau pasang langsung dari GitHub:

```bash
npm install github:ikyerza/baileys
```

Untuk memakai di bot yang sudah ada, ganti dependency `@whiskeysockets/baileys` di `package.json`:

```json
"dependencies": {
  "@whiskeysockets/baileys": "npm:@ikyerza/baileys"
}
```

Lalu jalankan `npm install` ulang.

## Persyaratan

- Node.js 18 atau lebih baru
- Akun WhatsApp aktif

## Contoh Penggunaan

### Koneksi dengan Pairing Code

```js
const {
  default: makeWASocket,
  useMultiFileAuthState,
  DisconnectReason
} = require('@ikyerza/baileys')

async function start() {
  const { state, saveCreds } = await useMultiFileAuthState('session')

  const sock = makeWASocket({
    auth: state,
    printQRInTerminal: false
  })

  // Minta pairing code jika belum login
  if (!sock.authState.creds.registered) {
    const nomor = '628xxxxxxxxxx' // nomor dengan kode negara, tanpa + atau spasi
    const code = await sock.requestPairingCode(nomor)
    console.log('Pairing code:', code)
  }

  sock.ev.on('creds.update', saveCreds)

  sock.ev.on('connection.update', ({ connection, lastDisconnect }) => {
    if (connection === 'close') {
      const code = lastDisconnect?.error?.output?.statusCode
      if (code !== DisconnectReason.loggedOut) start()
      else console.log('Sesi logout, hapus folder session lalu login ulang.')
    } else if (connection === 'open') {
      console.log('Terhubung!')
    }
  })

  return sock
}

start()
```

### Koneksi dengan QR Code

```js
const sock = makeWASocket({
  auth: state,
  printQRInTerminal: true
})
```

### Menerima dan Membalas Pesan

```js
sock.ev.on('messages.upsert', async ({ messages }) => {
  const m = messages[0]
  if (!m.message || m.key.fromMe) return

  const teks = m.message.conversation || m.message.extendedTextMessage?.text || ''

  if (teks === '.ping') {
    await sock.sendMessage(m.key.remoteJid, { text: 'Pong!' }, { quoted: m })
  }
})
```

### Mengirim Berbagai Jenis Pesan

```js
const jid = '628xxxxxxxxxx@s.whatsapp.net'

// Teks
await sock.sendMessage(jid, { text: 'Halo!' })

// Gambar
await sock.sendMessage(jid, {
  image: { url: './gambar.jpg' },
  caption: 'Ini gambar'
})

// Video
await sock.sendMessage(jid, {
  video: { url: './video.mp4' },
  caption: 'Ini video'
})

// Audio
await sock.sendMessage(jid, {
  audio: { url: './audio.mp3' },
  mimetype: 'audio/mp4'
})

// Dokumen
await sock.sendMessage(jid, {
  document: { url: './file.pdf' },
  mimetype: 'application/pdf',
  fileName: 'file.pdf'
})
```

### Grup

```js
// Info grup
const meta = await sock.groupMetadata('1234567890@g.us')

// Tambah / keluarkan anggota
await sock.groupParticipantsUpdate('1234567890@g.us', [jid], 'add')
await sock.groupParticipantsUpdate('1234567890@g.us', [jid], 'remove')

// Jadikan / cabut admin
await sock.groupParticipantsUpdate('1234567890@g.us', [jid], 'promote')
await sock.groupParticipantsUpdate('1234567890@g.us', [jid], 'demote')
```

## Struktur Folder

```
baileys/
├── lib/            # Source code library
├── package.json
└── README.md
```

## Kontribusi

Pull request dan laporan bug dipersilakan lewat [Issues](https://github.com/ikyerza/baileys/issues).

## Kredit

- [WhiskeySockets/Baileys](https://github.com/WhiskeySockets/Baileys) – library asli
- Seluruh kontributor Baileys

## Lisensi

MIT. Lihat file `LICENSE` untuk detailnya.
