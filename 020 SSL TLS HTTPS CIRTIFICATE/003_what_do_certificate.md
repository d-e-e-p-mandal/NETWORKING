This is the missing piece. Most explanations jump from certificates to AES without explaining what the session key actually is.

What is a Session Key?

A session key is simply:

A temporary secret key
shared between Client and Server
for one connection/session.

Example:

Session Key
ABC123XYZ789

Both browser and server know it.

Nobody else should know it.

⸻

Why Do We Need It?

Public-key encryption (RSA/ECC) is slow.

Symmetric encryption (AES) is fast.

So TLS does:

Public Key Crypto
       ↓
Used to establish trust
Session Key
       ↓
Used to encrypt actual data

⸻

Complete Story

Step 1: Server Has Certificate

Server owns:

Server Public Key
Server Private Key
Certificate

⸻

Step 2: Browser Connects

Browser
   ↓
Hello
   ↓
Server

⸻

Step 3: Server Sends Certificate

Certificate
Public Key

Browser verifies:

CA Signature
Domain Name
Expiry

⸻

Now browser trusts:

This really is bank.com

⸻

Step 4: Create Session Key

Modern TLS 1.3 uses:

ECDHE

Both sides independently calculate:

Same Secret

without sending the secret itself.

Example result:

ABC123XYZ789

This becomes:

Session Key

⸻

Where Is Private Key Used?

Many people think:

Every packet
      ↓
Private Key

Wrong.

Private key is used mainly during:

TLS Handshake

to prove:

I am the real server.

⸻

Example:

Server receives handshake data.

Server signs part of it using:

Server Private Key

Browser verifies using:

Server Public Key

This proves:

This server owns the private key.

⸻

After Handshake

Private key is basically finished.

Certificate
Private Key
       ↓
Authentication Done

Then:

Session Key Takes Over

⸻

Actual Communication

Suppose login request.

Username
Password

Browser encrypts:

AES(Session Key)

Result:

G8H2K9L7...

Send to server.

⸻

Server decrypts:

AES(Session Key)

Gets:

Username
Password

⸻

Response

Server sends:

Account Balance

Encrypts:

AES(Session Key)

Client decrypts:

AES(Session Key)

Reads balance.

⸻

Visual Flow

Browser
    |
    |--- TLS Handshake ---|
    |
Server
Certificate
Public Key
Private Key
      ↓
Authentication
      ↓
Session Key Created
      ↓
AES Encryption Starts

⸻

How Long Does Session Last?

Depends.

Usually:

One TLS Connection

has one session key.

Example:

Open website
      ↓
TLS Handshake
      ↓
Session Key Created
      ↓
Use connection
      ↓
Connection Closed
      ↓
Session Key Destroyed

⸻

Could be:

Few Seconds
Few Minutes
Few Hours

depending on connection reuse.

⸻

New Connection

If browser reconnects:

New Handshake
      ↓
New Session Key

Example:

Old Session Key
ABC123
New Session Key
XYZ789

Different every time.

⸻

Why Change Session Keys?

Suppose hacker somehow gets:

Today's Session Key

Only today’s connection is affected.

Old sessions remain secure.

This is called:

Forward Secrecy

⸻

Simplified Memory Trick

Certificate
      ↓
Who are you?
Private Key
      ↓
Prove identity
TLS Handshake
      ↓
Create Session Key
Session Key
      ↓
Encrypt all communication
Connection Ends
      ↓
Session Key Destroyed

The most important thing to remember:

Private Key is NOT used to encrypt every request.
Private Key is mainly used during the TLS handshake to prove server identity.
The Session Key (AES key) is what encrypts almost all actual client-server traffic.