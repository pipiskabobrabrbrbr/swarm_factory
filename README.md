# 🏭 Swarm Factory Demo: Dynamic Multi-Agent System Bootstrapper 🚀

The swarm_factory repository is the ultimate quickstart and reference implementation for the Agent Factory component of the Swarm framework.

It demonstrates how to programmatically instantiate, configure, and connect a fully-functional multi-agent ecosystem—a process essential for building truly dynamic and adaptive AI solutions.

✨ What Does This Demo Do?
This project bootstraps a minimal, self-contained multi-agent system by launching three core agents using the AgentFactory:

*   Specialist Agent (Basic_Agent): A core worker, launched with an MCP Runtime for tool/service interaction.

*   Executor Agent (Executor_Agent): The "Doer" responsible for executing planned workflows.

*   Planner Agent (Planner_Agent): The "Architect" that generates execution plans by consulting the Discovery Service (pre-populated with available Tasks, Tools, and Agents).

By running this project, you see the AgentFactory in action, creating a system where the Planner can leverage the Specialist (via the Executor) to handle user requests.

🛠️ Key Architectural Demonstrations
The main.rs in swarm_factory serves as a blueprint for dynamic multi-agent system creation, showcasing the integration of all critical Swarm components:

1.  Central Services Initialization
    The code first instantiates and configures the essential Swarm Services that enable collaboration and feedback:

    *   Discovery Service: Allows agents to find and register themselves, their capabilities (Domain Agents), and the available external services (Tasks, Tools).

    *   Memory Service: (Set up but not actively used in the minimal config) Essential for maintaining conversational and contextual history.

    *   Evaluation Service (LLM as a Judge): (Set up) Provides a feedback loop for performance assessment and dynamic workflow refinement.

2.  Capability Registration
    Before launching the Planner, the demo registers Tasks and Tools with the Discovery Service. This step is crucial, as it provides the Planner Agent with the knowledge base needed to generate an intelligent workflow plan.

    Rust
    ```rust
    register_tasks(discovery_service.clone()).await?; // Register the 'greeting' task
    register_tools(args.mcp_config_path.clone(),discovery_service.clone()).await?; // Register external tools
    ```
3.  Dynamic Agent Instantiation via AgentFactory
    The heart of the demo is the programmatic launch of specialized agents using configurations defined at runtime:

    *   Specialist with MCP: The Basic_Agent is launched with an associated FactoryMcpRuntimeConfig, connecting it to the external world through the Model Context Protocol (MCP).

    *   Orchestrators: The Executor_Agent and Planner_Agent are launched and specifically configured to be aware of each other (e.g., the Planner knows the Executor's URL), forming the core orchestration layer.

    Rust
    ```rust
    // Launch Basic Agent (Specialist with MCP)
    agent_factory.launch_agent(&factory_agent_config, Some(&factory_mcp_runtime_config), AgentType::Specialist).await
    // ...
    // Launch Executor and Planner
    agent_factory.launch_agent(&factory_agent_config_executor, None, AgentType::Executor).await
    agent_factory.launch_agent(&factory_agent_config_planner, None, AgentType::Planner).await
    ```
🚀 Quickstart: Launching Your Factory
You'll need a Rust environment and the LLM API keys set up as described in the main Swarm README.

Prerequisites
*   Install Rust: From rust-lang.org.

*   Set LLM API Keys: You need the environment variables set for your chosen LLM provider (e.g., Groq or Google AI Studio).

    Bash
    ```bash
    # Replace <YOUR-LLM-API-KEY> with your key
    export LLM_A2A_API_KEY=<YOUR-LLM-API-KEY>
    export LLM_PLANNER_API_KEY=<YOUR-LLM-API-KEY> # Can be the same key
    ```
Before running `swarm_factory`, you need to launch the enablers (discovery_service, evaluation_service, memory_service, and a MCP server) from the `swarm` repository.

Bash
```bash
git clone https://github.com/fcn06/swarm.git
cd swarm
./documentation/launch_enablers/run_all_commands.sh
cd ..
```

Step 1: Clone and Build
Bash
```bash
git clone https://github.com/fcn06/swarm_factory.git
cd swarm_factory
cargo build --release
```
Step 2: Run the Demo!
Execute the compiled binary. This will start all services (Discovery, Memory, etc., usually simulated or simple local instances) and then launch the three agents via the AgentFactory.

Bash
```bash
./target/release/swarm_factory
```
You will see output logs indicating the successful setup of services, registration of tasks/tools, and the launch of the Basic_Agent, Executor_Agent, and Planner_Agent.

📚 Learn More
This factory implementation is built upon the robust multi-agent primitives provided by the main Swarm repository:

*   Swarm Framework: https://github.com/fcn06/swarm - Dive into the core components, protocols (MCP, A2A), and agent architecture.

*   Swarm Factory Demo Script: The main Swarm repo also includes a quickstart script for this demo: ./documentation/demo_factory/run_all_commands.sh.

If you have any questions or ideas for extending this demo, please open an issue on the main Swarm repository!
