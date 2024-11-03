# Google Kubernetes Engine (GKE) Samples

This repository contains sample applications used in
[Google Kubernetes Engine (GKE)](https://cloud.google.com/kubernetes-engine/) tutorials.

See the following resources to get started:

- [Google Kubernetes Engine - Guides](https://cloud.google.com/kubernetes-engine/docs/concepts/kubernetes-engine-overview)
- [Google Kubernetes Engine - Quickstart](https://cloud.google.com/kubernetes-engine/docs/deploy-app-cluster)

Or click on the following button to open the interactive quickstart demo:

[![Open in Cloud Shell](https://gstatic.com/cloudssh/images/open-btn.svg)](https://ssh.cloud.google.com/cloudshell/editor?cloudshell_git_repo=https://github.com/GoogleCloudPlatform/kubernetes-engine-samples&cloudshell_tutorial=README.md&cloudshell_workspace=quickstarts/hello-app)

## Contributing

* To learn how you can contribute new samples or fixes, see [the contributing guide](/.github/CONTRIBUTING.md).

## Licensing

* This repository is licensed using an [Apache-2.0 license](/LICENSE).

## MME Ray demo 
```bash
git clone https://github.com/antonpp/kubernetes-engine-samples.git
cd kubernetes-engine-samples/ai-ml/gke-ray/rayserve/llm
export TUTORIAL_HOME=`pwd`

gcloud config set project <edit>
export PROJECT_ID=$(gcloud config get project)
export COMPUTE_REGION=europe-west1
export CLUSTER_VERSION=1.30.5
export HF_TOKEN=<edit>

kubectl create secret generic hf-secret \
  --from-literal=hf_api_token=${HF_TOKEN} \
  --dry-run=client -o yaml | kubectl apply -f -

gcloud container clusters create std-rayserve-cluster \
    --enable-ray-operator --enable-ray-cluster-logging --enable-ray-cluster-monitoring \
    --cluster-version=${CLUSTER_VERSION} \
    --machine-type=g2-standard-24 \
    --location=${COMPUTE_REGION} \
    --node-locations=${COMPUTE_REGION}-c \
    --num-nodes=1 \
    --accelerator type=nvidia-l4,count=2,gpu-driver-version=latest

kubectl apply -f mme-ray-service.yaml

kubectl port-forward svc/gemma-2b-it-serve-svc 8000:8000

curl -X POST http://localhost:8000/ -H "Content-Type: application/json" --header "serve_multiplexed_model_id: bar" -d '{"prompt": "What are the top 5 most popular programming languages? Please be brief.", "max_tokens": 200}'

```