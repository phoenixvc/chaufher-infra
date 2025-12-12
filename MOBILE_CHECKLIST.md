# Mobile Integration Checklist

This document contains the mobile checklist, environment variable examples, and code snippets referenced for app integration.

## API Contract
- OpenAPI/REST spec URL (replace with canonical spec): https://api.chaufher.app/openapi.json

## Quick checklist for mobile engineers
- Confirm `API_URL` and `SIGNALR_URL` for the environment (dev/staging/prod).
- Use OAuth/JWT via Azure AD/B2C; do not embed client secrets in the app (store in CI/Key Vault).
- Use SignalR with configured keep-alive settings and fallbacks for offline mode.
- Never call Key Vault directly from the app — request secrets via your backend service.
- Wire telemetry (App Insights / Sentry) and emit trace IDs for cross-service correlation.
- Use feature flags from Azure App Configuration for staged rollouts.

## Suggested environment variables (examples)
- `API_URL=https://api.dev.chaufher.app`
- `SIGNALR_URL=wss://api.dev.chaufher.app/hubs/notifications`
- `KEY_VAULT_URI=https://chaufher-dev-kv.vault.azure.net/` (CI/backend)
- `AZURE_AD_B2C_TENANT=...`
- `FCM_SERVER_KEY=...` (CI/infra only)
- `APNS_KEY=...` (CI/infra only)
- `TELEMETRY_CONNECTION=...`

## Examples (moved from README)
### Flutter SignalR example (using `signalr_core`)
```dart
import 'package:signalr_core/signalr_core.dart';

final hubConnection = HubConnectionBuilder()
  .withUrl(const String.fromEnvironment('SIGNALR_URL'),
      HttpConnectionOptions(accessTokenFactory: () async => await getAccessToken()))
  .build();

await hubConnection.start();
hubConnection.on('ReceiveLocation', (args) {
  // handle real-time update
});
```

### Backend Key Vault sample (Node.js)
```js
const { DefaultAzureCredential } = require('@azure/identity');
const { SecretClient } = require('@azure/keyvault-secrets');

const credential = new DefaultAzureCredential();
const client = new SecretClient(process.env.KEY_VAULT_URI, credential);

async function getSecret(name) {
  const secret = await client.getSecret(name);
  return secret.value;
}
```

> Note: Keep secrets and credentials out of code and never commit them to source control. Access Key Vault from backend services using managed identities or appropriate service principals.