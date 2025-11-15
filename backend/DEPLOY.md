# Deployment Instructions for Render

## Setup Environment Variable on Render

Your backend needs the Firebase service account credentials to work. Since we can't commit sensitive files to Git, we use an environment variable.

### Step 1: Prepare the Firebase Service Account JSON

You need to convert your `iiitnr-attendence-app-f604e-firebase-adminsdk-fbsvc-e79f0f1be5.json` file to a **single-line JSON string**.

**Important**: Replace all actual newlines in the `private_key` field with `\\n` (two characters: backslash + n).

Example format:
```json
{"type":"service_account","project_id":"your-project-id","private_key_id":"...","private_key":"-----BEGIN PRIVATE KEY-----\\nYOUR_KEY_HERE\\n-----END PRIVATE KEY-----\\n","client_email":"...@iam.gserviceaccount.com",...}
```

**Note**: The `\\n` in the private_key is important - it must be a literal backslash followed by 'n', not actual newline characters.

### Step 2: Set Environment Variable on Render

1. Go to your Render dashboard: https://dashboard.render.com/
2. Click on your backend service
3. Go to the **"Environment"** tab (left sidebar)
4. Click **"Add Environment Variable"**
5. Set:
   - **Key**: `FIREBASE_SERVICE_ACCOUNT_JSON`
   - **Value**: Paste the single-line JSON from Step 1
6. Click **"Save Changes"**

Render will automatically redeploy your service.

### Step 3: Verify Deployment

Once deployed, check the logs in Render dashboard. You should see:
```
✅ Using Firebase service account from environment variable
🚀 IIIT NR Attendance Backend running on port 10000
```

Test the health endpoint:
```
https://iiitnrattendence-backend.onrender.com/health
```

Should return:
```json
{"status":"ok","timestamp":"2025-11-16T..."}
```

### Optional: Add QR Secret

For better security, also add:
- **Key**: `QR_SECRET`
- **Value**: Any long random string (generate with `openssl rand -base64 32`)

## How to Convert JSON to Single Line

### Option 1: Using Node.js
```bash
node -e "console.log(JSON.stringify(require('./iiitnr-attendence-app-f604e-firebase-adminsdk-fbsvc-e79f0f1be5.json')))"
```

### Option 2: Using PowerShell
```powershell
(Get-Content .\iiitnr-attendence-app-f604e-firebase-adminsdk-fbsvc-e79f0f1be5.json | ConvertFrom-Json | ConvertTo-Json -Compress)
```

### Option 3: Using online tool
1. Copy the contents of your JSON file
2. Go to https://jsonformatter.org/json-minify
3. Paste and minify
4. Copy the result

## Troubleshooting

If deployment fails:
1. Check Render logs for error messages
2. Verify the JSON is valid (no syntax errors)
3. Make sure `\\n` is used in the private key, not actual newlines
4. Ensure all required packages are in `package.json`

## Local Development

For local development, the code automatically uses the local JSON file, so you don't need to set the environment variable locally.

