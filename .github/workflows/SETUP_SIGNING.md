# APK Signing Setup for GitHub Actions

This document explains how to set up APK signing for automated builds in GitHub Actions.

## Required Secrets

To enable signed release APK builds, you need to add the following secrets to your GitHub repository:

1. **SIGNING_KEY**: Base64 encoded keystore file
2. **SIGNING_KEY_ALIAS**: Alias of the signing key in the keystore
3. **KEY_STORE_PASSWORD**: Password for the keystore
4. **KEY_PASSWORD**: Password for the signing key

## How to Generate and Encode the Keystore

### 1. Generate a Keystore (if you don't have one)

```bash
keytool -genkey -v -keystore pebble-navigation.keystore -alias pebble-nav-key -keyalg RSA -keysize 2048 -validity 10000
```

### 2. Encode the Keystore to Base64

```bash
base64 -i pebble-navigation.keystore | tr -d '\n'
```

### 3. Add Secrets to GitHub Repository

1. Go to your repository on GitHub
2. Navigate to Settings → Secrets and variables → Actions
3. Click "New repository secret"
4. Add each of the required secrets:
   - `SIGNING_KEY`: Paste the base64 encoded keystore
   - `SIGNING_KEY_ALIAS`: Your keystore alias (e.g., "pebble-nav-key")
   - `KEY_STORE_PASSWORD`: Your keystore password
   - `KEY_PASSWORD`: Your key password

## How the Signing Works

- The GitHub Actions workflow will only sign release APKs when pushed to the main branch
- The signing step is conditional and only runs if the required secrets are configured
- If secrets are not set, the workflow will build unsigned release APKs
- Debug APKs are always unsigned and built for all branches

## Security Notes

- Never commit your keystore file to the repository
- Use strong, unique passwords for your keystore and key
- Store the keystore file securely as a backup
- Consider using different keys for development and production

## Testing the Setup

1. Push changes to a feature branch → Builds unsigned debug APK
2. Push to main branch → Builds signed release APK (if secrets are configured)
3. Check the Actions tab in GitHub to verify the signing process

## Troubleshooting

If the signing step fails:
1. Verify all required secrets are set correctly
2. Check that the SIGNING_KEY is properly base64 encoded
3. Ensure the keystore password and key password are correct
4. Verify the key alias matches what's in your keystore
