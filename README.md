# dynatrace_installation_on_eks

Set up monitoring via Dynatrace Operator - automatic mode

1. In the Dynatrace menu, go to Kubernetes.

2. Select Connect automatically via Dynatrace Operator.

3. Enter the following details.

Name: Defines the display name of your Kubernetes cluster. Additionally, this name is used as a prefix for naming Dynatrace-specific resources inside the Kubernetes cluster, such as DynaKube (custom resource), ActiveGate (pod), and OneAgents (pods), and as a name for the secret holding your tokens.

optional Group: Defines a group that is used by various Dynatrace settings, including network zone, ActiveGate group, and host group. If not set, defaults or empty values are used.

Dynatrace Operator token: Select Create token to generate the token and the necessary permissions automatically.

optional Data ingest token: For cloudNativeFullStack and applicationMonitoring deployments, if you want to enable metadata metric enrichment, select Create token.

![image](https://user-images.githubusercontent.com/74225291/219260788-73edba9a-eaf9-4712-8a31-eb6cfeea77c4.png)

Execute the following commands in your terminal:

    kubectl create namespace dynatrace
    kubectl apply -f https://github.com/Dynatrace/dynatrace-operator/releases/download/v0.10.2/kubernetes.yaml
    kubectl -n dynatrace wait pod --for=condition=ready --selector=app.kubernetes.io/name=dynatrace-operator,app.kubernetes.io/component=webhook --timeout=300s
    kubectl apply -f dynakube.yaml
    
![image](https://user-images.githubusercontent.com/74225291/219261056-cd7c3e9f-bf29-42a6-a7d7-52dfe393427d.png)

Now click on show deployment status.

You can verify the pods status on the K8s cluster using below :

    [root@ip-172-31-49-70 opt]# k get pods -n dynatrace
    NAME                                  READY   STATUS    RESTARTS   AGE
    dynatrace-operator-7c6f5bb874-79pml   1/1     Running   0          18m
    dynatrace-webhook-85c6db59ff-4pzbj    1/1     Running   0          18m
    dynatrace-webhook-85c6db59ff-ghrzz    1/1     Running   0          18m
    myeks-activegate-0                    0/1     Running   0          108s
    myeks-oneagent-fl6gc                  1/1     Running   0          107s
    myeks-oneagent-tkwt7                  1/1     Running   0          107s

    [root@ip-172-31-49-70 opt]# k get nodes
    NAME                             STATUS   ROLES    AGE   VERSION
    ip-192-168-20-129.ec2.internal   Ready    <none>   28m   v1.21.14-eks-49d8fe8
    ip-192-168-50-122.ec2.internal   Ready    <none>   28m   v1.21.14-eks-49d8fe8

I am using the 2 nodes cluster.

![image](https://user-images.githubusercontent.com/74225291/219261486-fb95d206-6564-4ec4-9836-0a26be9526a4.png)

For this two nodes host monitoring is automatically enabled now.

![image](https://user-images.githubusercontent.com/74225291/219261845-d2da2900-be3a-428b-8a1b-122735fb7690.png)

now you can in kubernetes cluster is added to DT UI. You can monitor everything on it.

![image](https://user-images.githubusercontent.com/74225291/219262009-185a910a-806e-4e2c-b18c-98c5d28f13ad.png)

Now we can deploy one sample springboot app on EKS cluster and verify its monitoring on DT.

Clone the github repo:

    git clone https://github.com/cloudtechmasters/hellospringboot-cicdWithHelm.git
    
Deploy the springboot helm chart:

    helm upgrade --install springboot chart-eks-springboot --set image.tag=latest

    # k get all
    NAME                              READY   STATUS    RESTARTS   AGE
    pod/springboot-66f8f66878-z7vjp   1/1     Running   0          2m58s

    NAME                 TYPE           CLUSTER-IP     EXTERNAL-IP                                                               PORT(S)          AGE
    service/kubernetes   ClusterIP      10.100.0.1     <none>                                                                    443/TCP          49m
    service/springboot   LoadBalancer   10.100.202.9   a99c2e9cbfd954451b7001eec300f231-1810103440.us-east-1.elb.amazonaws.com   8080:30392/TCP   4m52s

    NAME                         READY   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/springboot   1/1     1            1           4m52s

    NAME                                    DESIRED   CURRENT   READY   AGE
    replicaset.apps/springboot-66f8f66878   1         1         1       2m58s


![image](https://user-images.githubusercontent.com/74225291/219263152-8c282f82-ce43-49b5-9888-652ae5f2ae08.png)

In Default namespace it added the springboot deployment.

![image](https://user-images.githubusercontent.com/74225291/219263460-44327263-feb0-4e62-aeb1-4cd66ce86724.png)

To generate load use the curl command

![image](https://user-images.githubusercontent.com/74225291/219264051-3689a823-2bd5-4d0f-b55a-ff46f02d07b7.png)

You can get more detailed info about service by clinking on services --> HelmController

![image](https://user-images.githubusercontent.com/74225291/219264301-93c0c847-57e9-4972-9918-6a5c74b95cda.png)


![image](https://user-images.githubusercontent.com/74225291/219264456-ab8866bf-c37f-47be-a6f7-74af55fb4f2a.png)

