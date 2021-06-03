# secret-replicator-eks
replicates docker registry kind secrets across single cluster and multiple clusters

Problem Statement - Some secrets like docker registry needs to be in every name space before a defployemnt should happens and the secret cannot be stored in version control . 

Scope - 
1) Automatically Create secret as soon as a namespace is created in a EKS cluster.
2) When the secret chnages due to any compliance reason , it should automatically get updated in all namespaces 
3) If you have multiple EKS clusters , to expand the above two to entire EKS cluster 


Design - 







Details - 

1) Create a EKS cluster which has cross connectivity to all other EKS clusters . Using https://aws.amazon.com/blogs/containers/enabling-cross-account-access-to-amazon-eks-cluster-resources/ . Call it a mgmt cluster.
2) Create Operators using the kopfs framework , which will replicate secrets which have annotation "mw-sync: yes" defined in the configuration . Two operator are written which perform specific function.  
      a) Agent Operator- Run in client EKS cluster in namespace "operator" . Monitor the secret create/update event along with any new namespace creation event. As soon as any of 
         event occurs , the operator checks for the annotation and replicates the secret to all namespaces
      b) Master Operator- It run only in the MGMT EKS cluster in the "operator" namespace. Monitors the secret create/update event in operator namespace. If a secret is created            with the  required annotation. It will start pushing the secret to client EKS cluster under operator namespace. Once that update is done, the agent operator picks up the          change and starts replicating it 
  


