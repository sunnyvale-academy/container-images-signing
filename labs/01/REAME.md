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
$ cosign verify --key cosign.pub sunnyvaleit/my-nginx
```
