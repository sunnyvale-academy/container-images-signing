# Automated keyless image signing

## Sign

Manually run the GitHub action to build, sign and push the image on DockerHub.

## Verify the signature

```bash
$ rekor-cli search --email  denis.maggiorotto@sunnyvale.it

Found matching entries (listed by UUID):
108e9186e8c5677a6dfc3c8078afb523073d1e084aafbdd3bed821d5ffbfe740c66ade7f29f6f475
24296fb24b8ad77a28bb7c9549b4f974009476c173f543197f0113b68e3371d81cf7145ccd940fbc
24296fb24b8ad77a791b8299919759d6f9352da41ffe05f31633b6ce7f35f320ca09d336abdeb2fe
```

```bash
$ rekor-cli get --uuid 108e9186e8c5677a6dfc3c8078afb523073d1e084aafbdd3bed821d5ffbfe740c66ade7f29f6f475

LogID: c0d23d6ad406973f9559f3ba2d1ca01f84147d8ffc5b8445c224f98b9591801d
Index: 1042704897
IntegratedTime: 2026-03-05T15:10:07Z
UUID: 108e9186e8c5677a6dfc3c8078afb523073d1e084aafbdd3bed821d5ffbfe740c66ade7f29f6f475
Body: {
  "DSSEObj": {
    "envelopeHash": {
      "algorithm": "sha256",
      "value": "31d5562d93674d2572b9d6878c4cb9a8858433ff1ac6a9e9440cea870dc4fec8"
    },
    "payloadHash": {
      "algorithm": "sha256",
      "value": "0afb821646d21b08b08c94665641ada254559dff9d856909bc049af5c9571dee"
    },
    "signatures": [
      {
        "signature": "MEQCIDGx9e0KNTiPgA+FrYnJcWMffu0c5bvJN0YVGLyqPYAfAiBdx59KbEwHGeFQmKbWKzI1kGVxNYcPJUNvcIFGLRgaLQ==",
        "verifier": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMyekNDQW1LZ0F3SUJBZ0lVSzdpaVZTWDRVUlc2R2c0b1ZPQUdzYkY2cXljd0NnWUlLb1pJemowRUF3TXcKTnpFVk1CTUdBMVVFQ2hNTWMybG5jM1J2Y21VdVpHVjJNUjR3SEFZRFZRUURFeFZ6YVdkemRHOXlaUzFwYm5SbApjbTFsWkdsaGRHVXdIaGNOTWpZd016QTFNVFV4TURBM1doY05Nall3TXpBMU1UVXlNREEzV2pBQU1Ga3dFd1lICktvWkl6ajBDQVFZSUtvWkl6ajBEQVFjRFFnQUV6b3EzelpaSyt5alExZ3ArM1IydjQ0MHV4MVpMUEROMHhEZFcKNzM2bExyejJQMDVtaC93OWMwc1Jqb3k1ZWI4ZXdJcmxYbDIwaThuZUc5OTZ6QXdkU3FPQ0FZRXdnZ0Y5TUE0RwpBMVVkRHdFQi93UUVBd0lIZ0RBVEJnTlZIU1VFRERBS0JnZ3JCZ0VGQlFjREF6QWRCZ05WSFE0RUZnUVUrblN3CmQyVVhSbjRScUJuNFJnUXJhZlVZa21zd0h3WURWUjBqQkJnd0ZvQVUzOVBwejFZa0VaYjVxTmpwS0ZXaXhpNFkKWkQ4d0xBWURWUjBSQVFIL0JDSXdJSUVlWkdWdWFYTXViV0ZuWjJsdmNtOTBkRzlBYzNWdWJubDJZV3hsTG1sMApNQ3dHQ2lzR0FRUUJnNzh3QVFFRUhtaDBkSEJ6T2k4dloybDBhSFZpTG1OdmJTOXNiMmRwYmk5dllYVjBhREF1CkJnb3JCZ0VFQVlPL01BRUlCQ0FNSG1oMGRIQnpPaTh2WjJsMGFIVmlMbU52YlM5c2IyZHBiaTl2WVhWMGFEQ0IKaVFZS0t3WUJCQUhXZVFJRUFnUjdCSGtBZHdCMUFOMDlNR3JHeHhFeVl4a2VISmxuTndLaVNsNjQzanl0LzRlSwpjb0F2S2U2T0FBQUJuTDZNaVBzQUFBUURBRVl3UkFJZ0xOVy84U0h4UlZEMWx0YWlEcXlxYlR5TlJJZTNOT0UwCnlKMDBLaE96WG5zQ0lFYkZKcnFiN0FKalJXcUMyMlVlUks4S3EyNXFyczI1aHRDZFMzMmNqRkFiTUFvR0NDcUcKU000OUJBTURBMmNBTUdRQ01GM05EaDB2TXRoeURlYXFFMUZTeGpUQ05GL1JPMkZNTzJYVmFkTFNUU294UkRsRAoxNGVQOVdvV3c4RDF3Skx5bHdJd2FqZ0NyWkRDZkRVRmlTQUpTdFh4YXpLd1dXQno3NnJvRmEvbGtmbE02bFd5Cm1PQTFnZHYvT2tWeVdVcWFTdUhOCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K"
      }
    ]
  }
}
```

```bash
$ cosign verify \                                                                                                                                                                 
  --certificate-identity-regexp ".*" \
  --certificate-oidc-issuer-regexp ".*" \
  sunnyvaleit/my-nginx:latest
```
