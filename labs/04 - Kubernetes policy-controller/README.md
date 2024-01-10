# Kubernetes policy-controller

The policy-controller admission controller can be used to enforce policy on a Kubernetes cluster based on verifiable supply-chain metadata from cosign.

In this demo we will instruct Kubernetes to schedule Pod **ONLY IF** the container image are from specific subject and signed from a specific issuer.

Sigstore's policy-controller can be installed using helm:


```
$ helm repo add sigstore https://sigstore.github.io/helm-charts
$ helm repo update
$ kubectl create namespace cosign-system
$ helm install policy-controller -n cosign-system sigstore/policy-controller --devel
```

To let policy-controller to watch a namespace, just label it for example:

```console
$ kubectl label namespace default policy.sigstore.dev/include=true
```

Now apply the policy:

```console
$ kubectl apply -n default -f policy.yaml
```

Now, if you try to deploy an Nginx without signature or with a signature that can't be verified:


```console
$ kubectl apply -f fake-nginx.yaml 
Error from server (BadRequest): error when creating "fake-nginx.yaml": admission webhook "policy.sigstore.dev" denied the request: validation failed: no matching policies: spec.template.spec.containers[0].image
index.docker.io/library/nginx@sha256:2bdc49f2f8ae8d8dc50ed00f2ee56d00385c6f8bc8a8b320d0a294d9e3b49026
```

If you try to deploy an Nginx taken from **sunnyvaleit/my-nginx:latest**

```console
$ kubectl apply -f sunnyvale-nginx.yaml
deployment.apps/nginx-deployment created
```
