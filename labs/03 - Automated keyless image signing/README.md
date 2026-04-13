# Automated keyless image signing

## Sign

Manually run the GitHub action to build, sign and push the image on DockerHub.

## Verify the signature

```bash
cosign verify \                                                                                                                                                                 
  --certificate-identity-regexp ".*" \
  --certificate-oidc-issuer-regexp ".*" \
  sunnyvaleit/my-nginx:latest
```
