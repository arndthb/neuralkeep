# Private mobile access with Tailscale

The reference setup uses **Tailscale Serve** to proxy a local Karakeep HTTP port to a private HTTPS endpoint accessible only within the tailnet.

Example (adapt the port and HTTPS listener to your own setup):

```bash
tailscale serve --bg --https=8443 http://127.0.0.1:3002
tailscale serve status
```

Open the resulting HTTPS address on a device connected to the same tailnet. You may add the page to your phone's home screen.

**Important:** The application authentication URL (`NEXTAUTH_URL`) must match the address used for sign-in if authentication redirects fail. Changing this may require a controlled application restart.

Tailscale Serve is not Tailscale Funnel. Do not enable public exposure unintentionally. Never publish your real tailnet hostname or private IP address in issues, screenshots or configuration files.
