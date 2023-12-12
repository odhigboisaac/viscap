## Integrate build server with Kubernetes cluster 

1. Setup kubectl from open source  Or Install kubectl locally using the command.
  
   ```sh 
     az aks install-cli
     chmod +x ./kubectl
     mv ./kubectl /usr/local/bin
     kubectl version
   ``` 

2. Make sure you have installed azurecli latest version. 


3. Configure azurecli to connect with azure account  
    ```sh 
     az configure
     Provide signin credentials.
    ```

4. Download Kubernetes credentials and cluster configuration (.kube/config file) from the cluster  

   ```sh 
    az aks get-credentials --resource-group <resource-group-name> --name <cluster-name>
   ```
   Replace the resource-group and cluster-name with yours.This command downloads the credentials and configures the Kubernetes CLI to use them.
   
5. Verify the connection to your cluster using the command kubectl get nodes.
   ```
   kubectl get nodes
   ```
