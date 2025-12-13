
## 5. ML Strategy: Training vs. Fine-Tuning & Tech Stack

### Q1: Should we Train or Fine-tune?
**Answer: "Imitation Learning" (Best of both worlds).**
For a custom physical system like Indian Railways, "Fine-tuning" a generic model (like GPT-4) is **not effective** for core control because LLMs don't understand the physics of trains (acceleration curves, braking distances) accurately enough to prevent collisions.

**The Winning Strategy:**
1.  **Pre-train (Imitation Learning)**: We take your historical data (3-6 months of Control Charts) and train the agent to *clone* the behavior of your best human controllers. This gives the agent a "head start" (it learns to be as good as a human).
2.  **Fine-tune (Reinforcement Learning)**: valid "Training" phase. We let this pre-trained agent practice in the **Digital Twin**. It tries millions of variations to find strategies *better* than the humans (e.g., "What if I slowed down the goods train 2 mins earlier to save energy?").

### Q2: What Data/Parameters do we need?
To make decisions in "real-time", the DSS needs a snapshot of the **State Space**:
*   **Static**: Track layout, Gradient profile, Signal locations (from `knowledge_base`).
*   **Dynamic**: 
    *   `Train_ID`: {Position (km), Speed (km/h), Priority, Length}.
    *   `Signal_State`: {Red, Yellow, Green}.
    *   `Delay`: Current deviation from schedule.

### Q3: How do we give Rules to the Agent?
**NOT via English Prompts.**
We cannot trust an AI to "promise" it won't crash trains. We use **Hard Constraints (The Safety Shield)**.
*   **Mechanism**: A Python function (using the `constraints/` module) checks every action *before* it's applied.
    *   *AI says*: "Send Train A to Line 1."
    *   *Safety Shield says*: "Line 1 is occupied. Request Denied. Penalty Applied."
*   This ensures 100% safety compliance regardless of what the AI "thinks".

### Q4: Tech Stack - LangChain vs. RL?
**Verdict: Do NOT use LangChain for the core logic.**
LangChain is for building Chatbots (LLMs). It is too slow and hallucination-prone for real-time safety-critical railway control.

**Recommended Stack:**
*   **Simulation Environment**: `Gymnasium` (standard interface) + `SimPy` (for discrete event physics).
*   **AI Agent**: `Stable-Baselines3` (PPO Algorithm) or `Ray RLLib`.
*   **Rule Engine**: `Google OR-Tools` (Constraint Solver) or pure Python logic.
*   **User Interface (The Co-Pilot)**: *Here* you can use an LLM (potentially via LangChain) to explain the decision to the user in English ("I moved this train because...").

### Q5: Phase 1 Success Metrics - How do we measure "Accuracy" and "Improvement"?
To prove the model is superior to the current manual system, we track two distinct sets of metrics:

#### A. Prediction Accuracy (Digital Twin Validity)
*How well does our simulation match reality?*
1.  **Arrival Time Error (MAE)**: Difference between *Simulated Arrival* and *Actual Arrival* for a train (Target: < 2 mins).
2.  **Conflict Prediction Recall**: If a conflict happened in real life, did our model predict it? (Target: > 95%).

#### B. Operational Improvement (Optimization Goals)
*How much better is the AI's schedule compared to the historical human schedule?*
1.  **Total Delay Minutes**: Sum of all delays for all trains. (Goal: Reduce by 10-15%).
2.  **Section Capability (Throughput)**: Number of trains successfully cleared per hour. (Goal: Increase by ~10%).
3.  **Energy Consumption**: Estimated function of `Speed * Acceleration`. Smoother profiles = less energy. (Goal: Reduce by 5-8%).
4.  **Priority Adherence**: % of high-priority trains (e.g., Vande Bharat) kept on time vs. freight.
