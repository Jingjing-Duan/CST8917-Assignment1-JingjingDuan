# Assignment 1: Serverless Computing - Critical Analysis
**Course:** CST8917 - Serverless Applications | Summer 2026  
**Student Name:** Jingjing Duan  
**Student Number:** 041159829 
**Date:** June 14, 2026  

---

## Part 1: Paper Summary

### 1. Main Argument
In *Serverless Computing: One Step Forward, Two Steps Back*, Hellerstein et al. (2019) argue that serverless computing brings important improvements to cloud application development, but current Function-as-a-Service (FaaS) platforms also introduce several limitations. The “one step forward” refers to the advantages of serverless computing, such as automatic scaling and pay-per-use pricing. These features reduce the need for developers to manage servers and make cloud resources easier to use.

However, the “two steps back” means that first-generation serverless platforms sacrifice some capabilities needed for modern distributed applications. Although serverless works well for simple event-driven tasks, it is less suitable for applications that require long-running execution, large amounts of data, or coordination among multiple components. According to the authors, these limitations prevent serverless computing from reaching its full potential.

### 2. Key Limitations Identified
The paper discusses several weaknesses of first-generation serverless platforms (Hellerstein et al., 2019).

* **Execution Time Constraints:** Serverless functions are designed to be short-lived. Because functions have execution time limits, they are not suitable for long-running tasks or applications that require continuous processing.
* **Communication and Network Limitations:** Functions cannot communicate directly with each other because they do not have permanent addresses or identities. Instead, they often exchange information through external storage or databases, which increases latency and creates communication bottlenecks.
* **The “Data Shipping” Anti-Pattern:** In many cases, large amounts of data must be transferred from storage to the function. The authors argue that it would be more efficient to move computation closer to the data rather than constantly moving data across the network. Excessive data movement leads to higher costs and lower performance.
* **Limited Hardware Access:** Serverless platforms mainly provide standard CPU resources and usually do not offer access to specialized hardware such as GPUs or TPUs. This limits their ability to support machine learning and other high-performance workloads.
* **Challenges for Distributed and Stateful Workloads:** Since functions are stateless and isolated from one another, building distributed applications becomes difficult. Managing shared state and coordinating multiple functions require additional services, making the system more complicated and less efficient.

### 3. Proposed Future Directions
The authors believe that future cloud platforms should provide more flexibility and better support for distributed applications (Hellerstein et al., 2019).

* **Fluid Code and Data Placement:** First, computation and data should be placed more intelligently so that code can run closer to where the data is stored. This would reduce unnecessary data transfer and improve performance.
* **Heterogeneous Hardware Support:** Second, future platforms should support different types of hardware, including GPUs and other accelerators, to handle workloads that require more computing power.
* **Long-Running, Addressable Virtual Agents:** Third, cloud systems should provide long-running and addressable entities that can maintain state and communicate directly with one another. Such capabilities would make it easier to build complex distributed applications without relying heavily on external storage.

Overall, the authors do not reject serverless computing. Instead, they believe that serverless needs to evolve beyond simple stateless functions and provide better support for data-intensive and distributed workloads.

---

## Part 2: Azure Durable Functions Deep Dive

### 1. Orchestration Model
Azure Durable Functions extends the traditional Function-as-a-Service model by introducing three types of functions: client functions, orchestrator functions, and activity functions (Microsoft, 2026a). A client function starts the workflow, the orchestrator defines the sequence of tasks, and activity functions perform the actual work (Microsoft, 2026a). Unlike basic serverless functions that operate independently, Durable Functions provide a way to coordinate multiple functions as part of a larger workflow (Microsoft, 2026a). This approach helps address one of the concerns raised by Hellerstein et al. (2019), namely that first-generation serverless platforms lack effective coordination mechanisms for complex applications.

### 2. State Management
Traditional serverless functions are stateless, meaning that they do not remember previous execution results (Hellerstein et al., 2019). Azure Durable Functions addresses this problem by automatically managing state through event sourcing and checkpointing (Microsoft, 2026b). Whenever an activity completes, the framework records the result in storage. If the workflow needs to continue later, the orchestrator can replay its execution history and rebuild its state without re-running completed tasks (Microsoft, 2026b). As a result, developers do not need to manually save and restore state information. This makes it easier to build long-running and stateful applications compared with traditional FaaS platforms.

### 3. Execution Timeouts
Regular Azure Functions are subject to execution time limits, which makes them unsuitable for long-running processes (Hellerstein et al., 2019). Durable Functions overcome this limitation by allowing orchestrators to pause and resume execution (Microsoft, 2026b). When the workflow reaches an asynchronous operation, the current state is saved and the orchestrator can be unloaded from memory. Once the operation finishes, the workflow resumes from where it left off (Microsoft, 2026b). Although individual activity functions still have execution limits, the overall workflow can continue for much longer periods. This capability helps reduce the impact of the execution time constraints discussed by Hellerstein et al. (2019).

### 4. Communication Between Functions
In traditional serverless systems, functions often communicate indirectly through external storage or messaging services (Hellerstein et al., 2019). Azure Durable Functions simplify this process by allowing orchestrator functions to schedule activities and receive their outputs automatically (Microsoft, 2026a). The framework handles the communication details internally and stores messages behind the scenes using internal storage abstractions (Microsoft, 2026c). Developers do not need to manually manage queues or intermediate storage. Although Durable Functions still rely on storage services underneath, they provide a cleaner and more convenient programming model, which reduces some of the communication difficulties described in the paper.

### 5. Parallel Execution (Fan-out/Fan-in)
Durable Functions support the fan-out/fan-in pattern, which allows multiple activity functions to run at the same time (Microsoft, 2026a). The orchestrator starts several tasks in parallel and then waits until all of them finish before combining the results (Microsoft, 2026a). This pattern improves performance when processing large amounts of work that can be divided into independent tasks. Compared with basic serverless functions, it provides better support for distributed execution and coordination. While it does not completely eliminate all challenges of distributed computing, it makes parallel processing much easier to implement and manage (Microsoft, 2026a).

---

## Part 3: Critical Evaluation

### 1. Limitations That Remain Unresolved
Although Azure Durable Functions provide better workflow management, some of the limitations discussed by Hellerstein et al. (2019) still remain:

* **The "Data Shipping" Problem:** Durable Functions rely heavily on Azure Storage to save state and exchange messages between functions. While this process is handled automatically by the framework, data still needs to move back and forth between storage and compute resources (Microsoft, 2026c). As a result, the system continues to depend on network I/O, which may become inefficient for applications that process large amounts of data. In this sense, Durable Functions improve the developer experience, but they do not fundamentally change the underlying "data shipping" architecture described in the paper (Hellerstein et al., 2019).
* **Lack of Specialized Hardware Support:** Durable Functions run on the Azure Functions platform and mainly use standard CPU resources. They do not provide built-in, autoscaling support for GPUs or other specialized hardware accelerators (Hellerstein et al., 2019). Because of this, workloads such as machine learning training and high-performance computing are still better suited to other cloud services. This limitation shows that serverless platforms have not yet achieved the level of hardware flexibility envisioned by the authors (Hellerstein et al., 2019).

### 2. My Verdict
In my opinion, Azure Durable Functions represent important progress in serverless computing, but they do not completely solve the problems identified by Hellerstein et al. (2019).

Durable Functions make it much easier to build complex workflows by providing automatic state management and coordination between functions. Developers can focus on application logic instead of manually handling queues and state persistence. From a practical point of view, these improvements make serverless development more productive and easier to maintain.

However, many of the underlying limitations still exist. Durable Functions continue to rely on external storage for communication and state management (Microsoft, 2026c), and they do not address the lack of support for specialized hardware. Therefore, I believe that Durable Functions mainly provide a practical solution that works around some of the weaknesses of first-generation serverless platforms rather than completely redesigning the serverless model. 

Overall, Azure Durable Functions represent meaningful progress toward the future envisioned by the authors, but they do not fully achieve that vision (Hellerstein et al., 2019). More advances in data locality, hardware support, and distributed computing capabilities will be needed before serverless computing can support a wider range of applications efficiently.

---

## References

1. Hellerstein, J. M., Faleiro, J., Gonzalez, J. E., Schleier-Smith, J., Sreekanti, V., Tumanov, A., & Wu, C. (2019). *Serverless Computing: One Step Forward, Two Steps Back*. Conference on Innovative Data Systems Research (CIDR). https://www.cidrdb.org/cidr2019/papers/p119-hellerstein-cidr19.pdf

2. Microsoft. (2026a). *Durable Functions overview*.
https://learn.microsoft.com/en-us/azure/azure-functions/durable-functions/durable-functions-overview

3. Microsoft. (2026b). *Orchestrations checkpoints and replay in Durable Functions*. Microsoft Learn. https://learn.microsoft.com/en-us/azure/azure-functions/durable/durable-functions-orchestrations

4. Microsoft. (2026c). *Performance and scale in Durable Functions (Azure Functions)*. Microsoft Learn. https://learn.microsoft.com/en-us/azure/azure-functions/durable/durable-functions-perf-and-scale

---

## AI Disclosure Statement
I used Gemini to help summarize the paper, improve grammar, and organize the content.