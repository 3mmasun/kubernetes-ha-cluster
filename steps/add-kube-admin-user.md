# Key steps

1. Create a certificate signing request for a given user with name indicated as `/CN=XXX`
2. Create CSR in kubernetes and approve it, that it's signed by Kubernetes CA
3. Extract the certificate and base64 decode it
4. Generate a kubeconfig file, to be used to authenticate to kube-api servers
5. Set cluster in kubeconfig file
6. Set user and credentials in kubeconfig file
7. Set context in kubeconfig file
8. Create namespace if it's used in kubeconfig and not yet created
9. Create rolebinding or clusterrolebinding to bind the new user with admin role (or any roles that's appropriate)

```bash
KUBE_USER=mary
openssl req -new -newkey rsa:4096 -nodes -keyout ${KUBE_USER}-k8s.key -out ${KUBE_USER}-k8s.csr -subj "/CN=${KUBE_USER}/O=devops"

# get the certificate signing request in base64
cat ${KUBE_USER}-k8s.csr | base64 | tr -d '\n'

kubectl create –edit -f k8s-csr.yaml:
apiVersion: certificates.k8s.io/v1beta1
kind: CertificateSigningRequest
metadata:
  name: ${KUBE_USER}-k8s-access
spec:
  groups:
  - system:authenticated
  request: # replace with csr from shell command
  usages:
  - client auth

kubectl certificate approve ${KUBE_USER}-k8s-access

kubectl get csr ${KUBE_USER}-k8s-access -o jsonpath='{.status.certificate}' | base64 --decode > ${KUBE_USER}-k8s-access.crt

cat ${KUBE_USER}-k8s-access.crt

# skip this step if ca.crt is available in the working directory
kubectl config view -o jsonpath='{.clusters[0].cluster.certificate-authority-data}' --raw | base64 --decode - > k8s-ca.crt

# set cluster in kubeconfig
kubectl config set-cluster $(kubectl config view -o jsonpath='{.clusters[0].name}') --server=$(kubectl config view -o jsonpath='{.clusters[0].cluster.server}') --certificate-authority=k8s-ca.crt --kubeconfig=${KUBE_USER}-k8s-config --embed-certs

# set user and credential in kubeconfig
kubectl config set-credentials ${KUBE_USER} --client-certificate=${KUBE_USER}-k8s-access.crt --client-key=${KUBE_USER}-k8s.key --embed-certs --kubeconfig=${KUBE_USER}-k8s-config

# set context in kubeconfig
kubectl config set-context ${KUBE_USER} --cluster=$(kubectl config view -o jsonpath='{.clusters[0].name}') --namespace=${KUBE_USER} --user=${KUBE_USER} --kubeconfig=${KUBE_USER}-k8s-config

kubectl create ns ${KUBE_USER}

kubectl label ns ${KUBE_USER} user=${KUBE_USER} env=sandbox

kubectl config use-context ${KUBE_USER} --kubeconfig=${KUBE_USER}-k8s-config

# Test ${KUBE_USER}-k8s-config
kubectl version --kubeconfig=${KUBE_USER}-k8s-config

kubectl create clusterrolebinding ${KUBE_USER}-admin --clusterrole=admin --user=${KUBE_USER}

# or 
kubectl create rolebinding ${KUBE_USER}-admin --namespace=${KUBE_USER} --clusterrole=admin --user=${KUBE_USER}

# test get resources
kubectl get pods --kubeconfig=${KUBE_USER}-k8s-config
```