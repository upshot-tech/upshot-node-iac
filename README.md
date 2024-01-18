# Table of Contents
1. [Deploy Index provider](#Deploy-Index-provider)
2. [Deploy appchain](#Deploy-appchain)


# Deploy Index provider

## Deploy with docker-compose
There is a `index-provider/docker-compose.yml` provided that sets up one head node and one worker node.

### Dependencies
- Have an available image of the `upshot-compute-node` , and reference it as a base on the `FROM` of the `Dockerfile`.
- Create a set of keys in the `keys` directory for your head and worker, and use them in the head and worker configuration(volume mapping already provided in `docker-compose.yml` file). If no keys are specified in the volumes, new keys are created. However, the worker will need to specify the `peer_id` of the head for defining it as a `BOOT_NODES`. You can bring head up first, get the key from the container, then use it in the `BOOT_NODES`. More info in the [b7s-docker-compose](https://github.com/blocklessnetwork/b7s-docker-compose/tree/main) repo.
- Provide a valid `UPSHOT_API_TOKEN` env var.

### Run
Once this is set up, run `docker compose -f index-provider/docker-compose.yml up`

## Deploy in k8s with helm chart
Upshot team uses a [universal-helm](https://upshot-tech.github.io/helm-charts/) chart to deploy applications into kubernetes clusters.
There is a `index-provider/values.yaml` provided that sets up one head node and one worker node.

### Dependencies
 - You need to have configured `kubeconfig` file on the computer to connect to the cluster and deploy the node.

### Deploy with the Helm Chart
1. Add upshot Helm chart repo:
```bash
helm repo add upshot https://upshot-tech.github.io/helm-charts

```

2. Install helm chart with the given values file:

```bash
helm install \
  index-provider \
  upshot/universal-helm \
  -f index-provider/values.yaml
```

# Making requests to the node
Once both nodes are up, a function can be tested by hitting:

```
curl --location 'http://localhost:6000/api/v1/functions/execute' --header 'Accept: application/json, text/plain, */*' --header 'Content-Type: application/json;charset=UTF-8' --data '{
    "function_id": "bafybeifcwnj2hyxigjhtxoqqoei54favlgttmxc5fbuxuq5rrlw4g7kt6q",
    "method": "upshot-function-example.wasm",
    "config": {
        "env_vars": [
            {
                "name": "BLS_REQUEST_PATH",
                "value": "/api"
            },
            {
                "name": "UPSHOT_ARG_PARAMS",
                "value": "yuga"
            }
        ],
        "number_of_nodes": 1
    }
}'

```

# Deploy appchain

## Dependencies
- Create a set of keys and initialise genesis, see example in `appchain/init.sh`.
- Provide a valid `$_AI_PROVIDER_NODE_` and `$_AI_PROVIDER_NODE_FUNCTION_ID` env var.

## Deploy with docker-compose
There is a `appchain/docker-compose.yml` provided that sets up one head node and one worker node.

### Run
Once this is set up, run `docker compose -f appchain/docker-compose.yml up`

## Deploy in k8s with helm chart
Upshot team uses a [universal-helm](https://upshot-tech.github.io/helm-charts/) chart to deploy applications into kubernetes clusters.
There is a `index-provider/values.yaml` provided that sets up one head node and one worker node.

### Dependencies
 - You need to have configured `kubeconfig` file on the computer to connect to the cluster and deploy the node.

### Deploy with the Helm Chart
1. Add upshot Helm chart repo:
```bash
helm repo add upshot https://upshot-tech.github.io/helm-charts

```

2. Install helm chart with the given values file:

```bash
helm install \
  index-provider \
  upshot/universal-helm \
  -f appchain/values.yaml
```

# Making requests to the node
Once both nodes are up, a function can be tested by hitting:

```
curl --location 'https://localhost:6000/api/v1/functions/execute' --header 'Accept: application/json, text/plain, */*' --header 'Content-Type: application/json;charset=UTF-8' --data '{
    "function_id": "bafybeifcwnj2hyxigjhtxoqqoei54favlgttmxc5fbuxuq5rrlw4g7kt6q",
    "method": "upshot-function-example.wasm",
    "config": {
        "env_vars": [
            {
                "name": "BLS_REQUEST_PATH",
                "value": "/api"
            },
            {
                "name": "UPSHOT_ARG_PARAMS",
                "value": "yuga"
            }
        ],
        "number_of_nodes": 1
    }
}'

```
