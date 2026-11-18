# Entra Conditional Access and sign-in hunting queries

KQL queries for Microsoft Defender XDR advanced hunting that help you review Microsoft Entra Conditional Access with real sign-in evidence: testing a location restriction for an application, finding the gaps in an existing policy set, and building an inventory of service principal sign-ins.

All queries are read-only. They run in the Defender portal (security.microsoft.com > Hunting > Advanced hunting) with the Global Reader or Security Reader role, against the `EntraIdSignInEvents` and `EntraIdSpnSignInEvents` tables.

## Queries

### 01 - Application location restriction

Use these when you restrict an application (for example a VDI gateway) to approved countries and networks with an "include approved locations" policy plus a "block everywhere else" policy in report-only mode.

| File | What it shows |
|---|---|
| `01-ca-policy-results-for-app.kql` | How each matching policy evaluated for sign-ins to the app, including report-only results |
| `02-app-signins-outside-allowed-countries.kql` | Successful sign-ins from outside the allowed countries - run this before you design the policy |
| `03-report-only-block-impact.kql` | Sign-ins the report-only block policy would stop at go-live, by country, user count and IP |

### 02 - Conditional Access review

| File | What it shows |
|---|---|
| `04-single-factor-from-trusted-locations.kql` | Single-factor sign-ins from trusted named locations, by application - the cost of excluding "All trusted locations" from MFA |
| `05-admin-tools-single-factor-by-logon-type.kql` | The same for admin and privileged tools, split interactive versus non-interactive |
| `06-no-policy-signins-by-account.kql` | Sign-ins where no Conditional Access policy applied, by account |

### 03 - Service principals

| File | What it shows |
|---|---|
| `07-service-principal-inventory.kql` | Every service principal and managed identity signing in, with IPs, countries, resources and last seen |
| `08-service-principals-single-ip.kql` | Application service principals that only sign in from one IP - candidates for a location-locked workload identity policy |

## Before you run them

- Set the `let` variables at the top of each query: the application ID, policy names, allowed countries and look-back period.
- `reportOnlyFailure` means the sign-in would have been blocked, or would have failed the grant, if the policy were On.
- Single-factor counts include non-interactive token refreshes. Use query 05 to separate them before drawing conclusions about interactive sign-ins.
- A large `{PII Removed}` row in query 06, with no object ID or IP address, is normally external users from another tenant (B2B direct connect, Teams federation or cross-tenant passthrough), not a local account excluded from Conditional Access.
- Managed identities sit outside Conditional Access. Conditional Access for application service principals needs Microsoft Entra Workload ID Premium and only applies to single-tenant apps registered in your own tenant.

## Author

Seán O'Farrell - [cloud-secure.ai](https://cloud-secure.ai/)

## Licence

MIT - see [LICENSE](LICENSE).
