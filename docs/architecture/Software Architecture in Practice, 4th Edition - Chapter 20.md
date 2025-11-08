# 20. Designing an Architecture

With Humberto Cervantes

> A designer knows he has achieved perfection not when there is nothing left to add, but when there is nothing left to take away.
>
> — *Antoine de Saint-Exupéry*

Design—including architectural design—is a complex activity to perform. It involves making a myriad of decisions that take into account many aspects of a system. In the past, this task was only entrusted to senior software engineers—gurus—with decades of hard-won experience. A systematic method provides guidance in performing this complex activity so that it can be learned and capably performed by mere mortals.

In this chapter we provide a detailed discussion of a method—**Attribute-Driven Design (ADD)**—that allows an architecture to be designed in a systematic, repeatable, and cost-effective way. Repeatability and teachability are the hallmarks of an engineering discipline. To make a method repeatable and teachable, we need a set of steps that any suitably trained engineer can follow.

We begin by providing an overview of ADD and its steps. This overview is followed by more detailed discussions of some of the key steps.

---

## 20.1 Attribute-Driven Design

Architectural design for software systems is no different than design in general: It involves making decisions, and working with the available materials and skills, to satisfy requirements and constraints. In architectural design, we turn decisions about architectural drivers into structures, as shown in Figure **20.1**. Architectural drivers comprise architecturally significant requirements (**ASRs**—the topic of **Chapter 19**), but also include functionality, constraints, architectural concerns, and design purpose. The resulting structures are then used to guide the project in the many ways we laid out in **Chapter 2**: They guide analysis and construction. They serve as the foundation for educating a new project member. They guide cost and schedule estimations, team formation, risk analysis and mitigation, and, of course, implementation.

---XXX---

Figura 20.1 Overview of the architecture design activity

---XXX---

Prior to starting architecture design, it is important to determine the **scope of the system**—what is inside and what is outside of the system you are creating, and which external entities the system will interact with. This context can be represented using a **system context diagram**, like that shown in **Figure 20.2**. Context diagrams are discussed in more detail in **Chapter 22**.

## Figura 20.2 Example of a system context diagram

---XXX---

In **ADD**, architecture design is performed in **rounds**, each of which may consist of a series of design **iterations**. A round comprises the architecture design activities performed within a development cycle. Through one or more iterations, you produce an architecture that suits the established design purpose for this round.

Within each iteration, a series of design steps is performed. **ADD** provides detailed guidance on the steps that need to be performed inside each iteration. **Figure 20.3** shows the steps and artifacts associated with **ADD**. In the figure, steps 1-7 constitute a round. Within a round, steps 2-7 constitute one or more iterations within a round. In the following subsections, we provide an overview of each of these steps.

---XXX---
