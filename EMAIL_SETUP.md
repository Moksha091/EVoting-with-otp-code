# Gmail Email Setup Instructions

## Problem
Gmail blocks SMTP login with regular passwords for security. You need to use an **App Password**.

## Solution: Create Gmail App Password

### Step 1: Enable 2-Step Verification
1. Go to [Google Account Security](https://myaccount.google.com/security)
2. Enable **2-Step Verification** (if not already enabled)
3. Follow the setup process

### Step 2: Generate App Password
1. Go to [Google App Passwords](https://myaccount.google.com/apppasswords)
   - Or: Google Account → Security → 2-Step Verification → App Passwords
2. Select app: **Mail**
3. Select device: **Other (Custom name)** → Enter "E-Voting System"
4. Click **Generate**
5. **Copy the 16-character password** (no spaces)

### Step 3: Update sendmail.py
Open `sendmail.py` and update:
```python
GMAIL_SENDER = "your_email@gmail.com"  # Your Gmail address
GMAIL_APP_PASSWORD = "xxxx xxxx xxxx xxxx"  # The 16-char App Password (remove spaces)
```

**Example:**
```python
GMAIL_SENDER = "myemail@gmail.com"
GMAIL_APP_PASSWORD = "abcd efgh ijkl mnop"  # Remove spaces: "abcdefghijklmnop"
```

---

## Alternative Solutions

### Option 1: Use Different Email Service

**Outlook/Hotmail:**
```python
server = smtplib.SMTP('smtp-mail.outlook.com', 587)
```

**Yahoo:**
```python
server = smtplib.SMTP('smtp.mail.yahoo.com', 587)
```

### Option 2: Use SMTP Service (SendGrid, Mailgun, etc.)
- Sign up for a service like SendGrid
- Get API key
- Update sendmail.py to use their SMTP

### Option 3: Test Without Email (Development)
For testing, you can:
1. Print OTP to console
2. Display OTP on screen
3. Skip email verification temporarily

---

## Quick Fix for Testing

If you just want to test the system without email:

1. **Option A: Print OTP to Console**
   - The OTP is already printed in console
   - Check your terminal/command prompt

2. **Option B: Display OTP on Screen**
   - Modify `ValidateUser` function to show OTP in response
   - Add OTP to context before redirecting

3. **Option C: Use Console OTP**
   - Check the Django server console output
   - The OTP is printed there: `print(otp)`

---

## Troubleshooting

### Error: "Please log in with your web browser"
- **Solution**: Use App Password, not regular password

### Error: "Username and Password not accepted"
- **Solution**: Make sure you're using App Password (16 characters, no spaces)

### Error: "Less secure app access"
- **Solution**: Gmail no longer supports "less secure apps". Use App Password instead.

### Still Not Working?
1. Verify 2-Step Verification is enabled
2. Generate a new App Password
3. Make sure there are no spaces in the App Password
4. Try a different email service for testing

---

## Security Note
⚠️ **Never commit your email password to Git!**
- Consider using environment variables
- Or use a separate config file (not in version control)

