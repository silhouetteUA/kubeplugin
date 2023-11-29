# kubeplugin
Instruction: how to create your own kubectl plugin, week5 practice1

## How to create your own kubectl plugin?

Let us create a bash script using the following content:

```
#!/bin/bash

# Define command-line arguments

# Retrieve resource usage statistics from Kubernetes
echo "Resource, Namespace, Name, CPU, Memory"
kubectl top $1 -n $2 | tail -n +2 | while read line
do
  # Extract CPU and memory usage from the output
  NAME=$(echo $line | awk '{print $1}')
  CPU=$(echo $line | awk '{print $2}')
  MEMORY=$(echo $line | awk '{print $3}')

  # Output the statistics to the console
  echo "$1, $2, $NAME, $CPU, $MEMORY"
done
```

This script requires you to provide two ARG's during its execution:
`Resource` and `Namespace` value.
For `Resource` only two values can be passed, namely:

node  -- Display resource (CPU/memory) usage of nodes \
pod   -- Display resource (CPU/memory) usage of pods

For `Namespace` any value can be provided, like - default, kube-system, etc.

This script will provide statistics using the following format: `"Resource, Namespace, Name, CPU, Memory"`

Before script execution, please don't forget to add `execute` permission to it using `chmod +x`

Now let us try to execute the script:

`devops_course/week5/plugin/scripts>main> ./kubeplugin pod kube-system`

````
Resource, Namespace, Name, CPU, Memory
pod, kube-system, coredns-5d78c9869d-nh7vq, 4m, 40Mi
pod, kube-system, etcd-minikube, 35m, 145Mi
pod, kube-system, kube-apiserver-minikube, 71m, 535Mi
pod, kube-system, kube-controller-manager-minikube, 23m, 87Mi
pod, kube-system, kube-proxy-47hwj, 1m, 38Mi
pod, kube-system, kube-scheduler-minikube, 6m, 41Mi
pod, kube-system, metrics-server-7746886d4f-qvrsq, 5m, 18Mi
pod, kube-system, storage-provisioner, 6m, 25Mi
````

Now, let us follow https://kubernetes.io/docs/tasks/extend-kubectl/kubectl-plugins/ in order to create our first kubectl plugin.

Let us rename our script:

`mv kubeplugin kubectl-kubeplugin`

Now we need place it anywhere in  `PATH`, like `/usr/local/bin/kubectl-kubeplugin`

Now let us check our plugin in the list:

````
devops_course/week5/plugin/scripts>main>  kubectl plugin list
/usr/local/bin/kubectl-foo
/usr/local/bin/kubectl-kubeplugin
````

Now let us execute the script properly, using kubectl:

```
 ~/study/devops_course/week5/plugin/scripts>main>  kubectl kubeplugin node kube-system
Resource, Namespace, Name, CPU, Memory
node, kube-system, minikube, 395m, 4%
                                                                                                                                  
 ~/study/devops_course/week5/plugin/scripts>main>  kubectl kubeplugin pod kube-system
Resource, Namespace, Name, CPU, Memory
pod, kube-system, coredns-5d78c9869d-nh7vq, 4m, 38Mi
pod, kube-system, etcd-minikube, 34m, 146Mi
pod, kube-system, kube-apiserver-minikube, 68m, 535Mi
pod, kube-system, kube-controller-manager-minikube, 23m, 87Mi
pod, kube-system, kube-proxy-47hwj, 1m, 38Mi
pod, kube-system, kube-scheduler-minikube, 5m, 41Mi
pod, kube-system, metrics-server-7746886d4f-qvrsq, 6m, 19Mi
pod, kube-system, storage-provisioner, 5m, 25Mi
```

**Thanks for following the guide**
