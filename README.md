## Create a File Shares 'otelcol-data' in an existing Azure Storage Account or on a new Azure Storage Account
1. Create file share 'otelcol-queue'. This is required for storing,
- Checkpoint offsets from eventhub - reference https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/receiver/azureeventhubreceiver
- To be exported data from 'elasticsearchexporter'. 
2. Create file share 'elasticapm-data'. This is required for storing,
Elastic APM aggregation state - reference https://github.com/elastic/opentelemetry-collector-components/tree/main/connector/elasticapmconnector

## Create an Eventhub for Azure Resource Logs & Activity Logs
Enable diagnostic settings on the required resources, with destination as eventhub

## Create an User-Assigned Managed Identity and assign "Monitoring Reader" RBAC role
Create Federated Credential with subject: --subject: "system:serviceaccount:o11y-otel:opentelemetry-kube-stack-cluster-azure-collector"

## Create a Kubernetes Secret with Storage Account credentials

## Create Persistent Volumes 
We need to create Persistent Volumes manually because,
If we use dynamic provisioning using 'azurefile' StorageClass via PVC, it tries to dynamically provision a new storage account with public network access & fails,
due to the restriction set in our Azure Policy. We need to use 'azurefile' storage only as we need 'ReadWriteMany' option.

## Create a Kubernetes Secret to store the following data
Elastic Cloud API key
Elasticsearch endpoint
Data Stream Namespace
Eventhub endpoint
Eventhub Shared Access Key Name
Eventhub Shared Access Key
Azure Resource Group (if we need to get metrics from resources in a specific resource group)
Azure Client ID of User-Assigned Managed Identity
Azure Tenant ID

## Create a Kubernetes Service Account
We need to create this Service Account manually, as the helm chart doesn't have any option to annotate a service account.

## Install Helm chart - to deploy set of Collectors/Agents
helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts
kubectl create namespace o11y-otel
helm upgrade --install opentelemetry-kube-stack open-telemetry/opentelemetry-kube-stack -n o11y-otel --values values.yml --version '0.12.4'

