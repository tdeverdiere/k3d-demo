# k3d-demo

- Fork from: https://github.com/iwilltry42/k3d-demo for windows.

The main difference is that the app is not python but a node web server.

## Requirements

- `docker`
- `k3d >= v3.0.0-rc.7`

## Resources

- https://k3d.io/
- https://github.com/rancher/k3d
- v3 Release Candidate: https://github.com/rancher/k3d/releases/tag/v3.0.0-rc.7
  - install e.g. via `wget -q -O - https://raw.githubusercontent.com/rancher/k3d/main/install.sh | TAG=v3.0.0-rc.7 bash`
  
- k3d in Rancher Meetup May 2020: https://youtu.be/hMr3prm9gDM?t=2103 (with old version, using the old syntax!)

## Run the demo

### Create the Cluster

- Create a cluster: `k3d cluster create demo --api-port 6550 --servers 1 --agents 3 --port 8080:80@loadbalancer --volume ./sample:/src@all --wait`

### Access the Cluster

- List clusters: `k3d cluster list`
- Update the default kubeconfig with the new cluster details: `k3d kubeconfig merge demo --merge-default-kubeconfig --switch-context`
- Use kubectl to checkout the nodes: `kubectl get nodes`

### Use the Cluster

- Build the sample-app: `docker build sample/ -f sample/Dockerfile -t sample-app:local`
- Load the sample-app image into the cluster: `k3d image import -c demo sample-app:local`
- Create a new 'demo' namespace: `kubectl create namespace demo`
- Switch to the new 'demo' namespace: `kubectl config set-context --current --namespace=demo`
- Deploy the sample app with helm: `helm upgrade --install sample-app sample/conf/charts/sample-app --namespace demo --set app.image=sample-app:local`
- Access the sample app frontend via ingress (on chrome): `chrome --new-window http://localhost:8080`

### Grow the Cluster

- Add 2 agents to the cluster: `k3d node create new-agent --cluster demo --role agent --replicas 2`
- Use kubectl to see the new nodes: `kubectl get nodes`

### Start/Stop the Cluster

- Stop the cluster: `k3d cluster stop demo`
- Try to access the stopped cluster: "kubectl get nodes`
- Start the cluster: `k3d cluster start demo`
- Access restarted cluster: `kubectl get nodes`

### The End

- Delete the Cluster: `k3d cluster delete demo`
