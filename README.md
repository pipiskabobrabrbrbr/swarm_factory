# 🚀 Swarm Factory DEMO: End-to-End Multi-Agent System Bootstrapper 🚀

> This repository serves as an **end-to-end demonstration and quickstart guide** for launching a complete, functional multi-agent ecosystem using the Swarm framework. It showcases how to leverage the `AgentFactory` to programmatically instantiate, configure, and connect various Swarm components and agents, providing a robust reference for building dynamic and adaptive AI solutions.

## ✨ Quickstart: Launch Your Complete Swarm Ecosystem in Minutes!

This demo provides a streamlined way to experience the full power of Swarm. You'll launch the foundational services, register capabilities, and then dynamically instantiate the core agents (Specialist, Planner, Executor) that form a collaborative multi-agent system.

### Prerequisites

1.  **Install Rust**: If you don't have it already, download and install it from [rust-lang.org](https://www.rust-lang.org/tools/install).
2.  **Get an LLM API Key**: Swarm agents require an LLM to function. We recommend obtaining a free API key from [Groq](https://console.groq.com/keys) or [Google AI Studio (for Gemini)](https://aistudio.google.com/app/apikey). It can also connect to a local `llama.cpp` OpenAI-compatible server instance.

### Step 1: Launch the MCP Server

Before running `swarm_factory`, you need to kickstart an MCP (Model Context Protocol) server. You can use the example server provided in the main `swarm` repository:

```bash
git clone https://github.com/fcn06/swarm.git
cd swarm
cargo build --release --example main-server
./target/release/examples/main-server --port 8000 --log-level "warn" all &
cd ..
```

### Step 2: Set Your LLM API Keys

The `swarm_factory` demo utilizes LLMs for various agent roles. For simplicity, you can use the *same* API key for all roles.

```bash
# Replace <YOUR-LLM-API-KEY> with your actual API key.
export LLM_A2A_API_KEY=<YOUR-LLM-API-KEY>       # For general Agent-to-Agent communication
export LLM_PLANNER_API_KEY=<YOUR-LLM-API-KEY>     # For the Planner Agent
export LLM_JUDGE_API_KEY=<YOUR-LLM-API-KEY>     # For the LLM-as-a-Judge evaluation service
```

### Step 3: Clone and Build `swarm_factory`

```bash
git clone https://github.com/fcn06/swarm_factory.git
cd swarm_factory
cargo build --release
```

### Step 4: Run the `swarm_factory` Demo!

Execute the compiled binary. This will start the core Swarm Services (Discovery, Memory, Evaluation) and then dynamically launch the Specialist, Executor, and Planner agents via the `AgentFactory`.

```bash
./target/release/swarm_factory --log-level "warn"
```

You will see output logs indicating the successful setup of services, registration of tasks/tools, and the launch of `Basic_Agent`, `Executor_Agent`, and `Planner_Agent`.

### Step 5: Interact with Your Swarm!

Once the agents are launched, you can interact with them using a simple A2A client. There is one available in the main `swarm` repository. Here's a sample call using this client to query your newly launched Swarm:

```bash
# Make sure you are in the 'swarm' directory, not 'swarm_factory'
cd ../swarm 
./target/release/simple_workflow_agent_client --port 9590 --log-level "warn" --generation-type "dynamic_generation" --user-query "Who is Vivaldi ?"
```

## 🛠️ How This Demo Works: An Architectural Deep Dive

This `swarm_factory` project serves as a comprehensive blueprint for dynamic multi-agent system creation, showcasing the seamless integration of all critical Swarm components. It demonstrates the capabilities provided by both `swarm_commons` and `swarm_services` as part of a complete system.

### What Does This Demo Do?

This reference implementation demonstrates how to use the `swarm` crate to build an agentic ecosystem by:

*   **Launching Core Swarm Services:** Setting up the essential `Discovery Service`, `Memory Service`, and `Evaluation Service` (provided by `swarm_services`).
*   **Creating and Launching an `AgentFactory`:** The central component for programmatic agent instantiation.
*   **Dynamically Launching Agents:** Using the `AgentFactory` to create a `Domain Specialist` (`Basic_Agent`), `Planner Agent`, and `Executor Agent`.

This project bootstraps a minimal, self-contained multi-agent system where the `Planner Agent` can leverage the `Specialist Agent` (via the `Executor Agent`) to handle user requests, all facilitated by the underlying Swarm services.

### Key Architectural Demonstrations

The `main.rs` in `swarm_factory` provides a clear example of the integration points:

1.  **Central Services Initialization (`swarm_services`)**
    The code first instantiates and configures the essential Swarm Services that enable collaboration and feedback, which are provided by the `swarm_services` project:

    *   **Discovery Service:** Allows agents to find and register themselves, their capabilities (Domain Agents), and the available external services (Tasks, Tools).
    *   **Memory Service:** (Set up but configurable for active use) Essential for maintaining conversational and contextual history.
    *   **Evaluation Service (LLM as a Judge):** (Set up) Provides a feedback loop for performance assessment and dynamic workflow refinement.

2.  **Capability Registration (via `swarm_commons` and `swarm_services`)**
    Before launching the `Planner Agent`, the demo registers Tasks and Tools with the `Discovery Service`. This step is crucial, as it provides the `Planner Agent` with the knowledge base needed to generate an intelligent workflow plan. The models and configurations for these capabilities often reside in `swarm_commons`.

    ```rust
    register_tasks(discovery_service.clone()).await?; // Register the 'greeting' task
    register_tools(args.mcp_config_path.clone(),discovery_service.clone()).await?; // Register external tools
    ```

3.  **Dynamic Agent Instantiation via `AgentFactory` (from `swarm`)**
    The heart of the demo is the programmatic launch of specialized agents using configurations defined at runtime. This process uses core agent logic and models defined in `swarm_commons` and orchestrated by `swarm` itself:

    *   **Specialist with MCP:** The `Basic_Agent` is launched with an associated `FactoryMcpRuntimeConfig`, connecting it to the external world through the Model Context Protocol (MCP).

    *   **Orchestrators:** The `Executor_Agent` and `Planner_Agent` are launched and specifically configured to be aware of each other (e.g., the Planner knows the Executor's URL), forming the core orchestration layer.

    ```rust
    // Launch Basic Agent (Specialist with MCP)
    agent_factory.launch_agent(&factory_agent_config, Some(&factory_mcp_runtime_config), AgentType::Specialist).await
    // ...
    // Launch Executor and Planner
    agent_factory.launch_agent(&factory_agent_config_executor, None, AgentType::Executor).await
    agent_factory.launch_agent(&factory_agent_config_planner, None, AgentType::Planner).await
    ```

## 📚 Learn More

This factory implementation is built upon the robust multi-agent primitives provided by the main Swarm repositories:

*   **Swarm Framework:** [https://github.com/fcn06/swarm](https://github.com/fcn06/swarm) - Dive into the core components, protocols (MCP, A2A), and agent architecture.
*   **Swarm Commons:** [./codebase/swarm_commons/README.md](./codebase/swarm_commons/README.md) - Understand the foundational traits, models, configurations, and LLM integrations shared across Swarm.
*   **Swarm Services:** [./codebase/swarm_services/README.md](./codebase/swarm_services/README.md) - Explore the core infrastructure services for agent discovery, memory, and evaluation.

If you have any questions or ideas for extending this demo, please open an issue on the main Swarm repository!
