Set up the NetSuite SDF machine-to-machine (M2M) authentication environment.

## Step 0 — Check SuiteCloud CLI installation

Run `suitecloud --version` to check if the CLI is already installed.

- If it is installed, print the version and continue to Step 1.
- If it is not installed, check that `npm` is available by running `npm --version`.
  - If npm is not available, stop and tell the user: "npm is required to install SuiteCloud CLI. Please install Node.js from https://nodejs.org and try again."
  - If npm is available, install the CLI by running:

```
npm install -g @oracle/suitecloud-cli
```

After installation, confirm the version with `suitecloud --version` and continue.

## Step 1 — Set CI environment variables in ~/.zprofile

Read the file `~/.zprofile`. Check whether `SUITECLOUD_CI` and `SUITECLOUD_CI_PASSKEY` are already exported there.

For each variable that is **not** present:

- **SUITECLOUD_CI**: Add `export SUITECLOUD_CI=1`
- **SUITECLOUD_CI_PASSKEY**: Generate a cryptographically random alphanumeric string of 64 characters (using characters A-Z, a-z, 0-9), then add `export SUITECLOUD_CI_PASSKEY=<generated_value>`

If a variable is already present, skip it without modifying anything.

Append the missing exports to the end of `~/.zprofile`. Group them under a comment:

```
# NetSuite SDF M2M
export SUITECLOUD_CI=1
export SUITECLOUD_CI_PASSKEY=<generated_value>
```

After writing, tell the user which variables were added and which were already present.
Then tell the user to run `source ~/.zprofile` to apply the changes in the current shell session.

## Step 2 — Generate the M2M certificate

This step is per-project and must be run from the project root directory.

1. Create a folder named `sdf-credentials` in the current directory if it doesn't already exist.

2. Add `sdf-credentials/` to the project's `.gitignore`. If `.gitignore` doesn't exist, create it. If the entry is already present, skip it.

3. Tell the user that openssl will now ask for certificate details (Country, State, Organization, etc.) and they should fill them in interactively. Then run:

```
openssl req -new -x509 -newkey rsa:4096 -keyout sdf-credentials/private.pem -sigopt rsa_padding_mode:pss -sha256 -sigopt rsa_pss_saltlen:64 -out sdf-credentials/public.pem -nodes -days 730
```

4. After the command completes, confirm to the user that `private.pem` and `public.pem` were generated inside `sdf-credentials/`, and remind them that this folder is git-ignored and should never be committed.

## Step 3 — Upload the public key into your NetSuite account

This step is manual. Display the following instructions to the user:

---

**Upload the public key into your NetSuite account**

1. Go to **Setup > Integration > OAuth Client Credentials (M2M) Setup**
2. Click **Create New**
3. Select the **Employee** and **Role** to associate with this integration
4. Set the **Application** to `SuiteCloud Development Integration`
5. Upload the public certificate generated in the previous step: `sdf-credentials/public.pem`
6. Save the record
7. Copy the **Certificate ID** shown after saving — you will need it in the next step

> For visual guidance with screenshots, refer to the README in the repository.

---

After displaying the instructions, ask the user to paste the Certificate ID before continuing to the next step. Store it as `CERTIFICATE_ID` to use in subsequent steps.

## Step 4 — Create the auth ID in the SuiteCloud project

First, verify that the current directory is a valid SDF project by checking that a `manifest.xml` file exists. If it does not exist, stop and show this error to the user:

> No manifest.xml file was found. Run this skill from a valid SDF project folder.

Ask the user for the following two values:

- **Account ID**: The NetSuite account identifier. It can be found in the URL when logged into NetSuite, or at Setup > Company > Company Information > Account ID.
- **Auth ID**: An alias for this authentication (no spaces, all lowercase). Example: `my-ci`

Then build the command automatically:
- Use `CERTIFICATE_ID` collected in Step 3
- Resolve `--privatekeypath` as the absolute path to `sdf-credentials/private.pem` in the current working directory

Run the following command:

```
suitecloud account:setup:ci --account <ACCOUNT_ID> --authid <AUTH_ID> --certificateid <CERTIFICATE_ID> --privatekeypath <CWD>/sdf-credentials/private.pem
```

After the command completes successfully, confirm to the user that the auth ID has been created and the M2M setup is complete.
