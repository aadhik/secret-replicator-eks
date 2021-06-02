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
2) Using kopfs , create two operators 
  


