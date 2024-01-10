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

Rekor Transparencey Log can be queried via REST API, for example:

```console
$ curl -sX POST -H "Content-type: application/json" 'https://rekor.sigstore.dev/api/v1/index/retrieve' --data-raw "{\"hash\":\"sha256:08112964bae48f09fc319047c2a94c01a0f7684b6b64969cf8fc6286b8f6c13a\"}" | jq .
[
  "24296fb24b8ad77a791b8299919759d6f9352da41ffe05f31633b6ce7f35f320ca09d336abdeb2fe",
  "24296fb24b8ad77afdab6739f47f608a20af9aa7821f17e492a8884c6c38ac0b9e38cb429f9e68cf"
]
```

```console
$ curl -sX GET -H "Content-type: application/json" 'https://rekor.sigstore.dev/api/v1/log/entries/24296fb24b8ad77afdab6739f47f608a20af9aa7821f17e492a8884c6c38ac0b9e38cb429f9e68cf' | jq .
{
  "24296fb24b8ad77afdab6739f47f608a20af9aa7821f17e492a8884c6c38ac0b9e38cb429f9e68cf": {
    "body": "eyJhcGlWZXJzaW9uIjoiMC4wLjEiLCJraW5kIjoiaGFzaGVkcmVrb3JkIiwic3BlYyI6eyJkYXRhIjp7Imhhc2giOnsiYWxnb3JpdGhtIjoic2hhMjU2IiwidmFsdWUiOiIwODExMjk2NGJhZTQ4ZjA5ZmMzMTkwNDdjMmE5NGMwMWEwZjc2ODRiNmI2NDk2OWNmOGZjNjI4NmI4ZjZjMTNhIn19LCJzaWduYXR1cmUiOnsiY29udGVudCI6Ik1FVUNJR3JJd2Z0TE9BMklTcUtnR1lpK2RTVlp0N1FKR1cyK09iRVowUmlndkFQWUFpRUFtT1pnSzYwTXlkR2VQQ2g4Rk5ubFZWZDlsRzhyVXB4YXNCNEt3Vlk5dmprPSIsInB1YmxpY0tleSI6eyJjb250ZW50IjoiTFMwdExTMUNSVWRKVGlCUVZVSk1TVU1nUzBWWkxTMHRMUzBLVFVacmQwVjNXVWhMYjFwSmVtb3dRMEZSV1VsTGIxcEplbW93UkVGUlkwUlJaMEZGVlZnMVoydFlVV1ZSVlVsRFdWbEZlWGd2UlVONk1ETXhjamgwVUFwaGFHSTFUMDFUZEdGVGVFYzNRVWxCUVZCb2JIVlJVMnRaVDFGNGQxRjNOWFF4U1ZaV1IzVnBhRkl4ZWpONmEyeHFlVGhYYUhjNU56Sm5QVDBLTFMwdExTMUZUa1FnVUZWQ1RFbERJRXRGV1MwdExTMHRDZz09In19fX0=",
    "integratedTime": 1702487713,
    "logID": "c0d23d6ad406973f9559f3ba2d1ca01f84147d8ffc5b8445c224f98b9591801d",
    "logIndex": 56320072,
    "verification": {
      "inclusionProof": {
        "checkpoint": "rekor.sigstore.dev - 2605736670972794746\n58543711\nZqUPFynBTvPj/dA9YhUmiH3IDCpionSHw8QfuJlYtSU=\nTimestamp: 1704884598354420783\n\n— rekor.sigstore.dev wNI9ajBFAiEA5Dy5V8JMo+s3lgGUa68O/aTkE2RuXM5si+51YBPemUkCIAhi4u0YbmeQihxoCkGPCnL3LKqCoj5btfTdDPCPGLg6\n",
        "hashes": [
          "10e891d08187f8a6f316d20332377cb9c4474757d7ad390d8b9651bf9b857999",
          "9013028a18bd454b1c65e052f103a6c8ae852e0deef7823bbe0bab96870226ed",
          "1aa59ce8c964f5cba91121cbfc504f9601e94e4c3551cb0cc0c569358322b48d",
          "5c012ca29d1aa5bf16e1e344db7d85859e622314d64bd5b88f693f665aea0970",
          "d4ef854806e7d5db340a68b4a91ea42a4ed5c61f4eb99bce87ea7be1d8b58a8a",
          "c4bd4233d286a6c389ff37f8639516f3720c3f35710198d805b9ff70b0acd57c",
          "1ca0508dfed5c6eb9a0a067ffa45c527443aa238ce17cab05cafea1d822867cd",
          "bdcb99b1e0dc3641932d31632255559ac83c915fadd99aef12e41be2ad1432b2",
          "3693c3420b3e750d94be77fa82651c031166d71958901de8d0dec8fecc7759d7",
          "219e65934e61e9a9dbb30a3aa7e0c16bb9b602003266d56501452ca4669db0e1",
          "3c8143b6a1928d930caf52e32b4dd23ee258ca6330d0e5f6c987589c4a838e5d",
          "89928f1381c5b8ae823b217626522dbac46c7112574624c98c0f388569debb82",
          "a6da72592993318e7c6aa3af265c20d2b16e2602d119bf6ba12b78b12d231145",
          "76a4ca7c80a2d0d9b08461ce0de58ea4ce1cc0d08eb2a3f00f4fa166c80460e1",
          "a569069b4894ea7b414b81e56b35efea65526b7bc42a23270151066cfb29957f",
          "28414f63887443ab4a59d303695236d6f351a6118da84d6d5c8a355e741ae691",
          "68c5cdeb9c27e66ced4d2c967fa44ededff9e9a2a90a8428e392bf499ce93e50",
          "8c4e90beb054195e5ffca21a24e7441c45db457a88bc0db70076b69c26d16f10",
          "1322850526def1a52fa481eac98b0f164972fc49a24ab4ad5a7f2d22aca23b7c",
          "bfe409b065f53db6067f5b507d11a298e818579fcce2433c1dc389fe117cfcf8",
          "3d2a60ce71f99e29cb2e8f2ea2ad5219dfac8e18f38a0e8b8386b001e661d945",
          "74642ef0e334a0f45524d4fbb93d907a89889e51caad152e61565272a6c950b5",
          "4f803cd094642fb6c46f5e0e951939536d92b811f5979678592f62b6f1fb7872",
          "98c486feb5d87092a78a46c4b5be04868654900affc2e86ffb20074dc73a883a",
          "6969c49bd73f19bf28a5eaeabd331ddd60502defb2cd3d96e17b741c80adec6c"
        ],
        "logIndex": 52156641,
        "rootHash": "66a50f1729c14ef3e3fdd03d621526887dc80c2a62a27487c3c41fb89958b525",
        "treeSize": 58543711
      },
      "signedEntryTimestamp": "MEUCIQDiej9JxmJicJ6sf5R65oh8Jhv9qPIe5mvjJTK1lUoYEQIgRNw5BAy4To0QTkKtqb4lAGktcppyCWtBd5U7vWOpq74="
    }
  }
}
```