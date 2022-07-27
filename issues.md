# kops-k8s-aws

What if you mistakenly delete the Client instance from where you have configured the kops cluster in step 1 -

1. Launch a new EC2 instance, install the kops & kubectl client in new EC2 instance.

2. export the S3 and kops-cluster name in .bashrc and source it.
    This will help new instance to pick up the config and state of cluster from data stored in S3.
    
3. follow the following set of commands now -

a. Get your instances group
```
kops get ig
```

b. edit your master and slave nodes
```
kops edit ig <master-name>
```
Change maxsize and minsize to 0
```
minsize: 0
maxSize: 0
```

c. Update your cluster
```
kops update cluster --yes
```
Rolling changes to update the cluster

```
kops rolling-update cluster --cloudonly --force --yes
```
