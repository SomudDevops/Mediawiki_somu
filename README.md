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

Add the database deployment and service:
  - Open the templates directory and create a new file named database.yaml.
  - 


