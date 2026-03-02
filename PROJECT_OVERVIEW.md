# E-Voting System - Project Overview

## Architecture
**Django-based web application** using **blockchain technology** for secure vote storage with **face recognition** and **OTP authentication**.

---

## Core Components

### 1. **Blockchain Module** (`Blockchain.py`)
- **Block Structure**: Contains index, transactions, timestamp, previous_hash, nonce
- **Genesis Block**: First block (index 0) with empty transactions
- **Proof-of-Work**: Difficulty 2 (hash must start with "00")
- **Mining**: Creates new blocks by solving PoW puzzle
- **Persistence**: Saves blockchain to `blockchain_contract.txt` using pickle

### 2. **Database** (MySQL)
- **`register`**: User credentials (username, password, contact, email, address)
- **`addparty`**: Candidate details (candidatename, partyname, areaname, image)
- **`otp`**: Temporary OTP storage for validation

### 3. **Face Recognition** (OpenCV)
- Uses **LBPH (Local Binary Patterns Histograms)** face recognizer
- Haar Cascade for face detection
- Stores face profiles in `EVotingApp/static/profiles/`

---

## User Flow

### **Registration Flow**
1. User fills registration form (username, password, contact, email, address)
2. System captures face image via webcam
3. Face is detected and saved as profile image
4. User data stored in `register` table

### **Voting Flow**
1. **Login**: User enters username/password → verified against `register` table
2. **Face Verification**: 
   - Webcam captures face
   - System compares with stored profile using LBPH
   - Confidence threshold: < 80
3. **Double-Vote Check**: Scans blockchain to verify user hasn't voted
4. **OTP Generation**: 
   - 4-digit random OTP generated
   - Sent to user's email via `sendmail.py`
   - Stored in `otp` table
5. **OTP Validation**: User enters OTP → verified against database
6. **Candidate Selection**: User views candidates and selects one
7. **Vote Recording**:
   - Transaction format: `username#candidatename#date`
   - Added to `unconfirmed_transactions`
   - **Immediately mined** into a new block
   - Block saved to blockchain file
8. **Confirmation**: User sees block details (previous hash, block number, current hash)

---

## Admin Flow

### **Admin Login**
- Credentials: `admin/admin`
- Access to admin dashboard

### **Admin Functions**
1. **Add Party**: Upload candidate details (name, party, area, image)
2. **View Parties**: Display all registered candidates
3. **View Votes**: Count votes per candidate by scanning blockchain

---

## Blockchain Operations

### **Transaction Structure**
```
Format: username#candidatename#date
Example: "john#BJP#2024-01-15"
```

### **Block Mining Process**
1. Transaction added to `unconfirmed_transactions` list
2. New block created with:
   - Index: last_block.index + 1
   - Transactions: all unconfirmed transactions
   - Timestamp: current time
   - Previous hash: hash of last block
3. **Proof-of-Work**: Find nonce where hash starts with "00"
4. Block added to chain
5. Unconfirmed transactions cleared
6. Blockchain saved to file

### **Vote Counting**
- Iterates through all blocks (except genesis)
- Extracts candidate name from transaction string
- Counts occurrences per candidate

---

## Security Features

1. **Face Recognition**: Prevents impersonation
2. **OTP Validation**: Email-based second factor
3. **Double-Vote Prevention**: Blockchain scan before allowing vote
4. **Blockchain Immutability**: SHA-256 hashing with previous block linking
5. **Proof-of-Work**: Computational security (difficulty 2)

---

## File Structure

```
EVoting with otp code/
├── Blockchain.py          # Core blockchain implementation
├── Block.py               # Block class definition
├── manage.py              # Django management
├── EVotingApp/
│   ├── views.py           # All business logic & routes
│   ├── templates/         # HTML templates
│   └── static/            # Images, CSS, profiles
├── blockchain_contract.txt # Persistent blockchain storage
└── haarcascade_frontalface_default.xml # Face detection model
```

---

## Key Functions

- **`checkUser(name)`**: Scans blockchain to check if user already voted
- **`ValidateUser()`**: Face recognition + double-vote check + OTP generation
- **`FinishVote()`**: Records vote transaction and mines block
- **`getCount(name)`**: Counts votes for a candidate
- **`generate_otp()`**: Creates 4-digit random OTP

---

## Technology Stack

- **Backend**: Django 5.2.8, Python
- **Database**: MySQL (PyMySQL)
- **Blockchain**: Custom implementation with SHA-256
- **Face Recognition**: OpenCV (cv2), LBPH recognizer
- **Email**: Custom sendmail module
- **Frontend**: HTML templates with webcam integration

---

## Current Limitations

- Votes are mined **immediately** (no batch processing)
- No Merkle Tree implementation
- No Presiding Officer role
- No NADRA database integration
- Single-server deployment (no distributed blockchain)
- Proof-of-Work instead of specified sealing mechanism

---

## How It Works (Quick Summary)

1. **User registers** → Face profile saved
2. **User logs in** → Face verified → OTP sent → OTP validated
3. **User votes** → Transaction created → Block mined immediately → Saved to blockchain
4. **Admin views** → Scans blockchain to count votes per candidate

**Blockchain ensures**: Votes cannot be altered (immutable chain), double-voting prevented, transparent vote counting.

