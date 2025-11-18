# Configuring Windows Hello for Business, SSPR, & MFA

## Authentication Methods
Authentication methods are the different ways a user can prove their identity when signing in to Microsoft services (like Microsoft 365, Intune, Entra ID).

> They act as evidence that the person signing in is really the account owner.

**Authentication methods are used for:**
- Sign-in
- MFA (Multi-Factor Authentication)
- SSPR (Self-Service Password Reset)
- Passwordless authentication
- Verification during risky sign-ins

### Authentication Methods in Entra ID
1. **Microsoft Authenticator App** - Push notification, number matching, or passwordless sign-in.
2. **FIDO2 Security Keys** - Hardware keys (YubiKey, Feitian) for passwordless sign-in.
3. **Windows Hello for Business** - Biometric or PIN tied to TPM chip.
4. **Temporary Access Pass (TAP)** - Time-limited pass to set up passwordless methods.
5. **SMS (Text Message)** - One-time verification code sent to a mobile phone.
6. **Phone Call** - Automated call used to verify the user.
7. **Email OTP (For B2B Guests)** - One-time passcode sent to email for guest user login.
8. **Password** - Traditional username + password.
9. **Certificate-Based Authentication (CBA)** - User signs in using a certificate stored on the device or smart card.
10. **Passkeys** - Modern passwordless sign-in stored on device (Windows Hello, iOS, Android).

### Password Protection? 
Microsoft Entra ID Password Protection is a security feature that prevents users from setting weak, common, or easily guessable passwords.
> It protects against password spray, brute-force, and credential stuffing attacks.

#### How It Works
When a user changes or resets a password:
1. Password is checked against the global banned password list.
2. Then checked against the tenant custom banned password list.
3. If the password is weak → blocked.
4. Only strong passwords are accepted.
> No actual password ever leaves the device—Microsoft only receives a hashed evaluation of password “components”.

#### Configure Password Protection
#### Step 1 — Go to Password Protection Settings
1. Sign in to Entra Admin Center
2. Navigate to: Protection → Authentication methods → Password protection
#### Step 2 — Configure Cloud Password Protection
1. Enforce custom banned password list
   - Enable (Recommended)
   - This forces users to avoid words you add.
2. Custom banned password list
   - Add up to 1000 entries (examples):
     ```
     companyname
     productname
     bangladesh123
     admin
     welcome
     ```
3. Lockout settings (Default but configurable)
   - Lockout threshold (e.g., 10 failed attempts)
   - Lockout duration (e.g., 60 seconds)

### Step 3 — (Optional) Configure On-Prem Password Protection
This is needed only if you use on-prem Active Directory.

**You must deploy:**
1. Azure AD Password Protection Proxy Service
2. Domain Controller Agent 

---

## Windows Hello for Business
Windows Hello is Microsoft’s modern authentication system that replaces passwords with more secure and convenient methods such as biometrics (face, fingerprint) or PIN.

### Why Use Windows Hello?
- Stronger Security:
  - Eliminates traditional passwords: Passwords are easy to steal, reuse, guess, or leak. Windows Hello removes this weakness.
  - Uses biometric or PIN tied to the device: Your PIN/biometric never leaves the device. Even if someone steals your Microsoft account password, they cannot sign in without physical access to your device.
  - Uses hardware-based protection: Windows Hello uses TPM (Trusted Platform Module) to securely store authentication keys. This protects against:
- Faster & More Convenient
  - Face sign-in: instant login
  - Fingerprint: quick tap
  - PIN: faster than typing a long password: More speed, less friction, especially in enterprise environments.
- Resistant to Remote Attacks
  - A password can be stolen remotely.
  - A Windows Hello key cannot.
    > Even if hackers breach a database, they don’t get your biometric data or PIN because they are stored locally and cryptographically protected.
- Supports Multi-Factor Authentication (MFA)
  - Windows Hello =
    - Something you ARE (biometric) + something you HAVE (device)
    - Something you KNOW (PIN) + something you HAVE (device)
      > So MFA is built-in without extra steps.
- Enterprise Ready
  - Zero Trust authentication
  - Passwordless deployments
  - Reduced help desk password reset costs
  - Better user experience

<details><summary><h3>Configure Windows Hello in Intune</h></summary>

1. **Navigate to**: `Intune → Endpoint security → Account protection → Create Policy`
2. **Choose:**
   * **Platform:** Windows 10 and later
   * **Profile:** Identity Protection
3. **Configure settings:**
   * **Device Guard**
     * Credential Guard
       * Not configured
       * Disabled (Default)
       * Enabled with UEFI Lock ✔️
       * Enabled without UEFI Lock
   * **Windows Hello For Business**
     * Facial Features Use Enhanced Anti-Spoofing (true ✔️ / false / Not configured)
   * **Device-scoped settings**
     * Enable PIN Recovery
     * Expiration
     * PIN History
     * Lowercase Letters
     * Maximum PIN Length
     * Minimum PIN Length
     * Special Characters
     * Uppercase Letters
     * Require Security Device
     * Use Certificate for On-Prem Auth
     * Use Windows Hello for Business (Device)
   * **User-scoped settings**
     * Enable PIN Recovery (User)
     * Expiration (User)
     * PIN History (User)
     * Lowercase Letters (User)
     * Maximum PIN Length (User)
     * Minimum PIN Length (User)
     * Special Characters (User)
     * Uppercase Letters (User)
     * Require Security Device (User)
     * Use Windows Hello For Business (User)
4. **Assign the profile to users or devices**
* Specific **users** or **device groups**
* Entire organization, depending on deployment scope

> ### Note:
> - **Credential Guard**: Prevents attackers from using tools like Mimikatz to steal passwords from memory even if they get admin access to the device.
> - **Facial Features Use Enhanced Anti-Spoofing**: Stops someone from unlocking a device by holding up a photo or video of the user's face during Windows Hello sign-in.

</details>

---

## Self-Service Password Reset (SSPR)

### What is SSPR?
Self-Service Password Reset allows users to reset or unlock their own password without IT support by verifying identity through:
- Authentication methods (Phone, Email, Authenticator app, Security questions)

This reduces helpdesk calls and enables 24/7 support for users.

### How SSPR Works
- User attempts sign-in
- Clicks “Can’t access your account?”
- Completes authentication (MFA or verification methods)
- Resets password
- Password syncs to on-prem AD (if hybrid)

### On-Premises Integration
For hybrid environments:
- Install **Azure AD Connect**
- Enable **Password Writeback**
- This allows SSPR changes from the cloud to update the **on-prem Active Directory**

### Configure SSPR in Entra (Azure AD)
1. Navigate to: `Entra ID → Password Reset`
2. Choose who can use SSPR
   - All
   - Selected Users/Groups
3. Authentication Methods
   - Use the auth methods policy to manage other authentication methods.
4. On-premises integration
   - Enable password writeback (for hybrid AD)

## What Is Entra ID Password Protection  
