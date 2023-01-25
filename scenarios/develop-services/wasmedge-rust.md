# Develop a WASMEdge Service in Rust

Before you start please ensure that you have run through the [getting started](getting-started/README.md) section to create an WASMEdge enabled kubernetes environment.

In this section we are going to build a very simple service that highlights the key aspects for running a WASM service on Knative.

We assume you have a running rust environment. If not please goto [rustup](https://rustup.rs/).

We also assume you have a container management system such as [docker](https://docker.io) or [podman](https://podman.io/getting-started/installation) installed.

1. Install `cargo generate` This is a templating engine that can parse git repos to create projects.
    ```
    cargo install generate
    ```
1. [install WASMEdge](https://wasmedge.org/book/en/quick_start/install.html)

1. Install `cargo wasi` with `cargo install cargo-wasi`

1. Set `CARGO_TARGET_WASM32_WASI_RUNNER=wasmedge` in your `.profile` or your user profile settings on Windows by clicking:
   System > Advanced system settings > Advanced > Environment Variables.


1. Now generate a project. The template projects are standard Rust projects with place holders that replace the name of the project and and the repo configs.
    ```
    cargo generate gh:knawd/gen-templates
    ```

    The prompts help complete some of the assets in the project and map as follows:

    1. The name of the project. This is used to give the cargo project a name and to fill out the service and build scripts for this project

    1. "Want to create a knative service definition?"
        This is a yes/no response and if you respond with yes a knative service yaml will be created that enables you to push to your kuberenetes environment from your desktop.

    1. "Which container registry would you like to use?"
    This is a freeform response that fills out the service file and the generated documentation.
    Usual responses `docker.io` or `quay.io`

    1. "Which container organisation would you like to use?"
    This is a freeform response that fills out the service file and the generated documentation.
    Example organisation on is `knawd` available here - https://quay.io/organization/knawd

1. Once the project is created you can explore the `README.md` to understand the different options available to you.

1. Let run the tests to make sure the projects has generated correctly. The tests are embedded at the bottom of the the `handler.rs` file. This is a convention in rust but they can be created in seperate files.
    ```
    cargo wasi test
    ```
1. Now look under the **build** section in the README.md. You will see a generated command to build the docker image. Run the command for `podman` or `docker`.
    i.e.
    ```
    docker build -t {{container_reg}}/{{container_org}}/{{project-name}} .
    ```
    or
    ```
    podman build -t {{container_reg}}/{{container_org}}/{{project-name}} .
    ```

1. Then in the **push** section of the README take the code snippets to push the application to your registry.
    i.e.
    ```
    docker push {{container_reg}}/{{container_org}}/{{project-name}}
    ```
    or
    ```
    podman push {{container_reg}}/{{container_org}}/{{project-name}}
    ```

1. Finally you can **deploy** your knative service by applying the service to the kubernetes cluster using the deploy step in the Project README.
    i.e.
    ```
    kubectl apply -f service.yaml
    ```
    or
    ```
    oc apply -f service.yaml
    ```



