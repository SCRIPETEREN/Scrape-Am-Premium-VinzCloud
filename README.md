# VinzCloud AM Premium Activation CLI

CLI berbasis Node.js untuk berinteraksi dengan endpoint autentikasi, email verification, temporary mailbox, dan status layanan pada VinzCloud.

Tool ini dirancang untuk kebutuhan development, testing, debugging, dan automation terhadap layanan yang Anda miliki atau yang Anda mempunyai izin untuk uji.

«Important: Gunakan tool ini hanya pada layanan, akun, alamat email, dan endpoint yang Anda berwenang untuk akses. Jangan gunakan untuk mengakses akun orang lain, menghindari pembatasan layanan, atau melakukan aktivasi tanpa izin.»

---

Features

- Check service statistics
- Check API/service status
- Request magic-link email
- Verify magic link
- Generate temporary email address
- Check temporary-mail inbox
- Read individual temporary-mail messages
- Full automated verification flow
- Automatic inbox polling
- Automatic link extraction
- JSON API response output
- HTTP error handling
- CLI-based workflow
- No external npm dependency

---

Requirements

Pastikan environment Anda memiliki:

- Node.js "18+"
- Internet connection
- Access/permission terhadap service yang digunakan

Node.js versi terbaru juga dapat digunakan.

Check installation:

node --version

Contoh:

v22.x.x

---

Installation

Clone repository:

git clone https://github.com/SCRIPETEREN/<repository-name>.git

Masuk ke directory:

cd <repository-name>

Tidak diperlukan instalasi dependency karena script menggunakan API bawaan "fetch" pada Node.js modern.

Jalankan:

node vinzAm.js

Jika berhasil, CLI akan menampilkan daftar command yang tersedia.

---

Project Structure

.
├── vinzAm.js
├── README.md
├── LICENSE
└── .gitignore

Main File

vinzAm.js

Berisi:

- HTTP request wrapper
- API client
- Temporary mail handler
- Inbox polling
- Magic-link extraction
- Verification workflow
- CLI command handler
- Error handling

---

Configuration

Base service URL berada pada bagian:

const BASE = 'https://vinzcloud.vercel.app';

HTTP headers utama:

const HEADERS = {
  'Content-Type': 'application/json',
  Accept: 'application/json',
  Origin: BASE,
  Referer: BASE + '/',
  // ...
};

Jika Anda menjalankan instance service sendiri, ubah nilai "BASE" sesuai deployment Anda.

Contoh:

const BASE = 'https://your-domain.example';

«Jangan memasukkan API key, password, token, cookie, atau credential pribadi ke dalam repository.»

---

CLI Commands

1. Service Statistics

Menampilkan statistik service.

node vinzAm.js stats

Output diberikan dalam format JSON.

Contoh:

{
  "success": true
}

Struktur response bergantung pada API service.

---

2. Service Status

Memeriksa status service.

node vinzAm.js status

Command ini berguna untuk memastikan endpoint dapat diakses sebelum menjalankan workflow lainnya.

---

3. Send Verification Link

Mengirim verification/magic link ke alamat email yang diberikan.

node vinzAm.js send user@example.com

Contoh response:

{
  "success": true,
  "message": "Verification link sent"
}

Gunakan hanya dengan alamat email yang Anda miliki atau yang Anda berwenang gunakan untuk testing.

---

4. Verify Magic Link

Melakukan proses verification menggunakan email dan magic link.

node vinzAm.js verify user@example.com "https://example.com/verification-link"

Format:

node vinzAm.js verify <email> <magicLink>

Jika URL mengandung karakter khusus, gunakan tanda kutip:

node vinzAm.js verify user@example.com "https://example.com/..."

---

Temporary Mail

CLI menyediakan helper untuk temporary mailbox apabila endpoint service menyediakan fitur tersebut.

5. Generate Temporary Email

node vinzAm.js tempmail

Default domain:

catchmail.io

Atau tentukan domain:

node vinzAm.js tempmail catchmail.io

Format:

node vinzAm.js tempmail [domain]

Contoh response:

{
  "success": true,
  "email": "example@catchmail.io"
}

---

6. Check Inbox

Untuk mengambil pesan dari mailbox:

node vinzAm.js inbox example@catchmail.io

Format:

node vinzAm.js inbox <email>

Response ditampilkan sebagai JSON.

---

Automated Workflow

7. Full Auto

Command:

node vinzAm.js auto

Atau:

node vinzAm.js auto catchmail.io

Workflow secara umum:

Generate Temporary Email
          │
          ▼
     Send Verification
          │
          ▼
       Wait Inbox
          │
          ▼
      Poll Messages
          │
          ▼
     Extract Link
          │
          ▼
       Verify
          │
          ▼
        Result

Default polling:

Maximum attempts : 20
Interval          : 6 seconds
Maximum wait      : ~120 seconds

Command:

node vinzAm.js auto catchmail.io

Contoh proses:

[*] Generate temp mail...
[+] Email: example@catchmail.io

[*] Kirim magic link...
[+] Send: Verification link sent

[*] Polling inbox (max 20x, interval 6s)...
[+] Poll 1/20
[+] Poll 2/20
[+] Poll 3/20

[+] Magic Link: https://example.com/...

[*] Verifying...

========== HASIL ==========
{
  "email": "example@catchmail.io",
  "magicLink": "https://example.com/...",
  "result": {
    "success": true
  }
}

---

API Mapping

CLI menggunakan endpoint berikut:

Command| Method| Endpoint
"stats"| GET| "/api/stats"
"status"| GET| "/api/status"
"send"| POST| "/api/send-link"
"verify"| POST| "/api/verify-link"
"tempmail"| GET| "/api/tempmail/generate"
"inbox"| GET| "/api/tempmail/inbox"
message retrieval| GET| "/api/tempmail/message"

Send Request

{
  "email": "user@example.com"
}

Verify Request

{
  "email": "user@example.com",
  "magicLink": "https://example.com/..."
}

---

Architecture

Struktur internal sederhana:

vinzAm.js
│
├── Configuration
│   ├── BASE
│   └── HEADERS
│
├── HTTP Layer
│   └── req()
│
├── API Layer
│   ├── stats()
│   ├── status()
│   ├── send()
│   ├── verify()
│   ├── tempmail()
│   ├── inbox()
│   └── message()
│
├── Automation
│   ├── sleep()
│   └── fullAuto()
│
└── CLI
    └── main()

---

Error Handling

HTTP response yang bukan "2xx" akan menghasilkan error.

Contoh:

[ERROR] HTTP 404

Jika server mengembalikan JSON error, response juga akan ditampilkan:

{
  "error": "Not found"
}

Exit code:

0 = success
1 = error

Hal ini membuat CLI dapat digunakan pada script automation.

Contoh:

node vinzAm.js status
echo %ERRORLEVEL%

Pada Linux/macOS:

node vinzAm.js status
echo $?

---

Security

Do Not Commit Credentials

Jangan menyimpan data sensitif di repository:

API keys
Passwords
Session tokens
Cookies
Private credentials
Personal mailbox credentials

Gunakan environment variables apabila versi aplikasi Anda membutuhkan credential.

Contoh:

SERVICE_URL=https://your-domain.example

Kemudian:

const BASE = process.env.SERVICE_URL;

---

Magic Links Are Sensitive

Magic link dapat memberikan akses atau menyelesaikan proses autentikasi tertentu.

Jangan:

- membagikan magic link
- memasukkannya ke public issue
- menyimpan hasilnya di repository
- memasukkannya ke screenshot public
- mencetak credential sensitif ke log production

Untuk testing, gunakan akun dan mailbox yang memang Anda kontrol.

---

Responsible Usage

Tool ini dapat melakukan request otomatis dalam jumlah berulang. Karena itu, gunakan secara wajar dan patuhi:

- Terms of Service
- rate limits
- authorization requirements
- email provider policies
- privacy requirements
- hukum dan aturan yang berlaku

Jangan menggunakan tool untuk:

- mengambil alih akun
- mengakses akun tanpa izin
- menghindari authentication
- melakukan spam
- membuat aktivitas otomatis yang melanggar rate limit
- menguji sistem pihak lain tanpa authorization

---

Troubleshooting

"fetch is not defined"

Gunakan Node.js modern.

Check:

node --version

Disarankan:

Node.js 18+

---

HTTP 403

Kemungkinan server menolak request.

Periksa:

Origin
Referer
User-Agent
endpoint
service availability
authorization
rate limit

Jangan mencoba melewati access control pada service yang tidak Anda miliki atau tidak Anda berwenang uji.

---

Magic Link Tidak Ditemukan

Kemungkinan:

- email belum masuk
- inbox service mengalami delay
- response message berubah
- format link berubah
- endpoint temporary mail tidak tersedia
- verification email tidak berhasil dikirim

Workflow "auto" menggunakan polling dengan:

20 attempts × 6 seconds

Jika email belum tersedia setelah batas tersebut, proses akan gagal.

---

Service Tidak Bisa Diakses

Coba:

node vinzAm.js status

Jika status juga gagal, periksa deployment dan koneksi service terlebih dahulu.

---

Development

Clone repository:

git clone https://github.com/SCRIPETEREN/<repository-name>.git
cd <repository-name>

Jalankan:

node vinzAm.js

Tidak ada build process khusus.

---

Future Improvements

Beberapa pengembangan yang dapat ditambahkan:

- [ ] Environment-based configuration
- [ ] Config file support
- [ ] Better email validation
- [ ] Configurable polling interval
- [ ] Configurable polling attempts
- [ ] Structured logging
- [ ] Colored terminal output
- [ ] Interactive CLI
- [ ] Retry strategy
- [ ] Rate-limit handling
- [ ] Request timeout
- [ ] API response validation
- [ ] Unit tests
- [ ] Integration tests
- [ ] TypeScript version
- [ ] npm package
- [ ] Cross-platform CLI installer

---

License

Tentukan license sesuai kebutuhan project.

Contoh:

MIT License

Jika project menggunakan MIT License, tambahkan file:

LICENSE

dan cantumkan copyright holder yang sesuai.

---

Author

Scripeteren

GitHub:

"https://github.com/SCRIPETEREN"

---

Disclaimer

VinzCloud AM Premium Activation CLI disediakan untuk tujuan development, testing, automation, dan debugging pada service yang pengguna berwenang untuk akses.

Pengguna bertanggung jawab atas penggunaan tool, request yang dikirimkan, alamat email yang digunakan, serta kepatuhan terhadap kebijakan dan ketentuan layanan terkait.

Repository ini tidak memberikan hak akses tambahan terhadap service apa pun dan tidak dimaksudkan untuk mengakses akun atau sistem tanpa izin.

---

Full Source Code

Source code utama project tersedia pada:

vinzAm.js

Untuk menjaga README tetap mudah dibaca dan source tetap mudah dipelihara, gunakan file repository sebagai source of truth.

#!/usr/bin/env node

/**
 * Name : VinzCloud AM Premium Activation CLI
 * Owner : Scripeteren
 * Base Web : https://vinzcloud.vercel.app
 * Type : Scraper / CLI
 * Function : Send magic link, verify, tempmail, stats, full auto
 *
 * IMPORTANT:
 * Use only against services and accounts you own
 * or are explicitly authorized to test.
 */

const BASE = 'https://vinzcloud.vercel.app';

const HEADERS = {
  'Content-Type': 'application/json',
  Accept: 'application/json',
  Origin: BASE,
  Referer: BASE + '/',
  'User-Agent':
    'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36',
  'sec-ch-ua':
    '"Not_A Brand";v="8", "Chromium";v="120", "Google Chrome";v="120"',
  'sec-ch-ua-mobile': '?0',
  'sec-ch-ua-platform': '"Windows"',
};

async function req(path, opts = {}) {
  const url = path.startsWith('http') ? path : `${BASE}${path}`;

  const res = await fetch(url, {
    ...opts,
    headers: {
      ...HEADERS,
      ...(opts.headers || {}),
    },
  });

  const text = await res.text();

  let data;

  try {
    data = JSON.parse(text);
  } catch {
    data = {
      raw: text,
    };
  }

  if (!res.ok) {
    const err = new Error(
      data?.message ||
        data?.error ||
        `HTTP ${res.status}`
    );

    err.status = res.status;
    err.data = data;

    throw err;
  }

  return data;
}

const api = {
  stats: () => req('/api/stats'),

  status: () => req('/api/status'),

  send: (email) =>
    req('/api/send-link', {
      method: 'POST',
      body: JSON.stringify({
        email,
      }),
    }),

  verify: (email, magicLink) =>
    req('/api/verify-link', {
      method: 'POST',
      body: JSON.stringify({
        email,
        magicLink,
      }),
    }),

  tempmail: (domain = 'catchmail.io') =>
    req(
      `/api/tempmail/generate?domain=${encodeURIComponent(
        domain
      )}`
    ),

  inbox: (email) =>
    req(
      `/api/tempmail/inbox?email=${encodeURIComponent(
        email
      )}`
    ),

  message: (id, email) =>
    req(
      `/api/tempmail/message?id=${encodeURIComponent(
        id
      )}&email=${encodeURIComponent(email)}`
    ),
};

function sleep(ms) {
  return new Promise((resolve) => setTimeout(resolve, ms));
}

async function fullAuto(domain = 'catchmail.io') {
  console.log('[*] Generate temp mail...');

  const mail = await api.tempmail(domain);

  if (!mail.success || !mail.email) {
    throw new Error('Gagal generate temp mail');
  }

  const email = mail.email;

  console.log('[+] Email:', email);

  console.log('[*] Kirim magic link...');

  const sendRes = await api.send(email);

  console.log(
    '[+] Send:',
    sendRes.message || sendRes
  );

  console.log(
    '[*] Polling inbox (max 20x, interval 6s)...'
  );

  let magicLink = null;

  for (let i = 1; i <= 20; i++) {
    await sleep(6000);

    process.stdout.write(
      `\r[+] Poll ${i}/20`
    );

    const inbox = await api.inbox(email);

    if (inbox?.messages?.length) {
      for (const msg of inbox.messages) {
        try {
          const detail = await api.message(
            msg.id,
            email
          );

          const body = JSON.stringify(detail);

          const preferred = body.match(
            /https?:\/\/[^\s"'<>\\]+(?:alight-creative\.firebaseapp\.com|oobCode|__)/i
          );

          if (preferred) {
            magicLink = preferred[0];
            break;
          }

          const anyLink = body.match(
            /https?:\/\/[^\s"'<>\\]+/g
          );

          if (anyLink && anyLink.length) {
            magicLink = anyLink[0];
            break;
          }
        } catch {
          // Ignore individual message errors
        }
      }

      if (magicLink) {
        break;
      }
    }
  }

  console.log('');

  if (!magicLink) {
    throw new Error(
      'Magic link tidak ditemukan'
    );
  }

  console.log(
    '[+] Magic Link:',
    magicLink
  );

  console.log('[*] Verifying...');

  const result = await api.verify(
    email,
    magicLink
  );

  return {
    email,
    magicLink,
    result,
  };
}

// ─────────────────────────────────────────────
// CLI
// ─────────────────────────────────────────────

const [, , cmd, ...args] =
  process.argv;

async function main() {
  try {
    switch (cmd) {
      case 'stats': {
        const result = await api.stats();

        console.log(
          JSON.stringify(result, null, 2)
        );

        break;
      }

      case 'status': {
        const result = await api.status();

        console.log(
          JSON.stringify(result, null, 2)
        );

        break;
      }

      case 'send': {
        const email = args[0];

        if (!email) {
          throw new Error(
            'Usage: node vinzAm.js send <email>'
          );
        }

        const result = await api.send(email);

        console.log(
          JSON.stringify(result, null, 2)
        );

        break;
      }

      case 'verify': {
        const [email, magicLink] = args;

        if (!email || !magicLink) {
          throw new Error(
            'Usage: node vinzAm.js verify <email> <magicLink>'
          );
        }

        const result = await api.verify(
          email,
          magicLink
        );

        console.log(
          JSON.stringify(result, null, 2)
        );

        break;
      }

      case 'tempmail': {
        const domain =
          args[0] || 'catchmail.io';

        const result =
          await api.tempmail(domain);

        console.log(
          JSON.stringify(result, null, 2)
        );

        break;
      }

      case 'inbox': {
        const email = args[0];

        if (!email) {
          throw new Error(
            'Usage: node vinzAm.js inbox <email>'
          );
        }

        const result =
          await api.inbox(email);

        console.log(
          JSON.stringify(result, null, 2)
        );

        break;
      }

      case 'auto': {
        const domain =
          args[0] || 'catchmail.io';

        const result =
          await fullAuto(domain);

        console.log(
          '\n========== HASIL =========='
        );

        console.log(
          JSON.stringify(
            result,
            null,
            2
          )
        );

        break;
      }

      default:
        console.log(`
VinzCloud CLI

Usage:
  node vinzAm.js stats
  node vinzAm.js status
  node vinzAm.js send <email>
  node vinzAm.js verify <email> <magicLink>
  node vinzAm.js tempmail [domain]
  node vinzAm.js inbox <email>
  node vinzAm.js auto [domain]

Examples:
  node vinzAm.js stats
  node vinzAm.js status
  node vinzAm.js send user@example.com
  node vinzAm.js verify user@example.com "https://..."
  node vinzAm.js tempmail catchmail.io
  node vinzAm.js inbox user@catchmail.io
  node vinzAm.js auto catchmail.io
`);

        process.exit(
          cmd ? 1 : 0
        );
    }
  } catch (err) {
    console.error(
      '[ERROR]',
      err.message
    );

    if (err.data) {
      console.error(
        JSON.stringify(
          err.data,
          null,
          2
        )
      );
    }

    process.exit(1);
  }
}

main();

---

Source Code Notes

The CLI is organized into four primary layers:

┌──────────────────────────────┐
│            CLI               │
│ stats / status / send / etc. │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│          API Layer           │
│ stats / send / verify / mail │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│        HTTP Request          │
│       req() + headers       │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│       VinzCloud API          │
└──────────────────────────────┘

The "auto" command combines the individual API functions into one automated testing workflow:

tempmail()
    ↓
send()
    ↓
inbox()
    ↓
message()
    ↓
extract verification URL
    ↓
verify()
    ↓
result

For production or authorized testing environments, consider adding configurable timeouts, rate limiting, structured logging, response validation, and environment-based configuration.