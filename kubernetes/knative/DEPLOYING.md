## Deploying to Kubernetes as a Knative Service

![Knative Logo](https://avatars.githubusercontent.com/u/35583233?s=200&v=4)

You can containerize this template app and deploy it as a Knative Service.
See the [More World - Spring Boot Java](https://knative.dev/docs/serving/samples/more-world/Moreworld-java-spring/) sample for details.

You need to have Knative Service installed on your cluster. See https://knative.dev/docs/install/ for instructions.

We have included a `skaffold.yaml` file to make it easier to deploy the function app to a local cluster like [minikube](https://minikube.sigs.k8s.io/) or [kind](https://kind.sigs.k8s.io/).

You can install Skaffold by following these instructions: https://skaffold.dev/docs/install/

### Specifying image repo with skaffold

You need to specify the `default-repo` which is the registry prefix for the image that is being built. For Docker Hub the prefix would be your Docker ID, for other registries it would typically be the registry URL plus your project.

> **NOTE**: You must specify a registry prefix where you have permission to push images.

You can do this globally by running:

```bash
skaffold config set --global default-repo ${REGISTRY_PREFIX}
```

Or, you can set it for the current Kubernetes context:

```bash
skaffold config set default-repo ${REGISTRY_PREFIX}
```

Finally, you can specify it as part of the `run` command:

```bash
skaffold run --default-repo ${REGISTRY_PREFIX} --port-forward --tail
```

### Deploying to a cluster using Skaffold

To build and deploy the app to a remote or local cluster run:

```
skaffold run --default-repo ${REGISTRY_PREFIX}
```

To uninstall the app run:

```
skaffold delete
```

### Deploying as a native image to a cluster using Skaffold

> **NOTE:** The native image compilation is resource intensive and takes several minutes.

See the [System Requirements](https://docs.spring.io/spring-native/docs/current/reference/htmlsingle/#getting-started-buildpacks-system-requirements) note in the Spring Native documentation for suggestions on Docker configuration.

To build and deploy the app to a remote or local cluster run:

```
skaffold run -p native --default-repo ${REGISTRY_PREFIX} --tag native
```

To uninstall the app run:

```
skaffold delete
```

### Accessing the app deployed to your cluster

If you don't have `curl` installed it can be installed using downloads here: https://curl.se/download.html

You need to look up the Ingress endpoint for the Knative Serving install. See the [Install a networking layer](https://knative.dev/docs/install/install-serving-with-yaml/#install-a-networking-layer) and [Configure DNS](https://knative.dev/docs/install/install-serving-with-yaml/#configure-dns) sections for instructions for the different netwoking layers.

For _Kourier_ we can simply run the following port-forward command:

```
kubectl port-forward --namespace kourier-system service/kourier 8080:80
```

For _Contour_ we can simply run the following port-forward command:

```
kubectl port-forward --namespace contour-external service/envoy 8080:80
```

To invoke the deployed function run the following `curl` command in another terminal window:

```
curl localhost:8080 -w'\n' -H 'Content-Type: text/plain' -H 'Host: more-fun.default.example.com' -d Fun
```
