kind: Pod                              
apiVersion: v1                     
metadata:                           
  name: testpod                  
spec:                                    
  containers:                      
    - name: c00                     
      image: ubuntu              
      command: ["/bin/bash", "-c", "while true; do echo Hello-amiya; sleep 5 ; done"]
  restartPolicy: Never         # Defaults to Always






kind: Pod                              
apiVersion: v1                     
metadata:                           
  name: testpod
  annotations: 	
      description: learning pod                 
spec:                                    
  containers:                      
    - name: c00                     
      image: ubuntu              
      command: ["/bin/bash", "-c", "while true; do echo Hello-amiya; sleep 5 ; done"]
  restartPolicy: Never         # Defaults to Always

Multi container pod
kind: Pod
apiVersion: v1
metadata:
  name: testpod3
spec:
  containers:
    - name: c00
      image: ubuntu
      command: ["/bin/bash", "-c", "while true; do echo Welcome to k8s; sleep 5 ; done"]
    - name: c01
      image: ubuntu
      command: ["/bin/bash", "-c", "while true; do echo Welcome to docker; sleep 5 ; done"]

Environment variable:
POD ENVIRONMENT VARIABLES
kind: Pod
apiVersion: v1
metadata:
  name: environments
spec:
  containers:
    - name: c00
      image: ubuntu
      command: ["/bin/bash", "-c", "while true; do echo Hello-AMIYA; sleep 5 ; done"]
      env:                        # List of environment variables to be used inside the pod
      - name: MYNAME
        value: AMIYA


POD WITH PORTS
kind: Pod
apiVersion: v1
metadata:
  name: testpod4
spec:
  containers:
    - name: c00
      image: httpd
      ports:
       - containerPort: 80
       
Pod with label:
kind: Pod
apiVersion: v1
metadata:
  name: testpod
  labels:                                                   
    env: development
    class: pods
spec:
    containers:
       - name: c00
         image: ubuntu
         command: ["/bin/bash", "-c", "while true; do echo welcome to k8s; sleep 5 ; done"]
         
NODE SELECTOR EXAMPLE
---------------------
kind: Pod
apiVersion: v1
metadata:
  name: nodelabels
  labels:
    env: development
spec:
    containers:
       - name: c00
         image: ubuntu
         command: ["/bin/bash", "-c", "while true; do echo welcome to k8s; sleep 5 ; done"]
    nodeSelector:                                         
       hardware: t2-micro

nodename example
---------------- 
kind: Pod
apiVersion: v1
metadata:
  name: nodelabels
  labels:
    env: development
spec:
    containers:
       - name: c00
         image: ubuntu
         command: ["/bin/bash", "-c", "while true; do echo welcome to k8s; sleep 5 ; done"]
    nodeName: controlplane

Node affinity
----------------

requiredDuringSchedulingIgnoredDuringExecution
----------------------------------------------
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - { key: disktype , operator: In , values: [ ssd ] }            
  containers:
  - name: nginx
    image: nginx
    imagePullPolicy: IfNotPresent

preferredDuringSchedulingIgnoredDuringExecution
-----------------------------------------------
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  affinity:
    nodeAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1
        preference:
          matchExpressions:
           - { key: disktype ,  operator: In ,  values: [ ssd ] }          
  containers:
  - name: nginx
    image: nginx
    imagePullPolicy: IfNotPresent

ReplicationController
---------------------
kind: ReplicationController               
apiVersion: v1
metadata:
  name: myreplica
spec:
  replicas: 2            
  selector:        
    myname: amiya                             
  template:                
    metadata:
      name: testpod
      labels:            
        myname: amiya
    spec:
     containers:
       - name: c00
         image: ubuntu
         command: ["/bin/bash", "-c", "while true; do echo welcome to k8s ; sleep 5 ; done"]
	 
Replica Set
-----------
kind: ReplicaSet               
apiVersion: apps/v1
metadata:
  name: myreplica
spec:
  replicas: 2            
  selector:
    matchLabels:   
     myname: amiya                             
  template:                
    metadata:
      name: testpod
      labels:            
        myname: amiya
    spec:
     containers:
       - name: c00
         image: ubuntu
         command: ["/bin/bash", "-c", "while true; do echo welcome to k8s ; sleep 5 ; done"]

One more Example:
-----------------
kind: ReplicaSet                                    
apiVersion: apps/v1                            
metadata:
  name: myrs
spec:
  replicas: 2  
  selector:                  
    matchExpressions:        # these must match the labels
      - {key: myname, operator: In, values: [kuber, kubernetes, k8s]}
      - {key: env, operator: NotIn, values: [production]}
  template:      
    metadata:
      name: testpod7
      labels:              
        myname: k8s
    spec:
     containers:
       - name: c00
         image: ubuntu
         command: ["/bin/bash", "-c", "while true; do echo welcome to cloud; sleep 5 ; done"]
	 
	 
Deployment1 ( Equality based)
-----------
kind: Deployment
apiVersion: apps/v1
metadata:
   name: mydeployments
spec:
   replicas: 2
   selector:     
    matchLabels:
     name: deployment
   template:
     metadata:
       name: testpod
       labels:
         name: deployment
     spec:
      containers:
        - name: c00
          image: ubuntu
          command: ["/bin/bash", "-c", "while true; do echo welcome to k8s; sleep 5; done"]

Deployment ( Set Based)
----------------------
kind: Deployment
apiVersion: apps/v1
metadata:
   name: mydeployments
spec:
   replicas: 2
   selector:     
    matchExpressions:
     - {key: name, operator: In, values: [deployment, depl, deploy]}
   template:
     metadata:
       name: testpod
       labels:
         name: deployment
     spec:
      containers:
        - name: c00
          image: ubuntu
          command: ["/bin/bash", "-c", "while true; do echo welcome to k8s; sleep 5; done"]


Deployment (Roll back)
-------------
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
Deployment Strategy:
--------------------
Recreate:
----------
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 10
  strategy:
   type: Recreate
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.22
        ports:
        - containerPort: 80

Rolling Upate
-------------
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 10
  strategy:
   type: RollingUpdate
   rollingUpdate:
    maxSurge: 2        # how many pods we can add at a time
    maxUnavailable: 0 # how many pods can be unavailable during the rolling update
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.22
        ports:
        - containerPort: 80

Canary (main)
-------
kind: Deployment
apiVersion: apps/v1
metadata:
   name: depl
spec:
   replicas: 4
   selector:     
    matchLabels:
     name: k8s
   template:
     metadata:
       name: testpod
       labels:
         name: k8s
         track: stable
     spec:
      containers:
        - name: c00
          image: centos
          command: ["/bin/bash", "-c", "while true; do echo welcome to k8s-stable; sleep 5; done"]
Canary (canary)
---------------
kind: Deployment
apiVersion: apps/v1
metadata:
   name: depl-canary
spec:
   replicas: 1
   selector:     
    matchLabels:
     name: k8s
   template:
     metadata:
       name: testpod
       labels:
         name: k8s
         track: canary
     spec:
      containers:
        - name: c00
          image: ubuntu
          command: ["/bin/bash", "-c", "while true; do echo welcome to k8s-canary; sleep 5; done"]

