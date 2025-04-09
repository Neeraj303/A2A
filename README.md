![image info](images/A2A_banner.png)

**_An open protocol enabling communication and interoperability between opaque agentic applications._**

<!-- TOC -->

- [Agent2Agent Protocol A2A](#agent2agent-protocol-a2a)
    - [Getting Started](#getting-started)
    - [Contributing](#contributing)
    - [What's next](#whats-next)
    - [About](#about)

<!-- /TOC -->

One of the biggest challenges in enterprise AI adoption is getting agents built on different frameworks and vendors to work together. That’s why we created an open *Agent2Agent (A2A) protocol*, a collaborative way to help agents across different ecosystems communicate with each other. Google is driving this open protocol initiative for the industry because we believe this protocol will be **critical to support multi-agent communication by giving your agents a common language – irrespective of the framework or vendor they are built on**. 
With *A2A*, agents can show each other their capabilities and negotiate how they will interact with users (via text, forms, or bidirectional audio/video) – all while working securely together.

### **See A2A in Action**

Watch this demo video to see how A2A enables seamless communication between different agent frameworks:

<video width="100%" controls>
  <source src="https://storage.googleapis.com/gweb-developer-goog-blog-assets/original_videos/A2A_demo_v4.mp4?X-Goog-Algorithm=GOOG4-RSA-SHA256&X-Goog-Credential=storage-bucket-access%40gweb-developers-gblog-cms.google.com.iam.gserviceaccount.com%2F20250408%2Fauto%2Fstorage%2Fgoog4_request&X-Goog-Date=20250408T174915Z&X-Goog-Expires=86400&X-Goog-SignedHeaders=host&X-Goog-Signature=089d124afdf68c35b97e8042262f686a66681d24b8e21d0586537026faf36598da2ab9ad3bb2d8312e88cba10f443dda33eeb899c500a22b7374a79cf75c4bffd62ab095b45c07dbb4853d796d925123df94bd4b10b36c7bd4dc63aa984784b5a50e2ae7b238457d04b2410682943e2d514c4b530aa82c1a245ee834322f1676a363fa5913a318fe093f7769be17480303aeaee49f7d102ffbad7f9f5ab674386b5b10204d32bc7037d1524617cf21eccb739745a25d6e88cdce7c9fc121923cebbca5d96de0a7014d17953ee7c1c819f3e3ad150ef6d1fbc4730bd8317eb6e5d2c72cb51aebccdc85a2c78b101bc61852b63df6700ba2fed47f10a9ecfa1c16" type="video/mp4">
  Your browser does not support the video tag.
</video>

### **Conceptual Overview**

The Agent2Agent (A2A) protocol facilitates communication between independent AI agents. Here are the core concepts:

*   **Agent Card:** A public metadata file (usually at `/.well-known/agent.json`) describing an agent's capabilities, skills, endpoint URL, and authentication requirements. Clients use this for discovery.
*   **A2A Server:** An agent exposing an HTTP endpoint that implements the A2A protocol methods (defined in the [json specification](/specification)). It receives requests and manages task execution.
*   **A2A Client:** An application or another agent that consumes A2A services. It sends requests (like `tasks/send`) to an A2A Server's URL.
*   **Task:** The central unit of work. A client initiates a task by sending a message (`tasks/send` or `tasks/sendSubscribe`). Tasks have unique IDs and progress through states (`submitted`, `working`, `input-required`, `completed`, `failed`, `canceled`).
*   **Message:** Represents communication turns between the client (`role: "user"`) and the agent (`role: "agent"`). Messages contain `Parts`.
*   **Part:** The fundamental content unit within a `Message` or `Artifact`. Can be `TextPart`, `FilePart` (with inline bytes or a URI), or `DataPart` (for structured JSON, e.g., forms).
*   **Artifact:** Represents outputs generated by the agent during a task (e.g., generated files, final structured data). Artifacts also contain `Parts`.
*   **Streaming:** For long-running tasks, servers supporting the `streaming` capability can use `tasks/sendSubscribe`. The client receives Server-Sent Events (SSE) containing `TaskStatusUpdateEvent` or `TaskArtifactUpdateEvent` messages, providing real-time progress.
*   **Push Notifications:** Servers supporting `pushNotifications` can proactively send task updates to a client-provided webhook URL, configured via `tasks/pushNotification/set`.

**Typical Flow:**

1.  **Discovery:** Client fetches the Agent Card from the server's well-known URL.
2.  **Initiation:** Client sends a `tasks/send` or `tasks/sendSubscribe` request containing the initial user message and a unique Task ID.
3.  **Processing:**
    *   **(Streaming):** Server sends SSE events (status updates, artifacts) as the task progresses.
    *   **(Non-Streaming):** Server processes the task synchronously and returns the final `Task` object in the response.
4.  **Interaction (Optional):** If the task enters `input-required`, the client sends subsequent messages using the same Task ID via `tasks/send` or `tasks/sendSubscribe`.
5.  **Completion:** The task eventually reaches a terminal state (`completed`, `failed`, `canceled`).

### **Getting Started**

* 📚 Read the [technical documentation](https://google.github.io/A2A/#/documentation) to understand the capabilities
* 📝 Review the [json specification](/specification) of the protocol structures
* 🎬 Use our [samples](/samples) to see A2A in action
    * Sample A2A Client/Server ([Python](/samples/python/common), [JS](/samples/js/src))
    * [Multi-Agent Web App](/demo/README.md)
    * CLI ([Python](/samples/python/hosts/cli/README.md), [JS](/samples/js/README.md))
* 🤖 Use our [sample agents](/samples/python/agents/README.md) to see how to bring A2A to agent frameworks
    * [Agent Developer Kit (ADK)](/samples/python/agents/google_adk/README.md)
    * [CrewAI](/samples/python/agents/crewai/README.md)
    * [LangGraph](/samples/python/agents/langgraph/README.md)
    * [Genkit](/samples/js/src/agents/README.md)
* 📑 Review key topics to understand protocol details 
    * [A2A and MCP](https://google.github.io/A2A/#/topics/a2a_and_mcp.md)
    * [Agent Discovery](https://google.github.io/A2A/#/topics/agent_discovery.md)
    * [Enterprise Ready](https://google.github.io/A2A/#/topics/enterprise_ready.md)
    * [Push Notifications](https://google.github.io/A2A/#/topics/push_notifications.md) 

### **Contributing**

We welcome contributions! Please see our [contributing guide](CONTRIBUTING.md) to get started.\
Have questions? Join our community in GitHub discussions.\
Help us with protocol improvement feedback, in GitHub issues.\
Want to send private feedback? If so, please use this [Google form](https://docs.google.com/forms/d/e/1FAIpQLScS23OMSKnVFmYeqS2dP7dxY3eTyT7lmtGLUa8OJZfP4RTijQ/viewform)

### **What's next**

Future plans include improvements to the protocol itself and enhancements to the samples:

**Protocol Enhancements:**

*   **Agent Discovery:**
    *   Formalize inclusion of authorization schemes and optional credentials directly within the `AgentCard`.
*   **Agent Collaboration:**
    *   Investigate a `QuerySkill()` method for dynamically checking unsupported or unanticipated skills.
*   **Task Lifecycle & UX:**
    *   Support for dynamic UX negotiation *within* a task (e.g., agent adding audio/video mid-conversation).
*   **Client Methods & Transport:**
    *   Explore extending support to client-initiated methods (beyond task management).
    *   Improvements to streaming reliability and push notification mechanisms.

**Sample & Documentation Enhancements:**

*   Simplify "Hello World" examples.
*   Include additional examples of agents integrated with different frameworks or showcasing specific A2A features.
*   Provide more comprehensive documentation for the common client/server libraries.
*   Generate human-readable HTML documentation from the JSON Schema.

### **About**

A2A Protocol is an open source project run by Google LLC, under [License](LICENSE) and open to contributions from the entire community.
