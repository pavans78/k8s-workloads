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

Important points in this configuration:

`spec.replicas` — specifies how many pods to run

`strategy.type` — specifies which deployment strategy should be used. The strategy types can be `RollingUpdate` and `Recreate`.

        `RollingUpdate` - You can specify maxUnavailable and maxSurge to control the rolling update process.
        `Recreate` - All existing Pods are killed before new ones are created.
        
`spec.template.spec.containers` — specifies which container image to run in each of the pods and ports to expose.

