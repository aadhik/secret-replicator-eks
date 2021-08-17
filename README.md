# secret-replicator-eks
replicates docker registry kind secrets across single cluster and multiple clusters

Problem Statement - Some secrets like docker registry needs to be in every name space before a defployemnt should happens and the secret cannot be stored in version control . 
This problem multifold when you have multiple k8s cluster. Organization tends to have a single repository for images.
Any time a namespace is created the secret has to be manually created for deployments to happen. 
Or if the credentials expire for the id that connects to repository then the update has to happen manually across all cluster in every namespace.


Solution - 
1)	Stand up a EKS cluster that has access to all other k8s cluster. Let’s call this as mgmt. cluster
2)	Create a namepace operator, run Master operator in the mgmt. cluster in operator namespace
3)	In all other k8s cluster create a namespace operator and run the agent operator 
4)	Master operator checks mgmt. cluster for all secrets and config maps which have an annotation 
      “mw_sec/sync: true”, in the operator namespace 
5)	If the annotation is found it starts pushing the object to all other k8s cluster in operator namespace of the destination cluster
6)	Once the object is created in destination cluster, the agent operator finds the same annotation and starts pushing the object to all of its namespaces



Design - 








Details - 

1) Create a EKS cluster which has cross connectivity to all other EKS clusters . Using https://aws.amazon.com/blogs/containers/enabling-cross-account-access-to-amazon-eks-cluster-resources/ . Call it a mgmt cluster.
2) Create Operators using the kopfs framework , which will replicate secrets which have annotation "mw-sync: yes" defined in the configuration . Two operator are written which perform specific function.  
      a) Agent Operator- Run in client EKS cluster in namespace "operator" . Monitor the secret create/update event along with any new namespace creation event. As soon as any of 
         event occurs , the operator checks for the annotation and replicates the secret to all namespaces
      b) Master Operator- It run only in the MGMT EKS cluster in the "operator" namespace. Monitors the secret create/update event in operator namespace. If a secret is created            with the  required annotation. It will start pushing the secret to client EKS cluster under operator namespace. Once that update is done, the agent operator picks up the          change and starts replicating it 
  


