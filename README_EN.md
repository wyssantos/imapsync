# Imapsync with OAuth2 Authentication on Office365

This guide covers email migration between servers using **imapsync**, with Office365 as the source and OAuth2 authentication.

## Configuration on Azure

### 1. Register an Application in Azure AD
Office 365 requires IMAP connections to use OAuth2. To achieve this, we need to register an application in Azure Active Directory.

1. Access the [Azure Portal](https://portal.azure.com).
2. Navigate to **Azure Active Directory**.
3. Click on **App Registrations** and select **New Registration**.
4. Fill in the following fields:
   - **Application Name:** `IMAPSync OAuth2`
   - **Account Types:** Choose **Accounts in any organizational directory and personal Microsoft accounts**.
5. Click **Register**.

### 2. Obtain Application Credentials
Now, retrieve the required credentials:

1. In the application menu, click **Certificates & Secrets**.
2. Under **Client Secrets**, click **New Client Secret**.
3. Set a name and choose a validity period (e.g., 1 year).
4. Copy the secret value (it will not be displayed again).
5. Also, copy the following values:
   - **Application (client) ID**
   - **Directory (tenant) ID**

### 3. Grant IMAP Permissions to the Application

1. Go to **API Permissions**.
2. Click **Add a Permission** → **Microsoft APIs** → **Microsoft Graph**.
3. Choose **Delegated Permissions** and add:
   - `IMAP.AccessAsUser.All` (IMAP Access)
   - `offline_access` (Keep the session active)
   - `email` (Email access)
4. Click **Add Permissions**.
5. Then, click **Grant Admin Consent**.

---

## Installing ImapSync

```bash
git clone https://github.com/imapsync/imapsync.git
cd imapsync
```

See the `INSTALL.d/INSTALL.ANY.txt` file for installing the required Perl modules.

---

## Generating OAuth2 Token for IMAP

### 1. Install and Configure

1. Download the `oauth2_imap.zip` file from:
   [https://imapsync.lamiral.info/oauth2/](https://imapsync.lamiral.info/oauth2/)
2. Extract the file.

### 2. Generate the Token

Run the following command, replacing `foo@example.com` with your email:

```bash
./oauth2_imap --provider office365 foo@example.com
```

Tokens are generated in the **tokens** subdirectory, which must exist beforehand.

---

## Test Login with Token

```bash
./imapsync --host1 outlook.office365.com --user1 foo@example.com \
  --oauthaccesstoken1 ./oauth2_imap/tokens/oauth2_tokens_foo@example.com.txt \
  --host2 mail.example.com --user2 foo@example.com --password2 'P@ssw0rd' \
  --automap --justlogin
```

---

## Execute Migration

```bash
./imapsync --host1 outlook.office365.com --user1 foo@example.com \
  --oauthaccesstoken1 ./oauth2_imap/tokens/oauth2_tokens_foo@example.com.txt \
  --host2 mail.example.com --user2 foo@example.com --password2 'P@ssw0rd' \
  --syncinternaldates --resyncflags --automap --subscribe --useheader 'Message-ID' --skipsize
```

---

## References
- **Imapsync:** [https://github.com/imapsync/imapsync](https://github.com/imapsync/imapsync)
- **OAuth2 IMAP:** [https://imapsync.lamiral.info/oauth2/oauth2_imap/README_oauth2.txt](https://imapsync.lamiral.info/oauth2/oauth2_imap/README_oauth2.txt)


