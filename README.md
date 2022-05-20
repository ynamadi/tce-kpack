## Using kpack package in Tanzu Community Edition (TCE)
We will be going over using kpack to build OCI compliant images via a cluster created in TCE

This page walks you through the following steps:

1. Install Tanzu Community Edition.
2. Create an Unmanaged cluster.
3. Install kpack package.
4. Install kpack-dependencies package.
5. Build an OCI compliant image and Publish the image to your registry.


## Step 1 - Install Tanzu Community Edition via Homebrew

```shell
brew install vmware-tanzu/tanzu/tanzu-community-edition
```

## Step 2 - Create an Unmanaged Cluster in TCE

```shell
tanzu unmanaged-cluster create <CLUSTER_NAME>
```

Once the unmanaged cluster is created, you can now proceed to installing kpack and kpack-dependencies packages on to your cluster. 

## Step 3 - Installing kpack package.

### a. Check available kpack package versions

This is to determine the available kpack package versions available and the version you would like to install.

```shell
tanzu package available list kpack.community.tanzu.vmware.com
```

### b. Check the value's schema for values needed to configure kpack

This is helpful in determining what values are required for the package to reconcile successfully. 

```shell
tanzu package available get kpack.community.tanzu.vmware.com/<VERSION> --values-schema
```

### c. Installing Kpack package

The command below installs kpack package on to your cluster
```shell
tanzu package install kpack --package-name kpack.community.tanzu.vmware.com --version <VERSION> -f kpack/kpack-config-values.yaml
```

## Step 4 - Installing kpack-dependencies package.

### a. Check available kpack package versions

This is to determine the available kpack package versions available and the version you would like to install.

```shell
tanzu package available list kpack-dependencies.community.tanzu.vmware.com
```

### b. Check the value's schema for values needed to configure kpack

This is helpful in determining what values are required for the package to reconcile successfully.

```shell
tanzu package available get kpack-dependencies.community.tanzu.vmware.com/<VERSION> --values-schema
```

### c. Installing Kpack package

The command below installs kpack package on to your cluster
```shell
tanzu package install kpack --package-name kpack-dependencies.community.tanzu.vmware.com --version <VERSION> -f kpack/kpack-config-values.yaml
```

### d. Verify kpack-dependencies are installed on your cluster

```shell
kubectl describe clusterstore -n default
```

```shell
kubectl describe clusterstack -n default
```

```shell
kubectl describe clusterbuilder -n default
```

# Prerequisite Steps

### 1. Create secret
This is required to publish images to your registry

```shell
kubectl create secret docker-registry tutorial-registry-credentials \
--docker-username=<USERNAME> \
--docker-password=<PASSWORD> \
--docker-server=<SERVER>
```

### 2. Create Service Account
This service account is used to reference the registry secret created in prerequisite step 1 using the manifest service-account.yaml file.

# Apply the service account to your cluster
```shell
kubectl apply -f service-account.yaml
```

## Step 5 - Build OCI Image and Publish

The image.yaml manifest file defines your image source, builder and tag needed to build and publish your OCI image.   

### Apply the image to your cluster
```shell
kubectl apply -f image.yaml
```