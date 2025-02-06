# Background
The idea behind this article came across my mind when I took the LangGraph course and while working on the first examples, it kept me busy doing some fun work with the Graph workflow. Then I realized how helpful these stateful graphs are and can deliver insight into the flow of requests and queries within a multi agent AI system.

The purpose of this article is to demonstrate a probabilistic analysis on some most common agentic workflows in particular using LangGraph.
Let's consider a general example of a multi agent AI system with an LLM as the decision maker to do the following tasks:
Stateflow of the Agentic AI SystemHere is the breakdown of what is happening inside this workflow:

![image](https://github.com/user-attachments/assets/cf395d9b-440c-4180-8d92-c44f53661ef1)

- The user query is passed to the first node Refine Query 
- Based out of the context, the LLM routes the response to a Search or Generate Summary and 
- Finally the process is complete when the LLM generates the response.

This is a very simple representation of an agentic workflow. In the following, I show a high level representation of this process using LangGraph. 

**Note**: You don't need to call ANY LLMs or APIs to run this workflow. It's all running hypothetical scenarios.

## Building a Hypothetical Agentic Workflow with LangGraph
We mock the above workflow using the following scenario:
- State Representation:
Uses a typed dictionary (TypedDictState) to track core variables (e.g., tools_called, name, condition) along with performance metrics (e.g., steps, node_visits, termination_reason).

- Node Functions:
node_1: Records visits and updates the name.
node_2: Increments tools_called, sets condition to "good", and records its visit. - It represents of a fact that the LLM calls a search function or another tool.
node_3: Similar to node_2 but sets condition to "bad" and marks termination. - It acts like a final sentiment labeling of the generated response and sends it to the user.

- Decision Functions:
decide_condition: Routes 95% to node_2 and 5% to node_3. - node_3 is a bad labeler so chances of visiting this label is low. Better to check with node_2 because this node has a better performance i.e. labels the state as good!
decide_condition_2: Terminates if tools_called > 55, else loops back to node_1. - This condition is set to terminate the operation for a particular number of iterations i.e. enforce reaching the END statement.
Graph Construction:

- Assembles nodes and transitions with LangGraph starting at START and terminating upon reaching an absorbing condition (via node_3 or exceeding tool limits).
Termination:
- Ends when a bad outcome is reached or resource usage exceeds limits, enabling analysis of performance and decision efficiency.

Here is a simple Graph workflow (no LLM or anything expensive needed).

![image](https://github.com/user-attachments/assets/ce3f2c19-d7e2-44ca-be45-f3e1e439205e)
