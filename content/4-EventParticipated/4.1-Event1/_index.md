---
title: "Event 1"
date: 2026-05-09
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---

# "FCAJ Community Day" Workshop Report

### Event Objectives

* Share scientific methods to build motivation and maintain discipline in self-learning (Dopamine management).
* Provide guidance on Ultimate Prompt Engineering techniques to maximize productivity when working with LLMs.
* Align design thinking, career roadmaps, and real-world recruitment criteria for 3rd and 4th-year IT students.
* Introduce the BMX software development framework (BMM method) to efficiently deploy AI Agents and mitigate hallucination risks.

### Speaker

* **Mr. Long** - Specialist on Brain-Hacking Techniques.
* **Mr. Thinh** - Specialist on Ultimate Prompt Engineering.
* **Mr. Khang** - Solutions Architect at Cloud Kinetics (Over 3 years of experience in recruitment and tech engineering career mentorship).
* **Ms. Thao** - Software Developer at Vietnam International Bank (VIB).

### Key Highlights

#### The "Brain-Hacking" Method: Hooked on Learning like Gaming/Social Media

* **Dopamine Mechanism**: Dopamine is not generated upon receiving a reward; it spikes highest when the brain *anticipates an upcoming reward*. Social media algorithms (such as TikTok) capitalize on this by leveraging curiosity (not knowing what the next video will be) to keep users hooked.
* **Educational Application via 4 Pillars**: Curiosity → Experimentation → Discovery → Satisfaction.
* **Core Techniques**:
* *Loss Aversion*: Utilize a physical calendar or an app to track consecutive daily study streaks (similar to Duolingo or TikTok streaks). The psychological resistance to breaking a long-running streak compels individuals to maintain study habits, even if only for 5–10 minutes a day.
* *Bypassing the Amygdala*: The brain inherently exhibits fear and procrastination when confronted with massive volumes of knowledge. Overcome this by breaking goals down into micro-tasks (e.g., instead of trying to study AWS for 5 hours straight, focus on a single service for 10–20 minutes).
* *The 2-Minute Rule*: If a task can be completed within 2 minutes (e.g., opening an assignment file, reading a single page), execute it immediately to bypass the initial barrier of procrastination.
* *Feedback Loops & Gamification*: Design a personal progress system tracking Experience Points (XP), tiers (Bronze, Silver, Gold, Orange ranks identical to the FCJ system), and prepare mystery reward boxes to be drawn randomly after study sessions to stimulate dopamine.



#### Ultimate Prompt Engineering Techniques

* **7 Components of a Production-Ready Prompt**: Includes Role, Instruction, Context, Input Data, Output Format, Examples, and Constraints.
* **Token Optimization**: Tokens represent the subwords processed by an LLM. It is crucial to note that Vietnamese text typically consumes twice as many tokens as English when passed through an encoder (Tokener), directly increasing enterprise API operating costs.
* **Advanced Reasoning Architectures**:
* *Chain of Thought (CoT)*: Explicitly directs the AI to process its reasoning step-by-step to improve output accuracy.
* *Tree of Thought (ToT)*: Formulates a cognitive tree structure combined with search algorithms (DFS/BFS) to allow the AI to self-evaluate and determine the optimal solution path.
* *Self-Consistency*: Executes multiple independent reasoning paths simultaneously and samples the majority consensus.


* **Case Study (Prompt Optimizer Project)**: A real-world production project automated to optimize prompts running fully on a serverless AWS infrastructure. The stack consists of: CloudFront + S3 (Frontend/CDN), Amazon Cognito (User/JWT management), API Gateway + AWS Lambda (Backend traffic routing and core business logic execution), DynamoDB (Prompt history storage delivering single-digit millisecond latency), and Amazon Bedrock for secure access to Foundation Models like Claude 3.5 Sonnet.

#### Career Orientation for Students ("Why Aren't You Employed Yet?")

* **AI Amplification Effect**: AI magnifies deficiencies in unproductive engineers (resulting in copied-and-pasted faulty code without conceptual understanding), yet it yields 2x to 10x productivity gains for proficient engineers. Hiring managers lean heavily toward an applicant scoring 6–7 who demonstrates independent reasoning over a 9–10 score submission generated via AI where the candidate fails to articulate the underlying technical choices.
* **"Why" over "What" Framework**: Shift focus away from merely completing assignments (the "What") toward continuously questioning systemic architecture: "Why was this specific database or service chosen?" or "What are the architectural trade-offs?". Apply the "5 Whys" methodology to diagnose the root cause of engineering problems.
* **Integrity & Long-Term Vision**: Faced with 10 base project requirements, an engineer acting with integrity will map out and handle 20–30 edge cases autonomously. This standard of work remains constant regardless of managerial supervision. Prioritize long-term experiential investments over immediate short-term entry-level compensation packages.
* **The 3-Circle Career Model & 5 Pillars of Benefits**: An optimal career position aligns: What you love, What you must execute per enterprise standards, and Benefits received. The 5 benefit dimensions of a career include: Salary, Experience, Network, Knowledge, and Promotion.
* **The Core 5-Factor Candidate Evaluation Model**: Attitude (Priority #1 for Freshers), Education, Experience, Exposure, and Talent.

#### The BMX Software Development Framework (BMM Method) via AI Agents

* **Definition**: BMX (Through Method for Agile Development) is an open-source framework designed to standardize AI interaction workflows by mapping traditional Agile structures into independent, specialized AI Agent roles (PM, Architect, PO, Scrum Master, Developer, Reviewer/QA).
* **Mitigating Hallucination**: Flooding a single chat interface with excessive, unstructured constraints expands the Context Window, which causes semantic drift and leads the LLM to hallucinate or generate corrupted code. BMX addresses this by breaking down Product Requirement Documents (PRDs) and System Architecture schemas into modular, isolated Epics and Stories.
* **Operational Lifecycle**: Story tasks are rigorously managed by human operators through clear state transitions (Draft → Approved → Review → Done). The Developer Agent automatically parses and builds code exclusively for items marked as Approved. Following code generation, the Reviewer and QA Agents enter an iterative testing cycle until the codebase reaches a zero-defect state. Core Philosophy: You do not manage source code directly; you manage precise documentation, and the AI agent infrastructure compiles the optimal source code based on it.

### Key Takeaways

#### Design Thinking

* **Foundation-First Approach**: Rather than attempting to superficially master all 200–300 AWS services, focus on anchoring deep conceptual knowledge across 5 core foundational services to confidently scale architectures.
* **Critical Architectural Review**: Deploy structured thought processes to challenge AI-generated suggestions via relentless "Why" inquiries, avoiding passive acceptance of default LLM answers to eliminate hallucination traps.

#### Technical Architecture

* Mastered the application of the **7 essential prompt engineering components** within everyday engineering workflows to significantly elevate LLM response quality.
* Acquired a clear understanding of LLM operational costs linked to **token allocation**, paying careful attention to the 2x cost overhead incurred by Vietnamese text parsing relative to English.
* Learned to **orchestrate multiple AI Agents** utilizing the BMX framework by isolating system modules and wrapping context windows, allowing specialized agents to execute dedicated tasks without causing regressions in adjacent features.

#### Modernization Strategy

* **Document-Driven Development Strategy**: Transitioned technical execution away from manual code compilation toward constructing immaculate, production-grade documentation, thereby maximizing the automated generation capabilities of AI Agents.
* **Long-Term Career Strategy**: Embraced mistakes as key metrics of professional growth and adjusted career objectives toward a sustainable, long-term vision rather than optimizing entirely for short-term financial returns.

### Practical Applications

* **Re-Engineering Daily Prompts**: Restructure all AI interactions by universally enforcing the 7-component prompt structure (Role, Instruction, Context, Constraints...) to generate deterministic, high-quality results from Gemini/ChatGPT.
* **Injecting "Why" Thinking into Active Tasks**: Map out and systematically debug undocumented edge cases for ongoing feature tickets to reinforce personal code integrity.
* **Xây dựng chuỗi kỷ luật tự học**: Sử dụng phương pháp gạch lịch/tích chuỗi (Loss Aversion) kết hợp quy tắc 2 phút để duy trì thói quen học tập các công nghệ mới (như các dịch vụ AWS Lambda, Bedrock) ít nhất 15-30 phút mỗi ngày.
* **Evaluating the BMX Framework**: Clone and experiment with the BMX repository from GitHub on personal or group assignments to benchmark modular task distribution and AI-driven codebase management.
* **Maximizing Team Collaboration**: Actively contribute and present findings within public tech meetup communities to polish technical articulation, handle peer review, and scale professional networks.

### Personal Event Experience

Attending the **“FCAJ Community Day”** workshop proved to be an invaluable experience. It simultaneously pushed my technical perspective (Advanced Prompt Engineering, AI Agents, Serverless) and provided practical, grounded career advice from seasoned industry veterans. Key highlights included:

#### Learning from High-Caliber Speakers

* Engaging with the unfiltered, real-world experiences shared by Mr. Khang (Cloud Kinetics) and Ms. Thao (VIB) regarding enterprise dynamics. Their transparency on how recruiters filter applicants dismantled several misconceptions I held about GPA metrics and the superficial usage of AI in take-home assignments.

#### Practical Engineering Exposure

* Observing the live architecture demo of the serverless Prompt Optimizer running directly on Amazon Bedrock, DynamoDB, and Lambda.
* Breaking down complex prompt workflows like Tree of Thought (ToT) and Chain of Thought (CoT) via clean, step-by-step step-by-step structural diagrams.
* Demystifying the operational pipeline of multi-agent orchestration via the BMX framework, showing how task scoping isolates context boundaries from Planning down to Review/QA.

#### Deploying Modern Tooling

* Learning to leverage AI as a productivity multiplier instead of falling victim to the AI Amplification Effect. The sessions clarified how to maximize AI for high-level brainstorming and ideological expansion while retaining absolute ownership of the core system architecture.

#### Networking and Collaboration

* The venue fostered an inclusive environment that actively encouraged student inquiries and peer debate. The meetup strongly motivated me to share insights openly with the broader community without fearing mistakes, as "errors are simply natural checkpoints on the path to long-term exposure."

#### Core Lessons

* Professional excellence is not achieved by the naturally gifted, but rather by disciplined individuals who deliberately architect an optimal environment to support sustained daily learning.
* AI will not replace software engineers; however, software engineers who master core engineering principles and manage AI workflows (via advanced prompting and multi-agent systems) will inevitably replace those who do not.
* Commit to uncompromised integrity in engineering, ground every architecture decision by asking "Why", and evaluate personal career milestones through a long-term lens.

#### Event Evidence Images
#### Event Evidence Images

- ![Event Participation Evidence Image](</AWS-HUTECHintership-report/images/4-EventParticipated/4.1-Event1/event1(0).jpg>)
- ![Event Participation Evidence Image](</AWS-HUTECHintership-report/images/4-EventParticipated/4.1-Event1/event1(1).jpg>)
> Overall, this workshop went far beyond imparting technical competencies on Prompt Engineering and AI-centric development methodologies; it effectively ignited professional drive, reinforced self-learning discipline, and provided a clear career compass for the next generation of software builders.