
# Real Certificate Generation Flow

### Step 1: Server Generates Key Pair
- Suppose Bank wants HTTPS.

**First, Bank’s server generates:**
- Public Key
- Private Key

**Example:**
- bank_private.key
- bank_public.key


Important:
```
Private Key NEVER leaves server.
Server
 ├── Private Key
 └── Public Key
```

### Step 2: Create CSR
- CSR = Certificate Signing Request

**Server creates:** CSR File

**Contains:**
- Domain Name
- Organization Name
- Country
- Public Key

**Example:** `bank.csr`

**Important:**
- CSR contains Public Key
- CSR does NOT contain Private Key

**Flow:**
```
Server
   ↓
Public Key
   ↓
Create CSR
```

### Step 3: Send CSR to CA

**Bank sends:** bank.csr to CA.

**Examples:**
* DigiCert
* GlobalSign
* Let’s Encrypt


**CA receives:**
- CSR

**containing:**
- bank.com
- Public Key
- Organization Details


### Step 4: CA Verifies Ownership

**CA checks:**
- Do you own bank.com?

**Methods:**
- DNS Verification
- Email Verification
- HTTP Challenge
- Business Documents


**After verification:**
- CA trusts bank.com belongs to Bank



### Step 5: CA Creates Certificate
- CA builds: Certificate

**Contains:**
- Domain Name
- Public Key
- Issuer
- Expiry Date
- Serial Number

**Example:** `bank.crt`


### Step 6: CA Signs Certificate
- Now the important part.
- CA computes hash.
```
Certificate Data
      ↓
Hash
```
**Example:**
- ABC123


**CA signs hash using:**
```
CA Private Key
Hash
 ↓
CA Private Key
 ↓
Digital Signature
```

**Certificate becomes:**
```
Certificate Data
+
CA Signature
```

### Step 7: CA Sends Certificate Back

**CA sends:**
- bank.crt to Bank.


**Server now stores:**
- bank_private.key
- bank.crt

**Server Structure:**
```
Server
├── bank_private.key
└── bank.crt
```

- Root CA and Intermediate CA
- Real systems don’t usually use Root CA directly.

**Structure:**
```
Root CA
   ↓
Intermediate CA
   ↓
Website Certificate
```

**Example:**
```
Root CA
   ↓
DigiCert Intermediate CA
   ↓
bank.com Certificate
```

**Why Intermediate CA?**
- Because Root CA is extremely important.
- If Root Private Key leaks: Internet Trust Broken

**Therefore:**
- Root CA stays offline and signs only: Intermediate CA Certificates

- *Intermediate CA signs:* Website Certificates


**Flow:**
```
Root CA Private Key
      ↓
Signs Intermediate CA
Intermediate CA Private Key
      ↓
Signs bank.com Certificate
```
⸻

**Browser Verification:**
- Browser-OS already contains:
- Trusted Root CA Certificates installed in OS/browser.


**When bank sends certificate:**
```
bank.com Certificate
+
Intermediate Certificate
```
Browser checks:
```
bank Certificate
   ↓
Signed by Intermediate?
Intermediate Certificate
   ↓
Signed by Root?
Root Trusted?
```

**If yes:**

- Trust Established


**Complete Real-World Flow**
```
Bank Server
     ↓
Generate Public/Private Key
     ↓
Create CSR
     ↓
Send CSR to CA
     ↓
CA Verifies Domain Ownership
     ↓
CA Creates Certificate
     ↓
CA Signs Certificate Using CA Private Key
     ↓
Certificate Returned
     ↓
Install Certificate on Server
     ↓
Browser Connects
     ↓
Server Sends Certificate
     ↓
Browser Verifies CA Signature
     ↓
Trust Established
     ↓
TLS Handshake Starts

Most Important Interview Point
```

- Private Key NEVER goes to CA.
- Only CSR (containing Public Key) goes to CA.
- CA signs the Public Key and domain information.
- The signed result is the SSL/TLS certificate.