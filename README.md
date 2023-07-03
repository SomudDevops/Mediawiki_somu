## Deploying MediaWiki on Kubernetes (K8s) using Helm charts

## Procedure to deploy MediaWiki application on GCP K8s Cluster (GKE) with Helm Charts

### Create a shell script to Install required tools to start in Ubuntu.

```
#!/bin/bash

# Install Python 3 and pip
apt-get update
apt-get install -y python3 python3-pip

# Upgrade pip to the latest version
pip3 install --upgrade pip

# Install Ansible
pip3 install ansible
```
Save the script to a file, for example, install_ansible.sh. Make the file executable using the following command:

```
chmod +x install_ansible.sh
```
Then you can run the script using the following command:

```
./install_ansible.sh
```
After Installing Ansible and required tools to start, follow below steps:

Install Docker Desktop locally and enable Kubernetes service in settings. You can use the same K8s cluster for this task.

Set up the Dockerfile:
  - Create a new directory for your MediaWiki application.
  - Inside the directory, create a file named Dockerfile.
  - Add the following content to your Dockerfile:

```
FROM mediawiki:1.35

# Add any additional dependencies or configurations here

# Set the entrypoint command
ENTRYPOINT ["php", "-S", "0.0.0.0:8080", "-t", "/var/www/html"]
```

Build and push the Docker image:
  - Run the following command in the same directory as your Dockerfile:

```
docker build -t mymediawiki .
```
  - After the image is built, push it to a container registry of your choice.

#### Install Helm: 
If you haven't already, install Helm on your local machine by following the official Helm documentation: https://helm.sh/docs/intro/install/

Set up the Helm chart:
  - Create a new directory for your Helm chart.
  - Inside the directory, run the following command to initialize the Helm chart structure:

    ```
    helm create mediawiki
    ```

Configure the Helm chart:
  - Open the values.yaml file inside the mediawiki directory.
  - Modify the image section under deployment to use your Docker image. Update the repository and tag as appropriate:

    ```
    image:
      repository: your-image-repository
    tag: your-image-tag
      pullPolicy: IfNotPresent
    ```

Add the Helm chart repository: Add the Bitnami Helm chart repository, which provides the MediaWiki Helm chart. Run the following command to add the repository:

    ```
    helm repo add bitnami https://charts.bitnami.com/bitnami
    helm repo update
    ```
Customize the values: 
  - Create a values.yaml file to customize the configuration options for the MediaWiki deployment. You can use the default values provided by the Helm chart or override them as needed. Here's an example of a basic values.yaml file:

    ```
    replicaCount: 2
    fullnameOverride: my-wiki
    wikiName: My Wiki
    ```
You can customize other parameters such as image tags, persistence settings, ingress rules, etc., according to your requirements. Refer to the Helm chart documentation for all available options.

Deploy MediaWiki: 
  - Run the following command to deploy MediaWiki using Helm:

    ```
    helm install my-wiki bitnami/mediawiki -f values.yaml
    ```
Replace my-wiki with the desired release name for your MediaWiki installation. The -f flag specifies the path to your custom values.yaml file.

Verify the deployment: 
  - Use the following command to check the status of your deployment:

    ```
    kubectl get pods
    ```
    
You should see the MediaWiki pods running and ready.

Ensure you have an Ingress controller running in your Kubernetes cluster. Popular choices include Nginx Ingress Controller, Traefik, or HAProxy Ingress.
Update your values.yaml file or create a new one with the following values to enable Ingress:

    ```
    ingress:
      enabled: true
      annotations:
        kubernetes.io/ingress.class: <ingress-controller-class> # Replace with your Ingress controller class
        # Add any additional Ingress annotations as needed
      hosts:
        - name: my-wiki.example.com # Replace with your desired hostname
          path: /
    ```
    
Adjust the ingress.annotations section to match the annotations required by your Ingress controller. Set the hosts section to your desired hostname or domain.
Upgrade your Helm release to apply the changes:

    ```
    helm upgrade my-wiki bitnami/mediawiki -f values.yaml
    ```
Once the Ingress resource is created and propagated, you can access MediaWiki by navigating to the specified hostname or domain (e.g., http://my-wiki.example.com).

##### Port-Forwarding Tunnel:

If you don't have an Ingress controller available or prefer a simpler setup for local development or debugging purposes, you can use port-forwarding to access MediaWiki directly.
Here are the steps to create a port-forwarding tunnel to the MediaWiki service:

Identify the name of the MediaWiki pod in your cluster by running:

    ```
    kubectl get pods
    ```
Create the port-forwarding tunnel by running the following command, replacing <pod-name> and <local-port>:
    ```
    kubectl port-forward <pod-name> <local-port>:80
    ```
    
<pod-name> should be replaced with the name of the MediaWiki pod, and <local-port> should be the local port number you want to use (e.g., 8080).

Once the port-forwarding tunnel is established, you can access MediaWiki by opening your web browser and navigating to http://localhost:<local-port> (e.g., http://localhost:8080).

Remember that the port-forwarding approach is typically used for local testing or debugging and may not be suitable for production environments.



