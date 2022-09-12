# ubi-helm

Deploy a pure [ubi image](https://catalog.redhat.com/software/containers/ubi8/ubi/5c359854d70cc534b3a3784e) as a container with [Helm](https://helm.sh/).
With the [values.yaml](https://github.com/thomassuedbroecker/ubi-helm/blob/main/charts/ubi-helm/values.yaml) in the Helm chart we can configure [`replica count`](https://github.com/thomassuedbroecker/ubi-helm/blob/main/charts/ubi-helm/values.yaml#L6) of the pods. The deployed containers are only a basic ubi operating system.

```yaml
replicaCount: 2
```

### Step 1: Log on to OpenShift

```sh
oc login --token=YOUR_TOKEN --server=YOUR_MASTERNODE_SERVER
```

### Step 2: Create an OpenShift project

```sh
export PROJECT_NAME=obi-helm
oc new-project $PROJECT_NAME
```

### Step 3: Clone the repository

```sh
git clone https://github.com/thomassuedbroecker/ubi-helm.git
```

### Step 4: Navigate to the Helm chart directory

```sh
export CHART_NAME=ubi-helm
cd ./charts/$CHART_NAME
```

### Step 5: Verify the values.yaml content

```sh
cat values.yaml
```

* Example output:

```sh
# Default values for chart.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.
replicaCount: 2
image:
  repository: "ubi8/ubi"
  tag: latest
```

### Step 6: Verify the Helm chart

```sh
helm dependency update .
helm install --dry-run --debug $CHART_NAME .
helm lint
```

### Step 7: (optionl) Create a [Helm package](https://helm.sh/docs/helm/helm_package/)

```sh
helm package .
```

* Example output:

```sh
Successfully packaged chart and saved it to: /ubi-helm/charts/ubi-helm/ubi-helm-v1.0.0.tgz
```

### Step 7: Install helm chart

```sh
helm install $CHART_NAME .
```

### Step 8: Verify the running pods

```sh
oc get pods
POD=$(oc get -n $PROJECT_NAME pods | grep $CHART_NAME | head -n 1 | awk '{print $1;}')
oc exec -n $PROJECT_NAME $POD --container $CHART_NAME -- ls
```

* Example output:

```sh
NAME                                 READY   STATUS             RESTARTS         AGE
ubi-helm-ubi-helm-74d44b5c77-tzbcb   1/1     Running            7 (22s ago)      10m
ubi-helm-ubi-helm-74d44b5c77-wljvr   1/1     Running            7 (27s ago)      10m
bin
boot
dev
etc
home
...
```

### Step 9: Uninstall helm chart

```sh
helm uninstall $CHART_NAME
```
