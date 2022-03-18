# Project-5-Chaos-Engineering
## Chaos Engineering with LitmusChaos on Amazon EKS

What is Chaos Engineering?

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

## LitmusChaos Architectur

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

After running these commands on the terminal, navigate to AWS console and search for Amazon Elastic Kubernetes Service (EKS). The cluster created on the terminal should appear as such:

![Screenshot from 2022-03-15 01-45-05](https://user-images.githubusercontent.com/91766546/158957914-5f4fad3a-8dfc-4973-8dd2-4d5ba70eb029.png)
