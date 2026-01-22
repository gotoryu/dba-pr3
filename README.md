# JADE Multi-Agent Christmas Rescue

>This project implements a JADE-based multi-agent system comprising the following autonomous agents: coordinator, intermediary, authority and information provider. The coordinator identifies and navigates to distributed targets on a map by communicating with the authority and informant via a defined protocol. The intermediary enables data exchange between the coordinator and authority, which cannot understand each other immediately. The informant ensures that the positions of the targets are obtained through token-based authorization.

![Java](https://img.shields.io/badge/Java-21-orange) ![JADE](https://img.shields.io/badge/Framework-JADE-blue)
## Overview

This project extends the autonomous navigation system developed in assignment 2 into a sophisticated Multi-Agent System (MAS) using JADE.
For more information on assignment 2 see: https://github.com/gotoryu/dba-pr2.

While the previous homework focused on pathfinding algorithms (A* with limited vision), this assignment implements **FIPA-compliant communication protocols** and coordination between four distinct agent types to solve a complex, distributed task.

## The Scenario
The `Scout` agent is tasked with rescuing reindeer spread across an unknown map. However, the Scout does not know their locations and cannot communicate directly with the mission lead, `Santa`, due to a language barrier. Ultimately, the task was, to implement a communication protocol involving authentication, translation, and information retrieval.

## Multi-Agent Architecture

The system consists of four specialized agents:

1.  **Scout (The Coordinator):**
    * Inherits the A* navigation logic from Assignment 2.
    * Manages the global mission state using a state-machine behavior.
    * Coordinates with all other agents to obtain critical data.
2.  **Santa (The Authority):**
    * Probabilistically decides (80% chance) whether to trust the Scout and grant search permission.
    * Operates in a simulated "Finnish" language, requiring an intermediary (the translator) for communication.
    * Provides a Secret Code upon approval for authentication with other agents.
3.  **Translator (The Intermediary):**
    * Acts as a linguistic bridge between the Scout's "Bro"-slang and Santa's "Finnish".
    * Handles translation requests to maintain the integrity of the communication protocol.
4.  **Rudolph (The Informant):**
    * Holds the secret locations of all reindeer.
    * Only reveals coordinates if the Scout provides the correct Secret Code received from Santa.

## Communication Protocol

The core was the implementation of a robust Finite State Machine within the `RequestSearchBehaviour`:

### Phase 1: Authorization & Translation
1.  **Request:** Scout sends a message to the `Translator`: *"Bro Santa, can I help... En Plan"*.
2.  **Translate:** Translator converts the slang into "Finnish" and returns it to the Scout.
3.  **Negotiate:** Scout forwards the translated request to `Santa`.
4.  **Verification:** Santa replies with `AGREE` (including a Secret Code) with an 80% chance or `REFUSE` with a 20% chance.

### Phase 2: Authentication & Search Loop
1.  **Handshake:** Scout parses the Secret Code and sends it to `Rudolph` to establish a trusted connection.
2.  **Location Fetch:** Scout requests the position of the next reindeer from Rudolph.
3.  **Navigation:** Once coordinates are received, the Scout activates its `WalkBehaviour` to navigate the map using the modified A* from assignment 2.
4.  **Iteration:** This loop continues until Rudolph issues a `CANCEL` performative, signaling that all reindeer have been found.

### Phase 3: Extraction
1.  Scout requests Santa's final position (translated).
2.  Scout navigates to Santa to finalize the mission.

## Technical Features

* **FIPA-Standard:** Implementation of standard *Agent Communication Language* performatives (`REQUEST`, `INFORM`, `AGREE`, `REFUSE`, `CANCEL`).
* **Observer Pattern:** The Scout utilizes a `PositionListener` interface to update the GUI in real-time as it moves across the grid.
* **State Management:** Complex handling of asynchronous message passing using `blockingReceive()` within specific logic states.
* **Decoupled Logic:** Separation of concerns between navigation (A*), environment management, and agent communication.

## Getting Started

### Prerequisites
* Java JDK 21 or higher.
* JADE Framework libraries (included in `lib/` or configured via IDE).

### Installation

1.  **Clone the repository**
    ```bash
    git clone https://github.com/gotoryu/dba-pr3
    ```

2.  **Configuration**
    Ensure the JADE library is added to your classpath. If you are using IntelliJ IDEA for example: Go to: File → Project Structure → Libraries → New Project Library → add your `jade.jar`.

3.  **Run the Simulation**
    Execute the `pr2mapAgent.Main` class.

4.  **Usage**
    * Select a map file.
    * Click on a grass cell to set the **Scout** (start).
    * Click on another cell to set **Santa** (end).
    * Press **Start** to watch the scout collecting all the reindeer.
    * The positions of the reindeer are randomly set.

---

### Comparison with Assignment 2

| Feature | Assignment 2 (Single Agent)          | Assignment 3 (MAS) |
| :--- |:-------------------------------------| :--- |
| **Objective** | Point A to Point B navigation        | Dynamic multi-target rescue mission |
| **Knowledge** | Target coordinates pre-defined       | Coordinates hidden; must be negotiated |
| **Interaction** | Autonomous pathfinding only          | Social interaction & linguistic translation |
| **Complexity** | Algorithmic (A* with limited vision) | Architectural (MAS & Protocols) |