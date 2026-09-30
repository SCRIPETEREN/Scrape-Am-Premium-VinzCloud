# VinzCloud AM Premium Activation CLI

«A lightweight Node.js CLI for authorized testing and automation of email verification workflows, temporary mail handling, service statistics, and API status monitoring.»

<br />---

Overview

VinzCloud AM Premium Activation CLI is a lightweight command-line application written in JavaScript for Node.js.

It provides a simple interface for interacting with the configured VinzCloud API endpoints, including:

- Service statistics
- Service status
- Verification email requests
- Magic-link verification
- Temporary email generation
- Temporary inbox retrieval
- Individual message retrieval
- Automated verification workflow
- JSON API responses
- CLI-friendly error handling

The project is designed for development, testing, debugging, and authorized automation.

---

Tech Stack

Technology| Purpose
JavaScript| Main programming language
Node.js| Runtime environment
Fetch API| HTTP requests
REST API| Communication with the service
JSON| API request/response format
CLI| User interface

Requirements

- Node.js "18+"
- Internet connection
- Access to the configured service
- Authorization to perform the requested operations

Node.js version can be checked with:

node --version

---

Features

Service

- View service statistics
- Check service status
- Monitor API availability

Email Verification

- Request verification links
- Submit verification links
- Retrieve verification messages
- Automatically locate verification URLs

Temporary Mail

- Generate temporary email addresses
- Select a mailbox domain
- Read mailbox messages
- Retrieve individual messages

Automation

- Fully automated workflow
- Automatic mailbox polling
- Configurable domain input
- Automatic link extraction
- JSON result output

Developer Experience

- No external npm dependencies
- Simple single-file architecture
- Native Node.js "fetch"
- Structured API wrapper
- HTTP error handling
- CLI exit codes

---

Installation

Clone the repository:

git clone https://github.com/SCRIPETEREN/<repository-name>.git

Enter the project directory:

cd <repository-name>

Run the CLI:

node vinzAm.js

No "npm install" is required because the project uses the native "fetch()" implementation available in modern Node.js versions.

---

Project Structure

.
├── vinzAm.js
├── README.md
├── LICENSE
└── .gitignore

The main application is:

vinzAm.js

The file contains the complete CLI implementation.

---

Configuration

The service base URL is configured near the top of the application:

const BASE = 'https://vinzcloud.vercel.app';

HTTP headers are defined through:

const HEADERS = {
  'Content-Type': 'application/json',
  Accept: 'application/json',
  Origin: BASE,
  Referer: BASE + '/',
  // ...
};

If you are operating your own authorized instance, the base URL can be changed accordingly.

Example:

const BASE = 'https://your-domain.example';

---

CLI Usage

Running the application without a command displays the available commands:

node vinzAm.js

Available commands:

node vinzAm.js stats
node vinzAm.js status
node vinzAm.js send <email>
node vinzAm.js verify <email> <magicLink>
node vinzAm.js tempmail [domain]
node vinzAm.js inbox <email>
node vinzAm.js auto [domain]

---

Commands

"stats"

Returns service statistics.

node vinzAm.js stats

The API response is printed as formatted JSON.

---

"status"

Checks the current service/API status.

node vinzAm.js status

This is useful for checking connectivity before running other operations.

---

"send"

Requests a verification link for an email address.

node vinzAm.js send user@example.com

Syntax:

node vinzAm.js send <email>

The response is returned as JSON.

«Only use email addresses that you own or are authorized to test.»

---

"verify"

Submits an email address and verification link.

node vinzAm.js verify user@example.com "https://example.com/verification-link"

Syntax:

node vinzAm.js verify <email> <magicLink>

Quotes are recommended when the URL contains special characters.

---

"tempmail"

Generates a temporary mailbox using the default domain.

node vinzAm.js tempmail

Default domain:

catchmail.io

A custom domain can also be provided:

node vinzAm.js tempmail catchmail.io

Syntax:

node vinzAm.js tempmail [domain]

---

"inbox"

Retrieves messages from a temporary mailbox.

node vinzAm.js inbox example@catchmail.io

Syntax:

node vinzAm.js inbox <email>

The mailbox response is returned as JSON.

---

Automated Workflow

"auto"

The "auto" command combines multiple API operations into a single workflow.

node vinzAm.js auto

Or specify a domain:

node vinzAm.js auto catchmail.io

Workflow

┌──────────────────────┐
│ Generate Temp Email  │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ Request Verification │
│       Link           │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│    Poll Mailbox      │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ Retrieve Message     │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│ Extract Verification │
│        URL           │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│       Verify         │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│        Result        │
└──────────────────────┘

Polling Configuration

The current implementation uses:

Maximum attempts : 20
Interval         : 6 seconds
Maximum wait     : approximately 120 seconds

The process stops early when a valid verification URL is found.

---

API Reference

Command| HTTP Method| Endpoint
"stats"| "GET"| "/api/stats"
"status"| "GET"| "/api/status"
"send"| "POST"| "/api/send-link"
"verify"| "POST"| "/api/verify-link"
"tempmail"| "GET"| "/api/tempmail/generate"
"inbox"| "GET"| "/api/tempmail/inbox"
"message"| "GET"| "/api/tempmail/message"

---

Send Link Request

Endpoint:

POST /api/send-link

Request body:

{
  "email": "user@example.com"
}

---

Verify Link Request

Endpoint:

POST /api/verify-link

Request body:

{
  "email": "user@example.com",
  "magicLink": "https://example.com/..."
}

---

Generate Temporary Mail

Endpoint:

GET /api/tempmail/generate?domain=catchmail.io

The domain is URL-encoded before being sent to the API.

---

Retrieve Inbox

Endpoint:

GET /api/tempmail/inbox?email=user@example.com

---

Retrieve Message

Endpoint:

GET /api/tempmail/message?id=<messageId>&email=<email>

---

Architecture

The application is divided into several logical layers.

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
├── Automation Layer
│   ├── sleep()
│   └── fullAuto()
│
└── CLI Layer
    └── main()

---

HTTP Request Layer

All API requests are handled through the shared "req()" function.

Conceptually:

async function req(path, opts = {}) {
  const url = path.startsWith('http')
    ? path
    : `${BASE}${path}`;

  // Send request
  // Parse response
  // Validate HTTP status
  // Return JSON
}

This keeps the API functions small and consistent.

---

API Client

The API wrapper exposes individual methods:

const api = {
  stats: () => req('/api/stats'),

  status: () => req('/api/status'),

  send: (email) => {
    // ...
  },

  verify: (email, magicLink) => {
    // ...
  },

  tempmail: (domain) => {
    // ...
  },

  inbox: (email) => {
    // ...
  },

  message: (id, email) => {
    // ...
  },
};

This structure makes the endpoints easier to maintain and reuse.

---

Error Handling

The HTTP layer checks the response status.

For non-successful responses, an error is generated containing:

HTTP status
Error message
Original API response

Example:

[ERROR] HTTP 404

If additional API data is available, it is printed as JSON.

---

Exit Codes

The CLI uses standard process exit codes.

0 = Successful execution
1 = Error

This makes the application suitable for shell scripts and automation.

Example on Linux/macOS:

node vinzAm.js status
echo $?

Windows:

node vinzAm.js status
echo %ERRORLEVEL%

---

Example Output

Example CLI session:

$ node vinzAm.js auto catchmail.io

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

Actual API responses may differ depending on the service implementation.

---

Full Source Code

The complete implementation is contained in:

vinzAm.js

#!/usr/bin/env node

/**
 * VinzCloud AM Premium Activation CLI
 *
 * Runtime : Node.js 18+
 * Language : JavaScript
 * Interface : CLI
 *
 * Use only against services and accounts
 * that you own or are authorized to test.
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
  const url = path.startsWith('http')
    ? path
    : `${BASE}${path}`;

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
  return new Promise((resolve) =>
    setTimeout(resolve, ms)
  );
}

async function fullAuto(
  domain = 'catchmail.io'
) {
  console.log(
    '[*] Generate temp mail...'
  );

  const mail =
    await api.tempmail(domain);

  if (
    !mail.success ||
    !mail.email
  ) {
    throw new Error(
      'Failed to generate temporary mail'
    );
  }

  const email = mail.email;

  console.log(
    '[+] Email:',
    email
  );

  console.log(
    '[*] Sending verification link...'
  );

  const sendRes =
    await api.send(email);

  console.log(
    '[+] Send:',
    sendRes.message ||
      sendRes
  );

  console.log(
    '[*] Polling inbox (max 20x, interval 6s)...'
  );

  let magicLink = null;

  for (
    let i = 1;
    i <= 20;
    i++
  ) {
    await sleep(6000);

    process.stdout.write(
      `\r[+] Poll ${i}/20`
    );

    const inbox =
      await api.inbox(email);

    if (
      inbox?.messages?.length
    ) {
      for (
        const msg
        of inbox.messages
      ) {
        try {
          const detail =
            await api.message(
              msg.id,
              email
            );

          const body =
            JSON.stringify(
              detail
            );

          const links =
            body.match(
              /https?:\/\/[^\s"'<>\\]+/g
            );

          if (
            links &&
            links.length
          ) {
            magicLink =
              links[0];

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
      'Verification link not found'
    );
  }

  console.log(
    '[+] Magic Link:',
    magicLink
  );

  console.log(
    '[*] Verifying...'
  );

  const result =
    await api.verify(
      email,
      magicLink
    );

  return {
    email,
    magicLink,
    result,
  };
}

const [
  ,
  ,
  cmd,
  ...args
] = process.argv;

async function main() {
  try {
    switch (cmd) {
      case 'stats': {
        const result =
          await api.stats();

        console.log(
          JSON.stringify(
            result,
            null,
            2
          )
        );

        break;
      }

      case 'status': {
        const result =
          await api.status();

        console.log(
          JSON.stringify(
            result,
            null,
            2
          )
        );

        break;
      }

      case 'send': {
        const email =
          args[0];

        if (!email) {
          throw new Error(
            'Usage: node vinzAm.js send <email>'
          );
        }

        const result =
          await api.send(
            email
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

      case 'verify': {
        const [
          email,
          magicLink,
        ] = args;

        if (
          !email ||
          !magicLink
        ) {
          throw new Error(
            'Usage: node vinzAm.js verify <email> <magicLink>'
          );
        }

        const result =
          await api.verify(
            email,
            magicLink
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

      case 'tempmail': {
        const domain =
          args[0] ||
          'catchmail.io';

        const result =
          await api.tempmail(
            domain
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

      case 'inbox': {
        const email =
          args[0];

        if (!email) {
          throw new Error(
            'Usage: node vinzAm.js inbox <email>'
          );
        }

        const result =
          await api.inbox(
            email
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

      case 'auto': {
        const domain =
          args[0] ||
          'catchmail.io';

        const result =
          await fullAuto(
            domain
          );

        console.log(
          '\n========== RESULT =========='
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

Security

Do not commit sensitive information to GitHub.

Never place the following directly inside the repository:

API keys
Passwords
Authentication tokens
Session cookies
Private credentials
Private mailbox credentials

If credentials are required in future versions, use environment variables.

Example:

SERVICE_URL=https://your-domain.example

Then load the configuration from the environment instead of hardcoding secrets.

---

Magic Link Security

Verification links can contain sensitive authentication information.

Avoid:

- Sharing verification URLs publicly
- Posting verification URLs in GitHub Issues
- Including real verification URLs in screenshots
- Storing authentication links in source code
- Committing real mailbox data

Use test accounts and test mailboxes whenever possible.

---

Responsible Usage

This project should only be used against systems and accounts where you have explicit authorization.

Do not use it to:

- Access accounts belonging to other people
- Circumvent authentication controls
- Bypass service restrictions
- Spam email systems
- Abuse temporary-mail infrastructure
- Evade rate limits
- Automate unauthorized account activity
- Test third-party infrastructure without permission

The repository does not grant any additional authorization to access external systems.

---

Troubleshooting

"fetch is not defined"

Use a modern Node.js version.

node --version

Recommended:

Node.js 18+

---

HTTP 403

A "403 Forbidden" response may indicate that the server rejected the request.

Possible causes include:

- Invalid request headers
- Origin restrictions
- Referer restrictions
- Rate limiting
- Service-side access controls
- Endpoint changes

If you control the service, inspect the server logs and API configuration.

---

Verification Link Not Found

Possible causes:

- Email delivery delay
- Temporary mailbox delay
- API response format changed
- Verification email was not sent
- Mailbox endpoint unavailable
- Link extraction pattern no longer matches

The current automated workflow polls up to:

20 attempts
6 seconds per attempt

Maximum polling time:

approximately 120 seconds

---

Service Unavailable

Check:

node vinzAm.js status

If the status endpoint also fails, verify:

- Network connectivity
- Service deployment
- Domain configuration
- API availability
- Server logs

---

Development

Clone the repository:

git clone https://github.com/SCRIPETEREN/<repository-name>.git

Enter the project:

cd <repository-name>

Run:

node vinzAm.js

There is currently no build step or dependency installation required.

---

Future Improvements

Planned or possible improvements:

- [ ] Environment-based configuration
- [ ] Configurable base URL
- [ ] Configurable polling interval
- [ ] Configurable maximum attempts
- [ ] Request timeout
- [ ] Automatic retry handling
- [ ] Rate-limit handling
- [ ] Better email validation
- [ ] Structured logging
- [ ] Interactive CLI mode
- [ ] Configuration file support
- [ ] Unit tests
- [ ] Integration tests
- [ ] TypeScript version
- [ ] npm package
- [ ] Cross-platform installer
- [ ] API response schema validation

---

License

This project is licensed under the MIT License.

See the ""LICENSE"" (LICENSE) file for details.

---

Author

Scripeteren

GitHub:

SCRIPETEREN

---

Disclaimer

VinzCloud AM Premium Activation CLI is provided for development, testing, debugging, and authorized automation purposes.

You are responsible for ensuring that your use of this software complies with applicable laws, service policies, API terms, privacy requirements, and authorization requirements.

The author and contributors are not responsible for misuse of this software or unauthorized access to third-party systems.

---

<p align="center">
  <sub>Built with JavaScript and Node.js</sub>
</p>