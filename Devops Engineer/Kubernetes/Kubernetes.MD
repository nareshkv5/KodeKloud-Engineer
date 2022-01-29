--------------------------------------------------------------------------------------------------------------------------------------------------------------
Kubernetes Time Check Pod 21/Jan/2022
--------------------------------------------------------------------------------------------------------------------------------------------------------------

The Nautilus DevOps team want to create a time check pod in a particular Kubernetes namespace and record the logs. 
This might be initially used only for testing purposes, but later can be implemented in an existing cluster. 
Please find more details below about the task and perform it.

Create a pod called time-check in the datacenter namespace. This pod should run a container called time-check, container should use the busybox 
image with latest tag only and remember to mention tag i.e busybox:latest.

Create a config map called time-config with the data TIME_FREQ=9 in the same namespace.

The time-check container should run the command: while true; do date; sleep $TIME_FREQ;done and should write the result to 
the location /opt/security/time/time-check.log. Remember you will also need to add an environmental variable TIME_FREQ in the container, 
which should pick value from the config map TIME_FREQ key.

Create a volume log-volume and mount the same on /opt/security/time within the container.

Note: The kubectl utility on jump_host has been configured to work with the kubernetes cluster.

kubectl get namespace -- to check the existing namespaces

kubectl create namespace nautilus -- Create namespace nautilus as requested

Create a yaml file and copy the below manifest file 

vi time.yaml -- pod name should be time-check/name-space: nautilus/image: busybox-latest

apiVersion: v1
kind: ConfigMap
metadata:
  name: time-config
  namespace: nautilus
data:
  TIME_FREQ: "11"
---
apiVersion: v1
kind: Pod
metadata:
  name: time-check
  namespace: nautilus
  labels:
    app: time-check
spec:
  volumes:
    - name: log-volume
      emptyDir: {}
  containers:
    - name: time-check
      image: busybox:latest
      volumeMounts:
        - mountPath: /opt/security/time
          name: log-volume
      envFrom:
        - configMapRef:
            name: time-config
      command: ["/bin/sh", "-c"]
      args:
        [
          "while true; do date; sleep $TIME_FREQ;done > /opt/security/time/time-check.log",
        ]


kubectl create -f /tmp/time.yaml --- to execute the manifest file and create pods

kubectl get po -n nautilus -- to check whether the pod is running or not

kubectl exec time-check --namespace=nautilus -- cat /opt/security/time/time-check.log 
-- check the time-check.log file to see whether the timecheck is running with a gap of 11 seconds.

---------------------------------------------------------------------------------------------------------------------------------------------
24/Jan/2022 -- Create Namespaces in Kubernetes Cluster
---------------------------------------------------------------------------------------------------------------------------------------------

The Nautilus DevOps team is planning to deploy some micro services on Kubernetes platform. The team has already set up a Kubernetes cluster and now they want set up some namespaces, deployments etc. Based on the current requirements, the team has shared some details as below:

Create a namespace named dev and create a POD under it; name the pod dev-nginx-pod and use nginx image with latest tag only and remember to mention tag i.e nginx:latest.

Note: The kubectl utility on jump_host has been configured to work with the kubernetes cluster.


kubectl get namespace --- to view the existing namespace

kubectl create namespace dev -- to create a dev namespace as per the task

-- creating a pod with image (nginx:latest) nginx and tag as latest under namespace dev

kubectl run dev-nginx-pod --namespace=dev --image=nginx:latest (or) kubectl run dev-nginx-pod --image=nginx:latest -n dev

To check which pods are running under any namespaces

kubectl get pods -n dev 

kubectl describe po dev-nginx-pod -n dev -- to view the complete details of pod verify the image and podname and finish the task