# bitwarden_rs Kubernetes deployment in AWS

These manifests provide way to deploy fully functional and secure [`bitwarden_rs`](https://github.com/dani-garcia/bitwarden_rs) application in Kubernetes hosted in AWS. It provides little bit more than just simple deployment but you can use all or just part of the manifests depending on your needs and setup.

This has been deployed and tested with Kubernetes `1.10` but should work with `1.9` and `1.8` too.

To install run:

```sh
$ kubectl apply -f kubernets/ -n some-namespace
```

To remove run:

```sh
$ kubectl delete -f kubernets/ -n some-namespace
```

## Nginx proxy

The [nginx-ingress-controller](https://github.com/kubernetes/ingress-nginx) is an excellent ingress controller for Kubernetes. See [this gist](https://gist.github.com/icicimov/316ebea363e98824ce7a9aa3d34ffbb4) to deploy the controller in K8S cluster in AWS if you don't have one already running. It will create an ELB with `proxy-protocol` enabled and TCP backend protocol so we can have support for Websockets. 

If you already have a domain TLS certificate (maybe wildcard one) place it in a secret called `tls-secret` in the `default` namespace so Nginx can use it (optional).

Apart from that the gist provides features like [RBAC](https://kubernetes.io/docs/reference/access-authn-authz/rbac/), [HPA](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) and [PDB](https://kubernetes.io/docs/tasks/run-application/configure-pdb/) to insure smooth proxy operation.

To find out more see the project's [excellent documentation](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/).

## Settings

The Bitwarden related settings are set in the `configmap.yml` file. 

The SMTP username and password need to be set in the `smtp-secret.yml` file as `base64` encoded values.

There is another option to provide TLS certificate for the domain in the `ingress.yml` via the `secretName` parameter if you haven't done that already in the Nginx proxy settings (the difference is this certificate applies to this Ingress only versus globally for all domains when set in Nginx as default certificate).

The application gets deployed as Kubernetes [StatefulSet](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/) so we can easily use persistent EBS volume for our data. Some other settings like CPU and memory resources are set and can be modified as needed in this file. The app will run as user id 33 (www-data) instead of default root user for increased security. The included RBAC settings in the `rbac.yml` file provide access only to Kubernetes resources the app needs.

## Screenshot

![bitwarden_rs](bitwarden.png "bitwarden_rs")
