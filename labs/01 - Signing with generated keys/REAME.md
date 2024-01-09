# Image signing with generated keys

## Prerequisites

Having completed the [lab 00 (Build a container image)](../00/README.md)

## Keys creation and signing

Generate a key pair (when prompted for password, type ENTER twice to set an empty password)

```console
$ cosign generate-key-pair
```

Cosign should have created two files:

- cosign.key
- cosign.pub


Before signing, tag your image using the Docker Hub username as a prefix (in my case "sunnyvaleit")

```console
$ docker tag -t sunnyvaleit/my-nginx my-nginx
```

Push the image on Dockerhub (BE AWARE: use your Dockerhub username as name prefix):

```console
$ docker push sunnyvaleit/my-nginx
```

The correct image name (made of username/my-nginx) is needed before signing (as well as the presence of this image on the registry) so as Cosign can verify the following:

- The image is not anonymous 
- You have been authenticated by the registry with the username provided

To sign the image, type the following (BE AWARE: use your Dockerhub username as name prefix):

```console
$ cosign sign --key cosign.key sunnyvaleit/my-nginx
```

If prompted for password, use the password you used when creating the key-pair.

When prompted "Are you sure you would like to continue?", type **y**

Have a look of the command output, in my case:

```
tlog entry created with index: 56320072
Pushing signature to: index.docker.io/sunnyvaleit/my-nginx
```

To verify the image signature (BE AWARE: use your Dockerhub username as name prefix):

```
$ cosign verify --key cosign.pub sunnyvaleit/my-nginx -o json | jq .
```

You should see the following output:

```json
[
  {
    "critical": {
      "identity": {
        "docker-reference": "index.docker.io/sunnyvaleit/my-nginx"
      },
      "image": {
        "docker-manifest-digest": "sha256:1f7686d02d35bbf88e57b8157b349cc6775fb85ac837ae9410170fade95ce449"
      },
      "type": "cosign container image signature"
    },
    "optional": {
      "Bundle": {
        "SignedEntryTimestamp": "MEQCIACiXKjNsPa/oL2l77hKEkkF3qF4rKDoyD7tk9rJ1iJEAiA+3WCCqrYy2IjgWLDSO/T+Yht/ybpd99ZXdrrRGBdOKw==",
        "Payload": {
          "body": "eyJhcGlWZXJzaW9uIjoiMC4wLjEiLCJraW5kIjoiaGFzaGVkcmVrb3JkIiwic3BlYyI6eyJkYXRhIjp7Imhhc2giOnsiYWxnb3JpdGhtIjoic2hhMjU2IiwidmFsdWUiOiIwODExMjk2NGJhZTQ4ZjA5ZmMzMTkwNDdjMmE5NGMwMWEwZjc2ODRiNmI2NDk2OWNmOGZjNjI4NmI4ZjZjMTNhIn19LCJzaWduYXR1cmUiOnsiY29udGVudCI6Ik1FVUNJR3JJd2Z0TE9BMklTcUtnR1lpK2RTVlp0N1FKR1cyK09iRVowUmlndkFQWUFpRUFtT1pnSzYwTXlkR2VQQ2g4Rk5ubFZWZDlsRzhyVXB4YXNCNEt3Vlk5dmprPSIsInB1YmxpY0tleSI6eyJjb250ZW50IjoiTFMwdExTMUNSVWRKVGlCUVZVSk1TVU1nUzBWWkxTMHRMUzBLVFVacmQwVjNXVWhMYjFwSmVtb3dRMEZSV1VsTGIxcEplbW93UkVGUlkwUlJaMEZGVlZnMVoydFlVV1ZSVlVsRFdWbEZlWGd2UlVONk1ETXhjamgwVUFwaGFHSTFUMDFUZEdGVGVFYzNRVWxCUVZCb2JIVlJVMnRaVDFGNGQxRjNOWFF4U1ZaV1IzVnBhRkl4ZWpONmEyeHFlVGhYYUhjNU56Sm5QVDBLTFMwdExTMUZUa1FnVUZWQ1RFbERJRXRGV1MwdExTMHRDZz09In19fX0=",
          "integratedTime": 1702487713,
          "logIndex": 56320072,
          "logID": "c0d23d6ad406973f9559f3ba2d1ca01f84147d8ffc5b8445c224f98b9591801d"
        }
      }
    }
  }
]
```

and, if you decode from base64 the content of **Payload.body** 

```console
$ echo "eyJhcGlWZXJzaW9uIjoiMC4wLjEiLCJraW5kIjoiaGFzaGVkcmVrb3JkIiwic3BlYyI6eyJkYXRhIjp7Imhhc2giOnsiYWxnb3JpdGhtIjoic2hhMjU2IiwidmFsdWUiOiIwODExMjk2NGJhZTQ4ZjA5ZmMzMTkwNDdjMmE5NGMwMWEwZjc2ODRiNmI2NDk2OWNmOGZjNjI4NmI4ZjZjMTNhIn19LCJzaWduYXR1cmUiOnsiY29udGVudCI6Ik1FVUNJR3JJd2Z0TE9BMklTcUtnR1lpK2RTVlp0N1FKR1cyK09iRVowUmlndkFQWUFpRUFtT1pnSzYwTXlkR2VQQ2g4Rk5ubFZWZDlsRzhyVXB4YXNCNEt3Vlk5dmprPSIsInB1YmxpY0tleSI6eyJjb250ZW50IjoiTFMwdExTMUNSVWRKVGlCUVZVSk1TVU1nUzBWWkxTMHRMUzBLVFVacmQwVjNXVWhMYjFwSmVtb3dRMEZSV1VsTGIxcEplbW93UkVGUlkwUlJaMEZGVlZnMVoydFlVV1ZSVlVsRFdWbEZlWGd2UlVONk1ETXhjamgwVUFwaGFHSTFUMDFUZEdGVGVFYzNRVWxCUVZCb2JIVlJVMnRaVDFGNGQxRjNOWFF4U1ZaV1IzVnBhRkl4ZWpONmEyeHFlVGhYYUhjNU56Sm5QVDBLTFMwdExTMUZUa1FnVUZWQ1RFbERJRXRGV1MwdExTMHRDZz09In19fX0=" | base64 -d | jq .
```

you get 

```json
{
  "apiVersion": "0.0.1",
  "kind": "hashedrekord",
  "spec": {
    "data": {
      "hash": {
        "algorithm": "sha256",
        "value": "08112964bae48f09fc319047c2a94c01a0f7684b6b64969cf8fc6286b8f6c13a"
      }
    },
    "signature": {
      "content": "MEUCIGrIwftLOA2ISqKgGYi+dSVZt7QJGW2+ObEZ0RigvAPYAiEAmOZgK60MydGePCh8FNnlVVd9lG8rUpxasB4KwVY9vjk=",
      "publicKey": {
        "content": "LS0tLS1CRUdJTiBQVUJMSUMgS0VZLS0tLS0KTUZrd0V3WUhLb1pJemowQ0FRWUlLb1pJemowREFRY0RRZ0FFVVg1Z2tYUWVRVUlDWVlFeXgvRUN6MDMxcjh0UAphaGI1T01TdGFTeEc3QUlBQVBobHVRU2tZT1F4d1F3NXQxSVZWR3VpaFIxejN6a2xqeThXaHc5NzJnPT0KLS0tLS1FTkQgUFVCTElDIEtFWS0tLS0tCg=="
      }
    }
  }
}
```

Cosign stores signature information in a container image annotation. The signature is embedded in the image manifest as an annotation field. When you sign an image using cosign, it appends an annotation with the signature information to the image manifest.

Specifically, cosign adds a dev.cosignproject.cosign/signature annotation to the image manifest. This annotation includes the cryptographic signature and other metadata related to the signing process.

When you use cosign verify to verify an image, the tool retrieves the signature from this annotation and checks it against the public key associated with the signer.