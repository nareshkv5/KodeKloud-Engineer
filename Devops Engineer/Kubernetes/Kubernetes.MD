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
