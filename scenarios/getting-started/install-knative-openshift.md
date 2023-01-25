# Install knative and WASMEdge on OpenShift

## WIP - This is a first draft and NOT had a clean cluster validation.

1. Follow the excellent OpenShift documentation to install OpenShift Serverless which is based on knative.
   Ensure that you also install the knative-serving service into the `knative-serving` project

    https://docs.openshift.com/container-platform/4.11/serverless/install/install-serverless-operator.html

   ```
1. Patch the knative service to allow for the crun runtime to be specified as an attribute in the service.
   ```
   oc patch configmap/config-features -n knative-serving --type merge --patch '{"data":{"kubernetes.podspec-runtimeclassname":"enabled"}}'
   ```
1. As the deployer configures the node it needs to run with elevated privaliges. We should create a dedicated project for it and apply labels to minimise the warnings returned.
   ```
   oc new-project knawd
   oc label --overwrite ns knawd   pod-security.kubernetes.io/enforce=privileged   pod-security.kubernetes.io/enforce-version=v1.24
   oc label --overwrite ns knawd   pod-security.kubernetes.io/audit=privileged   pod-security.kubernetes.io/enforce-version=v1.24
   oc label --overwrite ns knawd   pod-security.kubernetes.io/warn=privileged   pod-security.kubernetes.io/enforce-version=v1.24
   ```
   This should be part of the helm package in later releases.

1. Now run install the project into the `knawd` on the cluster
   ```
   git clone https://github.com/uirlis/wasi-crun-deployer
   cd wasi-crun-deployer/charts/wasi-crun-deployer
   helm install wasi-crun-deployer . --namespace knawd -f rhel8-values.yaml --set job.autoRestart=true
   ```

1. Finally run the sample helloworld service to validate the installation.

   ```
   git clone https://github.com/uirlis/helloworld-rust-wasi
   cd helloworld-rust-wasi
   kubectl apply -f service.yaml
   ```