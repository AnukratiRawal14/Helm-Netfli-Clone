# Helm-Netflix-Clone
Step 1 : Create cloudformation stack to create vpc and  other resources <br>
         Use s3 url : https://amazon-eks.s3.us-west-2.amazonaws.com/cloudformation/2020-04-21/amazon-eks-vpc-private-subnets.yaml  <br>

Step 2 : Create IAM role <li> helm-eks-cluster-role </li> <li> helm-eks-worker-node-role -> AmazonEC2ContainerRegistryReadOnly, AmazonEKS_CNI_Policy, AmazonEKSWorkerNodePolicy </li>

Step 3: Update the config <pre>aws eks update-kubeconfig --region ap-south-1 --name CLUSTER_NAME --profile PROFILE_NAME </pre>

Step 4: Create namespace or else work default <pre> kubectl create ns helm </pre>

Step 5: Meanwhile, create helm chart <br>
 <pre>helm create netflix-clone</pre>

Step 6: Customize chart & values as per our requirement

Step 7: Package the chart 
<pre> helm package netflix-clone </pre>

Step 8: Once clutser and nodegroup get creates, install package helm chart
<pre> Install clone application - helm install my-netflix-clone ./netflix-clone-0.1.0.tgz</pre>

Step 9 : To check <pre> kubeclt get pods -n helm </pre>

Step 12: To verify the deployment, check command
<pre> kubectl get svc </pre>

 <h4> Set up Prometheus</h4>
<li> kubectl create namespace monitoring </li>
<li> kubectl config set-context --current --namespace=monitoring</li>
<li> helm repo add prometheus-community https://prometheus-community.github.io/helm-charts</li>
<li> helm repo update</li>
<li> helm install stable prometheus-community/kube-prometheus-stack</li>
<li> kubectl get pods -l "release=stable"</li>
<li> kubectl edit svc stable-kube-prometheus-sta-prometheus >>>> Change type to LoadBalancer from ClusterIP</li>
<li> kubectl get svc</li>
         
<h4> Set up Grafana </h4>
<li>helm repo add grafana https://grafana.github.io/helm-charts</li>
<li>helm repo update</li>
<li>helm install grafana grafana/grafana</li>
<li>kubectl expose service grafana --type=NodePort --target-port=3000 --name=grafana-ext</li>
<li>kubectl edit svc stable-grafana</li>
<li>kubectl get secret grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo</li>
<li>If grafana is not accepting the password in the console, do this:</li>
<li>kubectl exec --stdin --tty <stable-grafana-POD-NAME> -- /bin/bash</li>
<li>grafana-cli admin reset-admin-password <NEW-PASSWORD></li>
         
Login to your grafana console

<li>Navigate to Connections >> Add connections >> Search for 'Prometheus' >> Add New Data Sources</li>
<li>Provide Name, Server URL(LoadBalancer of Prometheus Server URL) and Click on 'save & test'</li>
<li>Navigate to Home >> Dashboards >> New Dashboard >> Import Dashboard and provide the ID of the the desired dashboard from https://grafana.com/grafana/dashboards/</li>
