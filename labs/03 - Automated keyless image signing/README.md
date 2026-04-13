# Automated keyless image signing

## Verify the signature

```bash
cosign verify \                                                                                                                                                                 
  --certificate-identity-regexp ".*" \
  --certificate-oidc-issuer-regexp ".*" \
  sunnyvaleit/my-nginx:latest
```
