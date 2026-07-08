---
title: "Blog 3"
date: 2026-07-07
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# Modernizing Applications and Databases with GenAI on AWS

In the software development process, many businesses are still operating legacy systems based on a monolithic model. Initially, this architecture might be easy to deploy and manage. However, as the system grows, user numbers increase, and business requirements constantly change, monolithic applications often reveal significant limitations: slow release times, difficulty in scaling independently, high operational costs, and the risk of cascading failures. Application modernization is not just about changing the technology, but also a transformation in system design thinking.

This blog post (compiled by the Otokoshi team) will focus on GenAI-powered App & Database Modernization solutions. Instead of simply "upgrading code," this process is a seamless combination of Domain-Driven Design (DDD), microservices, event-driven architecture, serverless computing, and AI tools (such as Amazon Q Developer, AWS Transform) to assist in analyzing, transforming, and optimizing the system from its roots.

---

## Architectural Guidance

The core change in the modernization process is the shift from a massive monolithic block to an ecosystem of small services (microservices), loosely coupled through event streams (event-driven). Not attempting to rewrite the entire system from scratch significantly reduces business risks. By defining business boundaries, we can decouple individual parts and safely transition them to serverless models.
**The modernization solution architecture can be visualized as follows:**
**The modernization solution architecture can be visualized as follows:**

![Figure 1. Architectural shift from Monolithic to Microservices combined with Event-driven and Serverless](/images/Blog3/blog3(0).jpg)

> *Figure 1. Architectural shift from Monolithic to Microservices combined with Event-driven and Serverless.*

---

When establishing boundaries for components in the new system, the system will share the following common characteristics:

* Starting with the business domain before discussing technology.
* Each critical function becomes an independent, autonomous service.
* Flexible, asynchronous communication via events instead of direct API calls.
* Running code without managing underlying physical servers or virtual machines.

When determining the monolithic decoupling strategy, consider:

* **Business**: Identify the core domains (order management, billing, customer, inventory).
* **Architecture**: Use Bounded Contexts to break things down, deciding what to keep and what to convert into a microservice.
* **Roadmap**: Modernize incrementally (part by part) rather than making a comprehensive change all at once.

---

## Technology Selection and Compute Models

| System Aspect / Level of Control | Suitable AWS Services / Compute Models |
| --- | --- |
| Requires strict control over OS, runtime, and configuration | Amazon Elastic Compute Cloud (Amazon EC2) |
| Containerized applications, microservices, and orchestration needs | Amazon Elastic Container Service (ECS), Amazon EKS |
| Running containers without managing underlying servers | AWS Fargate |
| Event-driven, short-lived tasks, small APIs, automation | AWS Lambda |

---

## Domain-Driven Design (DDD)

![5-step application modernization workflow](/images/Blog3/blog3(1).jpg)

A common mistake is starting with the question: "Should we use Lambda, ECS, or Kubernetes?". In reality, the first question must be: "What business operations is the system serving?". DDD helps technical and business teams use a ubiquitous language. Through *event storming* sessions, you can identify: - Important business events and participating actors.

* The timeline of key processing steps.
* The bounded contexts that need to be decoupled.

Drawbacks/Challenges: Requires continuous involvement and communication between *domain experts* and *software experts* to explore complexity before diving into code.

---

## Event-Driven Architecture

Solves the problem of communication between components. If microservices call each other using synchronous APIs, the system quickly becomes cross-dependent. When one service goes down, the entire system can be paralyzed.

* A service (e.g., Order) only needs to emit an event (OrderCreated).
* Other services (Payment, Inventory, Notification) independently listen to that event through routers like **Amazon EventBridge** or **Amazon SNS/SQS** to process their respective tasks.

> The system becomes more flexible, easier to scale, and eliminates direct dependencies between components, making the architecture "loosely-coupled".

---

## Compute Evolution: Transitioning to Serverless

* Offloading infrastructure burden: Maintenance, scaling, patching, and capacity management tasks are handed over to AWS.
* Harnessing the power of **AWS Lambda**:
1. Highly suitable for event-driven workloads.
2. Cost optimization through automatic scaling and pay-as-you-go pricing based on actual usage.
3. Developers only need to focus on writing business logic.

---

## The Role of GenAI in the Solution

### 1. Amazon Q Developer and AWS Transform

Although it cannot completely replace Software Architects, GenAI acts as a high-speed assistant to help solve complex data and source code migration problems.

![GenAI assistance through Amazon Q Developer and AWS Transform](/images/Blog3/blog3(2).jpg)

* **Amazon Q Developer**: Analyzes legacy codebases, suggests module decoupling, generates technical documentation, proposes test cases, and assists with refactoring (e.g., upgrading Java versions, explaining dependencies).
* **AWS Transform**: An agentic AI service supporting large-scale modernization for heavy workloads such as Windows, mainframes, or VMware.

Example of an AI-proposed transformation plan (diff) to replace a synchronous API call with an event-emitting model:

```diff
- // Legacy Monolithic Synchronous Call
- paymentService.processPayment(order.getId(), order.getTotal());
- inventoryService.deductItems(order.getItems());

+ // Modern Event-Driven Architecture with EventBridge
+ PutEventsRequestEntry eventEntry = PutEventsRequestEntry.builder()
+    .source("com.ecommerce.orders")
+    .detailType("OrderCreated")
+    .detail(orderJson)
+    .eventBusName("EcommerceEventBus")
+    .build();
+ eventBridgeClient.putEvents(PutEventsRequest.builder().entries(eventEntry).build());