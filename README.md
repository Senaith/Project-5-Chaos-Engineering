# Project-5-Chaos-Engineering
## Chaos Engineering with LitmusChaos on Amazon EKS

## What is Chaos Engineering?

![1_yoBx98rGeUy9UuDnFO4vfw](https://user-images.githubusercontent.com/91766546/158917498-3e1939be-e287-4c9d-8c75-1971e367d0f0.png)

Chaos Engineering is the discipline of experimenting on a system in order to build confidence in the system’s capability to withstand turbulent conditions in production. Advances in large-scale, distributed software systems are changing the game for software engineering. As an industry, we are quick to adopt practices that increase flexibility of development and velocity of deployment. An urgent question follows on the heels of these benefits: How much confidence we can have in the complex systems that we put into production?

We need to identify weaknesses before they manifest in system-wide, aberrant behaviors. Systemic weaknesses could take the form of: improper fallback settings when a service is unavailable; retry storms from improperly tuned timeouts; outages when a downstream dependency receives too much traffic; cascading failures when a single point of failure crashes; etc. We must address the most significant weaknesses proactively, before they affect our customers in production. We need a way to manage the chaos inherent in these systems, take advantage of increasing flexibility and velocity, and have confidence in our production deployments despite the complexity that they represent.

An empirical, systems-based approach addresses the chaos in distributed systems at scale and builds confidence in the ability of those systems to withstand realistic conditions. We learn about the behavior of a distributed system by observing it during a controlled experiment. We call this Chaos Engineering.

## Chaos in Practice

To specifically address the uncertainty of distributed systems at scale, Chaos Engineering can be thought of as the facilitation of experiments to uncover systemic weaknesses. These experiments follow four steps:

1. Start by defining ‘steady state’ as some measurable output of a system that indicates normal behavior.
2. Hypothesize that this steady state will continue in both the control group and the experimental group.
3. Introduce variables that reflect real world events like servers that crash, hard drives that malfunction, network connections that are severed, etc.
4. Try to disprove the hypothesis by looking for a difference in steady state between the control group and the experimental group.

The harder it is to disrupt the steady state, the more confidence we have in the behavior of the system. If a weakness is uncovered, we now have a target for improvement before that behavior manifests in the system at large.

## LitmusChaos Architecture

![litmus-marketecture-–-light-version-1](https://user-images.githubusercontent.com/91766546/158955341-fea0802b-ac90-4466-bcb4-f2668ad054c1.png)

[LitmusChaos](https://litmuschaos.io/) is a cloud-native Chaos Engineering framework for Kubernetes. It is built using the [Kubernetes Operator framework](https://sdk.operatorframework.io/). A [Kubernetes Operator](https://kubernetes.io/docs/concepts/extend-kubernetes/operator/) is a software extension to Kubernetes that makes use of [custom resource definitions (CRDs)](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/) to manage applications and their components.

The Litmus Chaos Operator helps reconcile the state of ChaosEngine, a custom resource that holds the chaos intent specified by a developer or DevOps engineer against a particular stateless/stateful Kubernetes deployment. The operator performs specific actions upon creation of the ChaosEngine, its primary resource. The operator also defines a secondary resource (the engine runner pod), which is created and managed by it in order to implement the reconcile functions.

Litmus takes a cloud-native approach to create, manage, and monitor chaos. Chaos is orchestrated using the following Kubernetes CRDs:

- **ChaosEngine**: A resource to link a Kubernetes application or Kubernetes node to a ChaosExperiment. ChaosEngine is watched by the Litmus ChaosOperator, which then invokes ChaosExperiments
- **ChaosExperiment**: A resource to group the configuration parameters of a chaos experiment. ChaosExperiment CRs are created by the operator when experiments are invoked by ChaosEngine.
- **ChaosResult**: A resource to hold the results of a ChaosExperiment.

### Getting Started

We will start by creating an Amazon EKS cluster with managed nodes. We’ll then install LitmusChaos and a demo application. Then, we will install chaos experiments to be run on the demo application and observe the behavior.

We will need the following to complete the project:

1. [AWS CLI Version 2](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)

Since I had installed the AWS CLI version 2 on my terminal from my previous project. I use the following command to check its version:

![Screenshot from 2022-03-14 21-56-38](https://user-images.githubusercontent.com/91766546/158956922-d94838d6-3143-4dd5-a6a8-2072f6031ec3.png)

3. [eksctl](https://docs.aws.amazon.com/eks/latest/userguide/eksctl.html)

![Screenshot from 2022-03-14 21-58-02](https://user-images.githubusercontent.com/91766546/158956975-265c1ed0-b50e-4e92-ba94-11a91b48313b.png)

5. [kubectl](https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html)

![Screenshot from 2022-03-14 21-58-31](https://user-images.githubusercontent.com/91766546/158957038-b0866691-5a18-45c9-b045-bef370fa3f1c.png)

![Screenshot from 2022-03-14 21-59-23](https://user-images.githubusercontent.com/91766546/158957076-78061b3f-f3cf-4733-b591-b7b075b40719.png)

6. [helm](https://www.eksworkshop.com/beginner/060_helm/helm_intro/install/index.html)

![Screenshot from 2022-03-14 22-00-17](https://user-images.githubusercontent.com/91766546/158957110-6f1d140b-e2bf-49f7-864f-76ece19ea9f3.png)

![Screenshot from 2022-03-14 22-00-46](https://user-images.githubusercontent.com/91766546/158957200-a2166270-dacc-44f1-8a6d-74c2eb8ca669.png)

![Screenshot from 2022-03-14 22-01-02](https://user-images.githubusercontent.com/91766546/158957220-1dec9330-3ae6-40d1-bc6e-1244740c2594.png)

![Screenshot from 2022-03-14 22-02-00](https://user-images.githubusercontent.com/91766546/158957240-05582b06-0d1a-4f0d-8b33-2a0c6e4c1043.png)

Step 1:
Create a new EKS cluster using eksctl:

Let's open our terminal and run these commands on it.

![Screenshot from 2022-03-14 22-02-51](https://user-images.githubusercontent.com/91766546/158957674-b8baf585-70d3-4f7f-a1bb-c456cd622cd7.png)

![Screenshot from 2022-03-14 22-03-33](https://user-images.githubusercontent.com/91766546/158957747-cd057011-ae0e-4d1a-8a23-d503941a00a4.png)

![Screenshot from 2022-03-14 22-32-34](https://user-images.githubusercontent.com/91766546/158957772-b7135006-ad3d-45a5-95b7-25235aae69e9.png)

After running these commands on the terminal, navigate to AWS console and search for Amazon Elastic Kubernetes Service (EKS)

![2022-03-18_17-37](https://user-images.githubusercontent.com/91766546/159435323-4b5cd162-814b-4854-a931-883fa1149a9c.png)

The cluster created on the terminal should appear as such:

![Screenshot from 2022-03-15 01-45-05](https://user-images.githubusercontent.com/91766546/159435662-a24c0b10-39ce-479d-9665-50dbe75a1f96.png)

When click on the cluster eks-litmus-demo, 2 nodes must show. If for any reason, there is no Node in there, need to add Nodes manually.

![2022-03-18_17-43](https://user-images.githubusercontent.com/91766546/159435948-c5b69da4-e743-458f-b387-821f73c6a39b.png)

![2022-03-18_17-46](https://user-images.githubusercontent.com/91766546/159436002-0d0732cc-9d30-4f5d-90e2-01b47a70fdc3.png)

![g1](https://user-images.githubusercontent.com/91766546/160276563-7a5e076e-b413-49ca-957b-08d0b900b2ec.png)

![Screenshot from 2022-03-15 01-56-17](https://user-images.githubusercontent.com/91766546/160276592-5a2297ae-6c12-4a82-ba92-7d658ba27358.png)

![f](https://user-images.githubusercontent.com/91766546/160276115-772deca8-1035-4546-9745-88ab96e4f32b.png)

![ff](https://user-images.githubusercontent.com/91766546/160276132-96855c1e-411d-482d-b127-45b3bd8df671.png)

![fff](https://user-images.githubusercontent.com/91766546/160276135-3e06c459-6c55-4b97-9d55-657103512ee0.png)

![ffff](https://user-images.githubusercontent.com/91766546/160276142-718c1266-a864-4cfa-9b69-4d81390a604f.png)

![fffff](https://user-images.githubusercontent.com/91766546/160276154-aab5a1c6-5980-4741-aa16-8095f668c42f.png)

![ffffff](https://user-images.githubusercontent.com/91766546/160276170-d07a8dca-eb64-4aba-b276-b8744b1ce530.png)

![g](https://user-images.githubusercontent.com/91766546/160276181-c21f38d8-d92b-45f9-beb7-502a12dab1cb.png)

![gg](https://user-images.githubusercontent.com/91766546/160276187-7ec91d5d-0d12-45a5-9eac-ab2de05d9c7a.png)

![ggg](https://user-images.githubusercontent.com/91766546/160276197-eb45c10a-1c0b-472a-b0c2-318a8acb1825.png)

![gggg](https://user-images.githubusercontent.com/91766546/160276205-85239f0b-4334-438d-8444-760cf01e5d21.png)

![ggggg](https://user-images.githubusercontent.com/91766546/160276208-c6709cdc-db81-417d-bfdb-1e7123b4a4d5.png)


### Install LitmusChaos

Let’s install LitmusChaos on an Amazon EKS cluster using a Helm chart. The Helm chart will install the needed CRDs, service account configuration, and ChaosCenter.

Add the Litmus Helm repository using the command below:

![Screenshot from 2022-03-14 22-34-35](https://user-images.githubusercontent.com/91766546/159203409-b2b6c528-6429-48c8-a0fb-ea2ff473e8b4.png)

Confirm that you have the Litmus-related Helm charts:

![Screenshot from 2022-03-14 22-34-49](https://user-images.githubusercontent.com/91766546/159203452-fa0c36b0-2d20-4a5c-b48d-dfb6c47af344.png)

Create a namespace to install LitmusChaos.

![Screenshot from 2022-03-14 22-36-18](https://user-images.githubusercontent.com/91766546/159203507-7e7fe03b-ca59-41e4-b7ae-440688d82549.png)

By default, Litmus Helm chart creates NodePort services. Let’s change the backend service type to ClusterIP and front-end service type to LoadBalancer, so we can access the Litmus ChaosCenter using a load balancer.

![Screenshot from 2022-03-14 22-38-36](https://user-images.githubusercontent.com/91766546/159203621-af9af1bd-e84d-4918-b13c-eba2bcce9c08.png)

![Screenshot from 2022-03-14 22-38-51](https://user-images.githubusercontent.com/91766546/159203658-1e9c8214-f851-4b51-b83e-dce8ceb9b83d.png)

![Screenshot from 2022-03-14 22-39-07](https://user-images.githubusercontent.com/91766546/159203676-4b38579a-cd59-4ffd-966d-8c9253fc2848.png)

![Screenshot from 2022-03-14 22-39-30](https://user-images.githubusercontent.com/91766546/159203700-023f2ad5-2c2d-4c14-b129-e6a9f170d5b4.png)

Verify that LitmusChaos is running:

![Screenshot from 2022-03-14 22-40-10](https://user-images.githubusercontent.com/91766546/159203735-2726208f-abe3-4f1e-be3d-59240408843f.png)


![Screenshot from 2022-03-14 22-41-21](https://user-images.githubusercontent.com/91766546/159203767-d671a023-c3cb-4038-848a-977c2ca069c2.png)

![Screenshot from 2022-03-14 22-41-38](https://user-images.githubusercontent.com/91766546/159203789-bd51f5df-4164-402c-bd76-dbdefa213b60.png)

![Screenshot from 2022-03-15 00-16-07](https://user-images.githubusercontent.com/91766546/159203831-8bf568e0-7cec-454e-9feb-9db077def8b2.png)

Access Litmus ChaosCenter UI using the URL given above and sign in using the default username “admin” and password “litmus.”

![y](https://user-images.githubusercontent.com/91766546/159204045-0c9b5f44-c450-44aa-b7e9-8a29f4f9821e.png)

![Screenshot from 2022-03-15 00-17-24](https://user-images.githubusercontent.com/91766546/159204082-47c8315f-e946-4e0b-98a3-8b54126a62b2.png)

After successful sign-in, you should see the welcome dashboard. Click on the ChaosAgents link from the left-hand navigation.

![Screenshot from 2022-03-15 00-18-22](https://user-images.githubusercontent.com/91766546/159204231-b368e679-f6e1-4016-b815-fc5699226be5.png)

A ChaosAgent represents the target cluster where Chaos would be injected via Litmus. Confirm that Self-Agent is in Active status. Note: It may take a couple of minutes for the Self-Agent to become active.

![yyy](https://user-images.githubusercontent.com/91766546/159204688-65e3ae13-2f34-4fb4-a8b6-2b4208683dfe.png)

Confirm the agent installation by running the command below.

![Screenshot from 2022-03-15 00-20-34](https://user-images.githubusercontent.com/91766546/159205271-cfba16d3-3482-4163-b7f2-95ce38772d96.png)

Verify that LitmusChaos CRDs are created:

![Screenshot from 2022-03-15 00-22-00](https://user-images.githubusercontent.com/91766546/159205339-5d422046-c177-4bf4-86da-9b2ae7ba2517.png)

Verify that LitmusChaos API resources are created:

![Screenshot from 2022-03-15 00-22-52](https://user-images.githubusercontent.com/91766546/159205384-fe48f177-4484-4293-a6d7-e29c0036b243.png)

Now that we installed LitmusChaos on the EKS cluster, let’s install a demo application to perform some chaos experiments on.

### Install demo application

Let’s deploy nginx on our cluster using the manifest below to run our chaos experiments on it. Save the manifest as nginx.yaml and apply it.

![Screenshot from 2022-03-15 00-24-05](https://user-images.githubusercontent.com/91766546/159205537-2807b8e0-b918-4920-91db-42c6863a5590.png)

![Screenshot from 2022-03-15 00-24-54](https://user-images.githubusercontent.com/91766546/159205557-650a97bb-067e-4fcc-886f-602a00972802.png)

Verify if the nginx pod is running by executing the command below.

![Screenshot from 2022-03-15 00-25-25](https://user-images.githubusercontent.com/91766546/159205594-69c591de-95c2-4e1d-b987-349fcdf2cc6f.png)

### Chaos Experiments

[Litmus ChaosHub](https://hub.litmuschaos.io/) is a public repository where LitmusChaos community members publish their chaos experiments such as **pod-delete,** **node-drain**, **node-cpu-hog**, etc. In this demo walkthrough, we will perform the **pod-autoscaler** experiment from LitmusChaos hub to test cluster auto scaling on Amazon EKS cluster.

### Experiment: Pod Autoscaler

The intent of the pod auto scaler experiment is to check the ability of nodes to accommodate the number of replicas for a deployment. Additionally, the experiment can also be used to check the cluster auto-scaling feature.

**Hypothesis**: Amazon EKS cluster should auto scale when cluster capacity is insufficient to run the pods.

Chaos experiment can be launched using the Litmus ChaosCenter UI by creating a workflow. Navigate to Litmus Chaos Center and select **Litmus Workflows** in the left-hand navigation and then select the **Schedule a workflow** button to create a workflow.

![yyyy](https://user-images.githubusercontent.com/91766546/159206322-a5627a00-92e0-4253-b208-b872a045f563.png)

Select the Self-Agent radio button on the Schedule a new Litmus workflow page and select Next.

![yyyyy](https://user-images.githubusercontent.com/91766546/159206577-45769719-2dd0-4fe9-addb-59fed65bcf92.png)

Choose Create a new workflow using the experiments from ChaosHubs and leave the Litmus ChaosHub selected from the dropdown.

![h](https://user-images.githubusercontent.com/91766546/159206845-5019f30a-c4d7-4600-a6c5-b9c6ba5882b6.png)

Enter a name for your workflow on the next screen.

![hh](https://user-images.githubusercontent.com/91766546/159206923-6493f3f4-9c0c-4690-9f5d-d38808289af1.png)

Let’s add the experiments in the next step. Select Add a new experiment; then search for autoscaler and select the generic/pod-autoscaler radio button.

![hhh](https://user-images.githubusercontent.com/91766546/159207007-fd11e890-1567-4863-9c5d-8a1f39b5300e.png)

![hhhh](https://user-images.githubusercontent.com/91766546/159207077-3d26eb9e-ae65-48aa-8d11-72298a53dd7a.png)

Let’s the edit the experiment and change some parameters. Choose the Edit icon:

![hhhhhh](https://user-images.githubusercontent.com/91766546/159207342-5785b9e5-9402-4834-88f9-73ed31d0be94.png)

Accept the default values in the General, Target Application, and Define the steady state for this application sections. In the Tune Experiment section, set the TOTAL_CHAOS_DURATION to 180 and REPLICA_COUNT to 10. TOTAL_CHAOS_DURATION sets the desired chaos duration in seconds and REPLICA_COUNT is the number of replicas to scale during the experiment. Select Finish.

![hhhhh](https://user-images.githubusercontent.com/91766546/159207267-946967f8-c4ce-41bb-b9bd-c7006e715098.png)

![hhhhhhh](https://user-images.githubusercontent.com/91766546/159207625-816a1a80-fa9e-4fa1-96b4-3cf99107cce4.png)

Then, choose Next and accept the defaults for reliability score and schedule the experiment to run now. Finally, select Finish to run the chaos experiment.

![hhhhhhhhh](https://user-images.githubusercontent.com/91766546/159207723-86b51e00-be69-4f6c-b63f-f2e60245b8fb.png)

![hhhhhhhhhh](https://user-images.githubusercontent.com/91766546/159207782-1bc5b61c-3893-4b52-8898-55651ba0eddc.png)

The chaos experiment is now scheduled to run and you can look at the status by clicking on the workflow.

![hhhhhhhhhhh](https://user-images.githubusercontent.com/91766546/159207873-640394e4-2c41-43bf-a6da-3cb7a62b5fd8.png)

From the ChaosResults, you will see that the experiment failed because there was no capacity in the cluster to run 10 replicas.

![hhhhhhhhhhhh](https://user-images.githubusercontent.com/91766546/159207973-ce4afe13-a17b-43fe-9a77-0ea4d7fe485c.png)

### Install Cluster Autoscaler

Cluster Autoscaler for AWS provides integration with Auto Scaling groups. Cluster Autoscaler will attempt to determine the CPU, memory, and GPU resources provided by an Auto Scaling group based on the instance type specified in its launch configuration or launch template.

Create an IAM OIDC identity provider for your cluster with the following command.

![Screenshot from 2022-03-15 00-49-00](https://user-images.githubusercontent.com/91766546/159208025-ff79c322-3d57-49d1-943d-5e39d07fc3c8.png)

### Create an IAM policy and role

Create an IAM policy that grants the permissions that the Cluster Autoscaler requires to use an IAM role.

![Screenshot from 2022-03-15 00-49-58](https://user-images.githubusercontent.com/91766546/159208074-70a75b17-69c8-404d-b3d3-fad291efdf0b.png)

![Screenshot from 2022-03-15 01-55-23](https://user-images.githubusercontent.com/91766546/159208128-9d10868d-6969-4a81-9ec7-10824de13e75.png)

Create an IAM role and attach an IAM policy to it using eksctl.

![Screenshot from 2022-03-15 02-11-52](https://user-images.githubusercontent.com/91766546/159208198-3a7413ae-395b-4172-bc4e-f5d2f3aeb6c9.png)

Make sure your service account with the ARN of the IAM role is annotated.

![Screenshot from 2022-03-15 02-13-19](https://user-images.githubusercontent.com/91766546/159208251-a46c7c7e-9e9c-49d7-8533-3f18694eac4c.png)

### Deploy the Cluster Autoscaler

Download the Cluster Autoscaler manifest.

![Screenshot from 2022-03-15 02-14-17](https://user-images.githubusercontent.com/91766546/160276727-4c23c573-e9ed-475d-95d4-5030943131d1.png)

![Screenshot from 2022-03-15 02-20-05](https://user-images.githubusercontent.com/91766546/160276884-eb917609-6614-4245-8ff2-670a1c5699f9.png)

 Edit the downloaded file to replace <YOUR CLUSTER NAME> with the cluster name (eks-litmus-demo). The edited section should look like the following:
  
![Screenshot from 2022-03-15 02-19-37](https://user-images.githubusercontent.com/91766546/160276740-c9b995e3-6180-4621-a622-944d564e2f66.png)

 Apply the manifest file to the cluster.
  
 ![Screenshot from 2022-03-15 02-21-08](https://user-images.githubusercontent.com/91766546/160276904-e738c3b3-65f3-43f6-aac4-6c60bf0aab66.png)

 Patch the deployment to add the cluster-autoscaler.kubernetes.io/safe-to-evict annotation to the Cluster Autoscaler pods with the following command.
  
 ![Screenshot from 2022-03-15 02-22-48](https://user-images.githubusercontent.com/91766546/160276914-50acb37d-19c8-4339-a299-9d11a4593f62.png)

Find the latest Cluster Autoscaler version that matches the Kubernetes major and minor versions of your cluster. For example, if the Kubernetes version of your cluster is 1.21, find the latest Cluster Autoscaler release that begins with 1.21. Record the semantic version number (1.21.n) for that release to use in the next step.
  
![Screenshot from 2022-03-15 02-24-39](https://user-images.githubusercontent.com/91766546/160276998-a47e87e2-784b-436e-a015-63f19d42b681.png)

Set the Cluster Autoscaler image tag to the version that was exported in the previous step with the following command.
  
![Screenshot from 2022-03-15 02-25-09](https://user-images.githubusercontent.com/91766546/160277050-f6f0f8b5-9992-4fae-9027-1bd3c7b33e82.png)

After you have deployed the Cluster Autoscaler, you can view the logs and verify that it’s monitoring your cluster load. View your Cluster Autoscaler logs with the following command.
  
![Screenshot from 2022-03-15 02-27-23](https://user-images.githubusercontent.com/91766546/160277106-f2e14106-31b5-431e-8b87-71c81dd1bbae.png)

Now that we have deployed the Cluster Autoscaler, let’s rerun the same experiment by navigating to Litmus Workflows, then the Schedules tab. 
Select the three dots menu icon for the workflow and select Rerun Schedule.
  
![b](https://user-images.githubusercontent.com/91766546/160277263-c682a9e3-cbbb-43da-a16b-677a8c364791.png)

![bb](https://user-images.githubusercontent.com/91766546/160277268-61f637a5-44d3-4f0c-89a4-7c6f5bfb4170.png)

![bbb](https://user-images.githubusercontent.com/91766546/160277273-23c92133-e598-4a95-868c-137e17c3a0d8.png)

![bbbb](https://user-images.githubusercontent.com/91766546/160277280-7103785f-d492-4683-a878-3023c17c47e2.png)

![bbbbb](https://user-images.githubusercontent.com/91766546/160277289-c1da9291-4cbb-4d39-9641-b653a9689b20.png)

### Experiment Conclusion

Autoscaling the pod triggered the ClusterAautoscaler as a result of insufficient capacity, and a new node was added to the cluster, and the pods were successfully provisioned.

### Next steps

From the above walkthrough, we saw how to get started with Chaos Engineering using LitmusChaos on Amazon EKS cluster. There are additional experiments such as **pod-delete**, **node-drain**, **node-cpu-hog**, and so on that you can integrate with a CI/CD pipeline to perform Chaos Engineering. LitmusChaos also supports **gitops** and advanced chaos workflows using **Chaos Workflows**.

## pod-delete

Pod delete contains chaos to disrupt state of kubernetes resources. Experiments can inject random pod delete failures against specified application.

- Causes (forced/graceful) pod failure of random replicas of an application deployment.
- Tests deployment sanity (replica availability & uninterrupted service) and recovery workflows of the application pod.
  
![1_yoBx98rGeUy9UuDnFO4vfw](https://user-images.githubusercontent.com/91766546/160277413-d44a2a92-ed6c-4cfa-bfcc-19d92cb7d0e6.png)

**PRE-REQUISITE:**

**Install Litmus Operator**: a tool for injecting Chaos Experiments

## Installation[](https://docs.litmuschaos.io/docs/getting-started/installation/#installation)

Users looking to use Litmus for the first time have two options available to them today. One way is to use a hosted Litmus service like [ChaosNative Litmus Cloud](https://cloud.chaosnative.com/). Alternatively, users looking for some more flexibility can install Litmus into their own Kubernetes cluster.

Users choosing the self-hosted option can refer to our Install and Configure docs for installing alternate versions and more detailed instructions.

- Self-Hosted
- Hosted (Beta)

Installation of Self-Hosted Litmus can be done using either of the below 

**methods** :

[Helm3](https://docs.litmuschaos.io/docs/getting-started/installation/#install-litmus-using-helm) chart 

[Kubectl](https://docs.litmuschaos.io/docs/getting-started/installation/#install-litmus-using-kubectl) yaml spec file. 

Refer to the below details for Self-Hosted Litmus installation.
  
### Install Litmus using Helm

The helm chart will install all the required service account configuration and ChaosCenter.

Since we already installed Helm at the beginning of this project, there is no need to do it again.

Note: This is the command I would use in case it wasn't install prior:

![bbbbbb](https://user-images.githubusercontent.com/91766546/160277648-8c235791-3ab0-4962-adfd-a8b15beda83e.png)Add the litmus helm repository

![Screenshot from 2022-03-15 02-41-22](https://user-images.githubusercontent.com/91766546/160277595-9900496d-5314-410f-b266-e6407d5e748f.png)

Then

### Install this Chaos Experiment
  
You can install the Chaos Experiment using the following command:
  
![Screenshot from 2022-03-15 07-52-51](https://user-images.githubusercontent.com/91766546/160278011-ea5a6ae0-4cb2-4ef5-a2bb-cfdaeee71821.png)

### Setup Service Account (RBAC)

Create a service account using the following command:

![Screenshot from 2022-03-15 02-42-22](https://user-images.githubusercontent.com/91766546/160278070-92832383-af62-4334-99b9-abb0200a3489.png)

Sample Chaos Engine

  Create an engine.yaml file using nano

![Screenshot from 2022-03-15 07-53-25](https://user-images.githubusercontent.com/91766546/160278122-bdc75b42-ef09-4500-ba97-dd73920af83d.png)

![Screenshot from 2022-03-15 07-54-00](https://user-images.githubusercontent.com/91766546/160278154-7212928d-f800-4991-b9f5-0189a5699e37.png)
