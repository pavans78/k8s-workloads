# Deployment:

In k8s, A Deployment provides declarative updates for Pods and ReplicaSets. You describe a desired state in a Deployment, and the Deployment Controller changes the actual state to the desired state at a controlled rate. You can define Deployments to create new ReplicaSets, or to remove existing Deployments and adopt all their resources with new Deployments.

The Deployment object not only creates the pods but also ensures the correct number of pods is always running in the cluster, handles scalability, and takes care of updates to the pods on an ongoing basis. All these activities can be configured through fields in the Deployment YAML. 

## Use Cases:
The following are typical use cases for Deployments in a Kubernetes are :

1. We can create a Deployment to rollout a ReplicaSet. The ReplicaSet creates Pods within the background. We can check the status of the current deployment rollout was completed or not.
2. Declare the new state of the Pods by changing the PodTemplateSpec of the deployment. A brand new ReplicaSet is made and also the Deployment manages to move the Pods from the previous ReplicaSet to the new one at a controlled rate.
3. Rollback to an earlier Deployment revision if the present state of the deployment isn’t stable. every rollback updates the revision of the deployment in the K8s cluster.
4. Scale up the Deployment to facilitate more load. we can scale up the deployment during hours where we see huge traffic and network in/outs
5. We can Pause the Deployment to update/apply fixes and bugs to its pod spec and then can start a new rollout for changes to reflect.

### Important points in this configuration:

`spec.replicas` — specifies how many pods to run

`strategy.type` — specifies which deployment strategy should be used. The strategy types can be `RollingUpdate` and `Recreate`.

   `RollingUpdate` - You can specify maxUnavailable and maxSurge to control the rolling update process.
   `Recreate` - All existing Pods are killed before new ones are created.
        
`spec.template.spec.containers` — specifies which container image to run in each of the pods and ports to expose.

`spec.selector` - specifies a label selector for the Pods targeted by this Deployment. `.spec.selector` must match `.spec.template.metadata.labels`, or it will be rejected by the API.

```
spec:
  progressDeadineSeconds: 600
  minReadySeconds: 5
  revisionHistoryLimit: 5
  replicas: 5
  selector:
    matchLabels:
    app: run
  
```

`progressDeadlineSeconds`(optional field) — specifies the number of seconds you want to wait for your Deployment to progress before the system reports back that the Deployment has failed progressing.

`minReadySeconds`(optional field) — specifies the minimum number of seconds for which a newly created Pod should be ready without any of its containers crashing, for it to be considered available.

`revisionHistoryLimit`(optional field) - that specifies the number of old ReplicaSets to retain to allow rollback. A Deployment's revision history is stored in the ReplicaSets it controls.

## RollingUpdate

```
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 30%
      maxSurge: 30%
```


`maxUnavailable` is an optional field that specifies the maximum number of Pods that can be unavailable during the update process. 

Deployment ensures that only a certain number of Pods are down while they are being updated. By default, it ensures that at least 70% of the desired number of Pods are up (30% max unavailable).

For example: If there are 10 replicas in the deployment, during update the deployment ensures that at least 7 are available and 3 pods will be unavailable.

`maxSurge` is an optional field that specifies the maximum number of Pods that can be created over the desired number of Pods.

Deployment also ensures that only a certain number of Pods are created above the desired number of Pods. By default, it ensures that at most 130% of the desired number of Pods are up (30% max surge).

For example: If there are 10 replicas in the deployment, during update the deployment will ensures that the total number of old and new Pods does not exceed 13 of desired Pods. Once old Pods have been killed, the new ReplicaSet can be scaled up further. 
Here during update, 13 of desired pods it has 7 in ready state, 3 will be terminating the old pods and 3 will be creating the new pods. 

If you look at the above Deployment closely, you will see that it first created a new Pod, then deleted some old Pods, and created new ones. It does not kill old Pods until a sufficient number of new Pods have come up and does not create new Pods until a sufficient number of old Pods have been killed. It makes sure that at least 7 Pods are available and that at max 13 Pods in total are available.


## Scaling a Deployment

You can scale a Deployment by using the following command:

`   kubectl scale deployments/deploy_name--replicas=10   `

## Updating a Deployment

A Deployment's rollout is triggered if and only if the Deployment's Pod template (that is, .spec.template) is changed, for example if the labels or container images of the template are updated. Other updates, such as scaling the Deployment, do not trigger a rollout.

You can change the image in the Deployment by using the following command:

`  kubectl set image deployments\deploy_name nginx=nginx:1.25    `

### Rollout a Deployment

At times when the deployment is not stable or we see any bugs what were not supposed to be there, such as crash looping, So by default the rollout history is stored in the system so that we can rollback the version anytime we want to deploy.

First, check the revisions of this Deployment using kubectl:

`   kubectl rollout history deployments deploy_name    `

To undo the rollout, you can use this kubectl command,

`  kubectl rollout undo deployments deploy_name      `

for specific revision

`  kubectl rollout undo deployments deploy_name --to-revision=<specific version>    `

To check the status of the Deployment,

`  kubectl rollout status deployments deploy_name     `

For more information on the deployment, please refer this [link](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)





