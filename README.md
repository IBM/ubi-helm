# ubi-helm

Deploy a pure [ubi image](https://catalog.redhat.com/software/containers/ubi8/ubi/5c359854d70cc534b3a3784e) as a container with [Helm](https://helm.sh/).
With the [values.yaml](https://github.com/thomassuedbroecker/ubi-helm/blob/main/charts/ubi-helm/values.yaml) in the Helm chart can configure [`replica count`](https://github.com/thomassuedbroecker/ubi-helm/blob/main/charts/ubi-helm/values.yaml#L6) of the pods. 
The deployed container is only the basic ubi operating system. 

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

### Step 4: Verify the helm chart

```sh
export CHART_NAME=ubi-helm
cd ./charts/$CHART_NAME
```

### Step 5: Verify the helm chart

```sh
helm dependency update .
helm install --dry-run --debug $CHART_NAME .
```

### Step 6: Install helm chart

```sh
helm lint
helm install $CHART_NAME .
```

### Step 7: Verify the running pod

```sh
oc get pods
POD=$(oc get -n $PROJECT_NAME pods | grep $CHART_NAME | head -n 1 | awk '{print $1;}')
oc exec -n $PROJECT_NAME $POD --container $CHART_NAME -- ls
```

* Example output:

```sh
bin
boot
dev
etc
home
...
```

### Step 8: Uninstall helm chart

```sh
helm uninstall ubi-helm
```
