# Metaflow Dynamic Orchestrator (GSoC 2026 PoC)

### 🚀 Overview
The **Metaflow Dynamic Orchestrator** is a technical Proof of Concept (PoC) for my Google Summer of Code 2026 proposal: **"Make Metaflow Jupyter-Native"**. 

Currently, Metaflow forces a file-based execution model on notebooks, requiring users to wrap everything in a single cell using `%%writefile`. This project solves that by decoupling the `FlowSpec` from static files and introducing a **Dynamic Step Registry**.

---

### 🛠️ The Core Engineering Challenge
In standard notebooks, you can't split a Python class definition across multiple cells. However, Data Scientists need to iterate on individual steps (Data Loading, Preprocessing, Training) separately. 

**My Solution:** I built a registry-based engine that captures independent function logic from different cells and "stitches" them into a valid, production-ready Metaflow DAG at runtime.

#### **Technical Highlights:**
* **Source Extraction:** Uses Python's `inspect` module to pull logic directly from the notebook's memory.
* **Automated Indentation:** A custom refactoring logic that handles nested class indentation on the fly (solving the common `IndentationError` in dynamic code).
* **Virtual Scripting:** Generates a temporary execution bridge that injects global dependencies like `pandas`, `sklearn`, and `kagglehub` into the Metaflow context.
* **Subprocess Orchestration:** Executes the full flow as a standalone process to maintain Metaflow's internal state integrity.

---

### 📊 Testing with a Netflix Pipeline
To validate the orchestrator, I didn't just run a "Hello World." I built a multi-cell pipeline using the **Netflix Shows** dataset:

1. **The Start:** Pulled the raw dataset using `kagglehub`. 
2. **The Preprocessor:** Cleaned the data, handled missing values, and applied Label Encoding to the 'type' column to prepare it for modeling.
3. **The Orchestrator:** This engine took these separate memory blocks, fixed the structural syntax, and triggered a successful Metaflow run.

#### **Execution Success Proof:**
The pipeline finished with **Run ID: 1774459213877571**. You can see the logs below where the state (`self.df`) was maintained perfectly across cells:
```text
Workflow starting...
[start/1] Data Loaded: (8807, 12)
[preprocess/2] Preprocessing finished in a separate cell!
[end/3] Metaflow Run Complete!
Done!
