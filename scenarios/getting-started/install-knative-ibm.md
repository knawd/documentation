# Install knative and WASMEdge on IBM Cloud

1. Create a cluster called `knawd-k8s` on IBM Cloud.
   For full instructions see https://cloud.ibm.com/docs/containers?topic=containers-getting-started

1. Install the IBM Cloud Cli.
   The instructions please ensure you install the https://cloud.ibm.com/docs/cli?topic=cli-getting-started

1. Install the kubernetes service plugin
   https://cloud.ibm.com/docs/cli?topic=cli-install-devtools-manually#idt-install-kube


1. Once the cluster is complete login with the ibmcloud cli

   ```bash
   ibm cloud login
   ```

1. Now configure the kubectl connection
   ```
   ibmcloud ks cluster config -c knawd-k8s
   ```

1. We are going to follow a summary of the [instructions for the knative website](https://knative.dev/docs/install/yaml-install/serving/install-serving-with-yaml/#prerequisites).

1. Install the custom resource definitions which are the objects such as `service` that you will use to deploy an app.
   ```
   kubectl apply -f https://github.com/knative/serving/releases/download/knative-v1.8.3/serving-crds.yaml
   ```

1. Then install the knative serving core
   ```
   kubectl apply -f https://github.com/knative/serving/releases/download/knative-v1.8.3/serving-core.yaml
   ```

1. Now we install the networking layer
   ```
   kubectl apply -f https://github.com/knative/net-kourier/releases/download/knative-v1.8.1/kourier.yaml
   ```

1. And Patch the knative space
   ```
   kubectl patch configmap/config-network \
    --namespace knative-serving \
    --type merge \
    --patch '{"data":{"ingress-class":"kourier.ingress.networking.knative.dev"}}'
   ```

1. Finally configure dns
   ```
   kubectl apply -f https://github.com/knative/serving/releases/download/knative-v1.8.3/serving-default-domain.yaml
   ```

1. Patch the knative service to allow for the crun runtime to be specified as an attribute in the service.
   ```
   kubectl patch configmap/config-features -n knative-serving --type merge --patch '{"data":{"kubernetes.podspec-runtimeclassname":"enabled"}}'
   ```

1. Now run install the deployment project
   ```
   git clone https://github.com/uirlis/wasi-crun-deployer
   cd wasi-crun-deployer/charts/wasi-crun-deployer
   helm install wasi-crun-deployer . --create-namespace --namespace knawd --set job.autoRestart=true
   ```

1. Finally run the tempate helloworld service.

   ```
   git clone https://github.com/uirlis/helloworld-rust-wasi
   cd helloworld-rust-wasi
   kubectl apply -f service.yaml
   ```

1. Get the external IP of the kourier network
   ```
   kubectl get svc kourier -n kourier-system
   ```

1. Visit http://helloworld-rust-wasi.default.<EXTERNAL-IP>.sslip.io/
