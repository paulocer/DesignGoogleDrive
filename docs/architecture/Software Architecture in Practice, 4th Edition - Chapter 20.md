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

<p align="center">
  <img src="https://github.com/paulocer/DesignGoogleDrive/blob/main/Figure%2020.1.png" alt="Figura 20.1 Overview of the architecture design activity"/>
</p>

**Figura 20.1** Overview of the architecture design activity

Prior to starting architecture design, it is important to determine the **scope of the system**—what is inside and what is outside of the system you are creating, and which external entities the system will interact with. This context can be represented using a **system context diagram**, like that shown in **Figure 20.2**. Context diagrams are discussed in more detail in **Chapter 22**.

<p align="center">
  <img src="https://github.com/paulocer/DesignGoogleDrive/blob/main/Figure%2020.2.png" alt="Figura 20.2 Example of a system context diagram"/>
</p>

**Figura 20.2** Example of a system context diagram

In **ADD**, architecture design is performed in **rounds**, each of which may consist of a series of design **iterations**. A round comprises the architecture design activities performed within a development cycle. Through one or more iterations, you produce an architecture that suits the established design purpose for this round.

Within each iteration, a series of design steps is performed. **ADD** provides detailed guidance on the steps that need to be performed inside each iteration. **Figure 20.3** shows the steps and artifacts associated with **ADD**. In the figure, steps 1-7 constitute a round. Within a round, steps 2-7 constitute one or more iterations within a round. In the following subsections, we provide an overview of each of these steps.

<p align="center">
  <img src="https://github.com/paulocer/DesignGoogleDrive/blob/main/Figure%2020.3.png" alt="Figura 20.2 Example of a system context diagram"/>
</p>

**Figura 20.3** Steps and artifacts of ADD

In ADD, **architecture design** is performed in **rounds**, each of which may consist of a series of **design iterations**. A round comprises the architecture design activities performed within a development cycle. Through one or more iterations, you produce an architecture that suits the established design purpose for this round.

Within each iteration, a series of design steps is performed. ADD provides detailed guidance on the steps that need to be performed inside each iteration. Figure 20.3 shows the steps and artifacts associated with ADD. In the figure, steps 1–7 constitute a round. Within a round, steps 2–7 constitute one or more iterations within a round. In the following subsections, we provide an overview of each of these steps.

## 20.2 The Steps of ADD The sections that follow describe the steps for ADD.

### Step 1: Review Inputs

Before starting a design round, you need to ensure that the architectural drivers (the inputs to the design process) are available and correct. These include:

The purpose of the design round

The primary functional requirements

The primary quality attribute (QA) scenarios

Any constraints

Any concerns

Why do we explicitly capture the design purpose? You need to make sure that you are clear about your goals for a round. In an incremental design context comprising multiple rounds, the purpose for a design round may be, for example, to produce a design for early estimation, to refine an existing design to build a new increment of the system, or to design and generate a prototype to mitigate certain technical risks. In addition you need to know the existing architecture's design, if this is not greenfield development.

At this point, the primary functionality—typically captured as a set of use cases or user stories—and QA scenarios should have been prioritized, ideally by your most important project stakeholders. (You can employ several different techniques to elicit and prioritize them, as discussed in Chapter 19). You, the architect, must now "own" these. For example, you need to check whether any important stakeholders were overlooked in the original requirements elicitation process, and whether any business conditions have changed since the prioritization was performed. These inputs really do "drive" design, so getting them right and getting their priority right are crucial. We cannot stress this point strongly enough. Software architecture design, like most activities in software engineering, is a "garbage-in-garbage-out" process. The results of ADD cannot be good if the inputs are poorly formed.

The drivers become part of an architectural design backlog that you should use to perform the different design iterations. When you have made design decisions that account for all of the items in the backlog, you've completed this round. (We discuss the idea of a backlog in more depth in Section 20.8.)

Steps 2–7 make up the activities for each design iteration carried out within this design round.

### Step 2: Establish Iteration Goal by Selecting Drivers

Each design iteration focuses on achieving a particular goal. Such a goal typically involves designing to satisfy a subset of the drivers. For example, an iteration goal could be to create structures from elements that will allow a particular performance scenario, or a use case to be achieved. For this reason, when performing design activities, you need to establish a global goal before you start a particular design iteration.

### Step 3: Choose One or More Elements of the System to Refine

Satisfying drivers requires you to make architectural design decisions, which then manifest themselves in one or more architectural structures. These structures are composed of interrelated elements—modules and/or components, as defined in Chapter 1—and these elements are generally obtained by refining other elements that you previously identified in an earlier iteration. Refinement can mean decomposition into finer-grained elements (top-down approach), combination of elements into coarser-grained elements (bottom-up approach) or improvement of previously identified elements. For greenfield development, you can start by establishing the system context and then selecting the only available element—that is, the system itself—for refinement by decomposition. For existing systems or for later design iterations in greenfield systems, you normally choose to refine elements that were identified in prior iterations.

The elements that you will select are the ones involved in the satisfaction of specific drivers. For this reason, when the design addresses an existing system, you need to have a good understanding of the elements that are part of the as-built architecture of the system. Obtaining this information might involve some “detective work,” reverse engineering, or discussions with developers.

In some cases, you may need to reverse the order of steps 2 and 3. For example, when working on a greenfield system or when fleshing out high-level reference architectures, you will, at least in the early stages of design, focus on elements of the system and start the iteration by selecting a particular element and then considering the drivers that you want to address.

### Step 4: Choose One or More Design Concepts That Satisfy the Selected Drivers

Choosing the design concept(s) is probably the most difficult decision you will face in the design process, because it requires you to identify the various design concepts that might plausibly be used to achieve your iteration goal, and to then make a selection from these alternatives. Many different types of design concepts are available—for example, tactics, patterns, reference architectures, and externally developed components—and, for each type, many options may exist. This can result in a considerable number of alternatives that need to be analyzed before making the final choice. In Section 20.3, we discuss the identification and selection of design concepts in more detail.

### Step 5: Instantiate Architectural Elements, Allocate Responsibilities, and Define Interfaces

Once you have selected one or more design concepts, you must make another type of design decision: how to instantiate elements out of the design concepts that you just selected. For example, if you selected the layers pattern as a design concept, you must decide how many layers will be used, and their allowed relationships, since the pattern itself does not prescribe these.

After instantiating the elements, you then need to allocate responsibilities to each of them. For example, in an app, at least three layers are usually present: presentation, business, and data. The responsibilities of these layers differ:

The responsibilities of the presentation layer include managing all of the user interactions.

The business layer manages application logic and enforces business rules.

The data layer manages the persistence and consistency of data.

Instantiating elements is only one part of creating structures that satisfy a driver or a concern. The elements that have been instantiated also need to be connected, thereby allowing them to collaborate with each other. This requires the existence of relationships between the elements and the exchange of information through some kind of interface. The interface is a contractual specification indicating how information should flow between the elements. In Section 20.4, we present more details on how the different types of design concepts are instantiated, how relationships are created, and how interfaces are defined.

### Step 6: Sketch Views and Record Design Decisions

At this point, you have finished performing the design activities for the iteration. However, you may have not taken any actions to ensure that the views—the representations of the structures you created—are preserved. For instance, if you performed step 5 in a conference room, you probably ended up with a series of diagrams on a whiteboard. This information is essential to the rest of the process, and you must capture it so that you can later analyze and communicate it to other stakeholders. Capturing the views may be as simple as taking a picture of the whiteboard.

The views that you have created are almost certainly not complete; thus, these diagrams may need to be revisited and refined in a subsequent iteration. This is typically done to accommodate elements resulting from other design decisions that you will make to support additional drivers. This is why we speak of “sketching” the views in ADD, where a “sketch” refers to a preliminary type of documentation. The more formal, more fully fleshed-out documentation of these views—should you choose to produce it (see Chapter 22)—occurs only after the design iterations have been finished (as part of the architectural documentation activity).

In addition to capturing the sketches of the views, you should record the significant decisions made in the design iteration, as well as the reasons that motivated those decisions (i.e., the rationale), to facilitate later analysis and understanding of the decisions. For example, decisions about important tradeoffs should be recorded at this time. During a design iteration, decisions are primarily made in steps 4 and 5. In Section 20.5, we explain how to create preliminary documentation during the design process, including recording design decisions and their rationale.

### Step 7: Perform Analysis of Current Design and Review Iteration Goal and Achievement of Design Purpose

By step 7, you should have created a partial design that addresses the goal established for the iteration. Making sure that this is actually the case is a good idea, to avoid unhappy stakeholders and later rework. You can perform the analysis yourself by reviewing the sketches of the views and design decisions that you captured, but an even better idea is to have someone else help you review this design. We do this for the same reason that organizations frequently have a separate testing/quality assurance group: Another person will not share your assumptions, and will have a different experience base and a different perspective. This diversity helps to find “bugs,” in both code and architecture. We discuss architectural analysis in more depth in Chapter 21.

Once the design performed in the iteration has been analyzed, you should review the state of your architecture in terms of your established design purpose. This means considering if, at this point, you have performed enough design iterations to satisfy the drivers that are associated with the design round. It also means considering whether the design purpose has been achieved or if additional design rounds are needed in future project increments. In Section 20.6, we discuss simple techniques that allow you to keep track of design progress.

### Iterate If Necessary

You should perform additional iterations and repeat steps 2–7 for every driver that was considered. More often than not, however, this kind of repetition will not be possible because of time or resource constraints that force you to stop the iteration even with one or two implementation.

What are you therefore evaluating if more design iterations are necessary? Let’s take a step back. You should at least have addressed the drivers with the highest priority. Ideally, you should have certainty that critical drivers are satisfied or, at least, that design is “good enough” to satisfy them.

## 20.3 More on ADD Step 4: Choose One or More Design Concepts

Most of the time you, as an architect, don’t need to, and should not, reinvent the wheel. Rather, your major design activity is to identify and select design concepts to meet the most important challenges and address the key drivers across the design iterations. Design is still an original and creative endeavor, but the creativity resides in the appropriate identification of these existing solutions, followed by combining and adapting them to the problem at hand. Even with an existing corpus of solutions to choose from—and we are not always blessed with a rich corpus—this is still the hardest part of design.

### Identification of Design Concepts

The identification of design concepts might appear daunting, because of the vast number of options available. There are likely dozens of design patterns and externally developed components that you could use to address any particular issue. To make things worse, these design concepts are scattered across many different sources: in practitioner blogs and websites, in research literature, and in books. Moreover, in many cases, there is no canonical definition of a concept. Different sites, for example, will define the broker pattern in different, largely informal ways. Finally, once you have identified the alternatives that can potentially help you achieve the design goals of the iteration, you need to select the best one(s) for your purposes.

To address a specific design problem, you can and often will use and combine different types of design concepts. For example, to build a security driver, you might employ a security pattern, a security tactic, a security framework, or some combination of these.

Once you have more clarity regarding the types of design concepts that you wish to use, you still need to identify alternatives—that is, design candidates. You can achieve this in several ways, although you will probably use a combination of these techniques rather than a single method:

- Leverage existing best practices. You can identify alternatives by making use of existing catalogs. Some design concepts, such as patterns, are extensively documented; others, such as externally developed components, are documented in a less thorough way. The benefits of this approach is that you can identify many alternatives and leverage the considerable knowledge and experience of others. The downsides are that searching and studying the information can require a considerable amount of time, the quality of the documented knowledge is often unknown, and the assumptions and biases of the authors are also unknown.

- Leverage your own knowledge and experience. If the system you are designing is similar to other systems you have designed in the past, you will probably want to begin with some of the design concepts that you have used before. The benefit of this approach is that the identification of alternatives can be performed rapidly and confidently. The downside is that you may end up using the same ideas repeatedly, even if they are not the most appropriate for all the design problems that you are facing, or if they have been superseded by newer, better approaches. As the saying goes: If all you have is a hammer, all the world looks like a nail.

- Leverage the knowledge and experience of others. As an architect, you have a background and knowledge that you have gained through the years. This background and knowledge will vary from person to person, especially if the types of design problems they have addressed in the past differ. You can leverage this information by performing the identification and selection of design concepts with some of your peers through brainstorming.

## Selection of Design Concepts

Once you have identified a list of alternative design concepts, you need to select which one of the alternatives is the most appropriate to solve the design problem at hand. You can achieve this in a relatively simple way, by creating a table that lists the pros and cons associated with each alternative and selecting one of the alternatives based on those criteria and your drivers. The table can also contain other criteria, such as the cost associated with the use of the alternative. Methods such as SWOT (strengths, weaknesses, opportunities, threats) analysis can help you make this decision.

When identifying and selecting design concepts, keep in mind the constraints that are part of the architectural drivers, because some constraints will restrict you from selecting particular alternatives. For example, a constraint might be that all libraries and frameworks must employ an approved license. In that case, even if you have found a framework that could be useful for your needs, you may need to discard it if it does not carry an approved license.

You also need to keep in mind that the decisions regarding the selection of design concepts that you made in previous iterations may restrict the design concepts that you can now select due to incompatibilities. An example would be selecting a web architecture in an initial iteration and then selecting a user interface framework for local applications in a subsequent iteration.

## Creation of Prototypes

In case the previously mentioned analysis techniques do not guide you to make an appropriate selection of design concepts, you may need to create prototypes and collect measurements from them. Creating early “throwaway” prototypes is a useful technique to help in the selection of externally developed components. This type of prototype is usually created without consideration for maintainability, reuse, or allowance for achieving other important goals. Such a prototype should not be used as a basis for further development.

Although the creation of prototypes can be costly, certain scenarios strongly motivate them. When thinking about whether you should create a prototype, ask these questions:

- Does the project incorporate emerging technologies?

- Is the technology new in the company?

- Are there certain drivers, particularly QAs, whose satisfaction using the selected technology presents risks (i.e., it is not understood whether they can be satisfied)?

- Is there a lack of trusted information, internal or external, that would provide some degree of certainty that the selected technology will be useful to satisfy the project drivers?

- Are there configuration options associated with the technology that need to be tested or understood?

- Is it unclear whether the selected technology can be easily integrated with other technologies that are used in the project?

- If most of your answers to these questions are “yes,” then you should strongly consider the creation of a throwaway prototype.

## To Prototype or Not to Prototype?

It is often the case that architectural decisions must be made with imperfect knowledge. But what's the best way to go: a team could run a series of experiments (such as building prototypes) to try to reduce their uncertainty about which path to follow. The problem is that such experiments could carry a substantial cost, and the conclusions drawn from them might not be definitive.

For example, suppose a team needs to decide whether the system they are designing should be based on a traditional three-tier architecture or should be microservices, but they are not confident about that approach. They do a coarse estimation (within two alternatives, not project that the cost of developing the three-tier architecture would be $500,000 and that of developing the microservices would be $650,000. If, having developed the three-tier architecture, the team later concluded that the wrong architecture was chosen, the estimated refactoring cost would be $300,000. If the microservices architecture was the first one developed, and a later refactoring was needed, its estimated refactoring cost would be $150,000.

What should the team do?

To decide whether it is worth it to conduct the experiments, or how much we should be willing to pay for them, we can follow this reasoning: If experiments can be gained and the cost of being wrong, the team could use a technique known as Value of Information (VoI) to tackle the question. The VoI technique is used to calculate the expected gain from a reduction in the uncertainty surrounding a decision through some form of data collection exercise: in this case, the construction of prototypes. To use VoI, the team will need to assess the following parameters: the cost of making the wrong design choice, the cost of performing the experiments, the team's level of confidence in each design choice, and their level of confidence in the results of the experiments. Using these estimates, VoI then applies Bayes's Theorem to calculate two quantities: the expected value of perfect information (EVPI) and the expected value of sample information (EVSI). If the experiments can be conducted, the team should be willing to pay for the experiments, were they to provide definitive results (i.e., no false positives or false negatives). EVPI represents how much one should be willing to spend knowing that the results of the experiment might not identify the right solution with 100 percent certainty.

As these results represent expected values, they should be evaluated in the larger context of a risk.

—Eduardo Miranda

# 20.4 More on ADD Step 5: Producing Structures

Design concepts per se won't help you satisfy your drivers unless you produce *structures*; that is, you need to identify and connect elements that are derived from the selected design concepts. This is the "instantiation" phase for architectural elements in ADD: creating elements and relationships between them, and associating responsibilities with these elements. Recall that the architecture of a software system is composed of a set of structures. As we saw in Chapter 1, there are three are three major categories:

- Module structures, which are composed of elements that exist at development time such as files, modules, and classes
- Component and connector (C&C) structures, which are composed of elements that exist at runtime (such as processes and threads)
- Allocation structures, which are composed of both software elements (from a module or C&C structure) and non-software elements (a target or deployed file system, development and at runtime, such as file systems, hardware, and development teams).

When you instantiate a design concept, you may actually affect more than one structure (by exercising for example, in a particular iteration you might instantiate the patterns redundancy (warm spare) pattern, introduced in Chapter 7. This will result in both a C&C structure and an allocation structure. A part of applying this pattern is identifying which component(s) should be replicated. If the redundant warm spare is kept consistent with that of the active node, a mechanism (for managing and transferring state, and a mechanism for detecting the failure of a node. These decisions are responsibilities that must live somewhere in the elements of a module structure.

## Instantiating Elements

Here's how instantiation might look for each of the design concept categories:

- *Reference architectures*. In the case of reference architectures, instantiation typically means that you perform some sort of customization. This will involve adding, removing, and modifying the elements and relationships defined by the reference architecture. For example, if you are designing a web application that needs to communicate with an external application to handle payments, you will probably need to add an integration component alongside the traditional presentation, business, and data tiers.
- *Patterns*. Patterns provide a generic structure composed of elements, along with their relationships and their responsibilities. As the structure is generic, you will need to adapt it to your specific problem. Instantiation usually involves transforming the generic structure defined by the pattern into a specific one that is adapted to the needs of the problem you are solving. For example, consider the client-server architectural pattern. It establishes the basic elements of computation (i.e., clients and servers) and their relationships (i.e., connection and communication), but does not specify how many clients or servers you should use for your problem, or what the functionality of each should be, or which clients should talk to which servers or which communication protocol they should use. Resolution is totally up to the analyst.
- *Tactics*. This design concept does not prescribe a particular structure. Thus, to instantiate a tactic, you may apply a different type of design concept (that you're already using) to realize the tactic. Alternatively, you may utilize a design concept that, without any need for adaptation, already realizes the tactic. For example, you might: (1) select a security tactic of *authenticating actors* that you realize it through a session-cookie-calculation you weave into your preexisting login process; or (2) adopt a security pattern that includes actor authentication; or (3) integrate an externally developed component such as a security framework that authenticates actors.
- *Externally* developed components. The instantiation of these components may or may not imply the creation of new elements. For example, in the case of object-oriented frameworks, instantiation may require you to create new classes that inherit from the base classes defined in the framework. This will result in new elements. An example that does not involve the creation of new elements is one where you deploy an existing library or framework in the context of threads in a thread-pool.

## Associating Responsibilities and Identifying Properties

When you are creating elements by instantiating design concepts, you need to consider the responsibilities that are allocated to these elements. For example, if you instantiate the microservices architecture pattern (Chapter 5), you need to decide what the microservices will do, how many of them you will deploy, and what the properties of those microservices will be. When instantiating elements and associating responsibilities, you should also consider how the design principles that elements should honor (ideally, but not always, are defined by a narrow set of compatibility, usability, and performance requirements).

An important aspect that you need to consider when instantiating design elements is the documentation of the elements that you create. What is the configuration options, state/data, resource management, priority, or even hardware characteristics (if the elements that you created are physical nodes) of the chosen technologies. Identifying these properties supports analysis and the documentation of your design rationale.

## Establishing Relationships between the Elements

The creation of structures also requires making decisions with respect to the relationships that exist between the elements and their properties. Consider again the client-server pattern. In instantiating this pattern, you need to decide which clients will talk to which servers, via which ports and protocols. You also need to decide whether communication occurs (e.g., synchronous or asynchronous). How do initiates interactions? How much information is transferred and at what rate?

These design decisions can have a significant impact on your project in areas like QAs such as performance.

## Defining Interfaces

Interfaces establish a contractual specification that allows elements to collaborate and exchange information. They may be either external or internal.

External interfaces are interfaces of other systems with which your system must interact. These may form constraints in your system, since you usually cannot influence their specification. As we noted earlier, encapsulating a system consists at the beginning of the design process is used to identify external interfaces. Since external entities and the system under development exchange interfaces, there should be at least one external interface per external system (or above). In bigger systems, there can be many.

Internal interfaces are interfaces between the elements that result from the instantiation of design concepts. To identify the relationships and the interface details, you need to understand how the elements interact with each other to support use cases or QA scenarios. As we said in Chapter 15 in our discussion of software interfaces, "interface" means any thing one element does that impacts the processing of another element. A particularly common type of interaction is the runtime exchange of information.

Behavioral representations such as UML sequence diagrams, statecharts, and activity diagrams (see Chapter 22) allow you to record the information that is exchanged between elements during execution. This type of analysis is also useful to identify relationships between elements: If two elements need to exchange information during use or startup and they have a relationship between these elements exists. Any information that is exchanged becomes part of the definition of the interface.

The identification of interfaces is usually not performed equally across all design iterations. When you are starting the design of a greenfield system, for example, you will not hesitate will precise definition of interfaces across elements; interfaces will then be refined in later iterations. The interfaces of abstract elements will be intentionally underspecified. For example, in an early iteration you might simply specify that the HTTP client "communicates" to the business logic tier, and the business logic tier sends "results" back. As the design process proceeds, and particularly when you are starting the construction phase, use cases and QA scenarios, you will need to refine the interfaces of the elements that participate in these interactions.

In some special cases, identifying the appropriate interfaces may be greatly simplified. For example, if you choose a compiler technology stack or a set of components that have been designed to interoperate, then the interface will already be defined by those technologies. In such a case, the specification of interfaces is a relatively trivial task, as the chosen technologies have "baked in" many interface assumptions and decisions.

Finally, be aware that not all of the internal interfaces need to be identified in any given ADD iteration. Some may be delegated to later design activities.

## 20.5 More on ADD Step 6: Creating Preliminary Documentation during the Design

As we will see in Chapter 22, software architecture is documented as a set of views, which represent the different structures that compose the architecture. The documentation for these views does not need to wait until the architecture is produced as part of design. Capturing them, even if they are represented informally (as sketches), along with the design decisions that led you to create these structures, is a task that should be performed as part of normal ADD activities.

### Recording Sketches of the Views

When you produce structures by instantiating the design concepts that you have selected to address a particular design problem, you will typically not only produce these structures in your mind but also create some sketches of them. In the simplest case, you will produce these sketches on a whiteboard, a flipchart, a drawing tool, or even just a piece of paper. Additionally, you may use a modeling tool to draw the structures as a more rigorous notation. The sketches that you produce are an initial documentation for your architecture that you should capture and that you may flesh out later, if necessary. When you create sketches, you don't necessarily need to use a more formal language such as UML—although if you're fluent and comfortable with it then using a more rigorous notation may be useful in maintaining consistency in the use of symbols. Eventually, you will need to use a rigorous notation as your diagrams provide clarity and avoid ambiguity.

You should develop a discipline of writing down the responsibilities that you allocate to the elements as you create the structures. The reasons for this are simple. As you identify an element, you are determining some responsibilities for that element in your mind. Writing them down at that moment ensures that you won't have to remember the intended responsibilities later. Also, it is easier to write down the responsibilities associated with your elements gradually, rather than documenting all of them together at a later time.

Creating this preliminary documentation as you design the architecture requires some discipline. The benefits are worth the effort, though, as you will be able to later produce the more detailed architecture documentation relatively easily and quickly. For simple way to document responsibilities, if you are using a modeling tool, you can add them in the property section of the element itself and paste it in a document, along with a table that summarizes the responsibilities of every element you have defined. If you prefer to use a tool like that shown in Figure 20.4), If you use a design tool, you can select an element to create and use its properties window to specify the element's use; you will include these sketches in a whiteboard, attached to the document its responsibilities, and then generate the documentation automatically.

<p align="center">
  <img src="https://github.com/paulocer/DesignGoogleDrive/blob/main/Figure%2020.4.png" alt="Figura 20.4 Example elementary documentation"/>
</p>

**Figura 20.4** Example elementary documentation

The diagram is complemented by a table that describes the element's responsibilities. Table 20.1 serves this purpose for some of the elements identified in Figure 20.4.

**Table 20.1** Elements and Responsibilities

<p align="center">
  <img src="https://github.com/paulocer/DesignGoogleDrive/blob/main/Table%2020.1.png" alt="Table 20.1 Elements and Responsibilities"/>
</p>

Of course, it's not necessary to document *everything* at this stage. The three purposes of documentation that we discussed in Chapter 18 are relevant here. At this moment you are designing, you should choose a documentation purpose and then document to fulfill that purpose, based on your risk mitigation concerns. For example, if you have a critical QA scenario that your architecture design needs to meet, and if you will need to prove to this goal to a stakeholder who will perform an analysis, then you must take care to document the information that is relevant for him to perform a satisfactory. If you plan to hand off the system to future team members, then you should sketch a C&C view of the system, showing how it operates and how the elements provide the expected quality attributes injected in the system, showing at least the major layers or subsystems.

Finally, remember as you as documenting that your design may eventually be analyzed. Consequently, you need to think about which information should be documented to support this analysis.

### Recording Design Decisions

In each design iteration, you will make important design decisions to achieve your iteration goal. When you study a diagram, the approach or an architecture, you might see the end product of a thought process but can't always easily understand the decisions that were made to achieve that goal. Because design decisions beyond the representation of the chosen elements, relationships, and properties is fundamental to help clarify how you arrived at the result—that is, the design rationale. We describe this purpose of rationale in Chapter 22.

## 20.6 More on ADD Step 7: Perform Analysis of the Current Design and Review the Iteration Goal and Achievement of the Design Purpose

At the end of an iteration, it is prudent to do some analysis to reflect on the design decisions that you just made. We describe several techniques to do so in Chapter 21. One kind of analysis that you need to perform at this point is to assess whether you have done enough design work. In particular:

- How much design do you need to do?
- How much design have you done so far?
- Are you finished?

Practices such as the use of backlogs and Kanban boards can help you track the design progress and answer these questions.

### Use of an Architectural Backlog

An architectural backlog is a to-do list of the pending actions that still need to be performed as part of the architecture design process. Initially, you should populate the design backlog with your drivers, but other activities that support the architectural design iteration also be added to the architectural backlog. For example:

- Creation of a prototype to test a particular technology or to address a specific QA risk
- Exploration and understanding of existing assets (possibly requiring reverse engineering)
- Issues uncovered in a review of the design decisions made to this point

Also, you may add more items to the backlog as decisions are made. As a case in point, if you choose a reference architecture, you will probably need to refine specific concerns, or QA scenarios derived from them, to the architectural design backlog. For example, if we choose a web application reference architecture and discover that it does not provide session management, then that becomes a concern that needs to be added to the backlog.

Practices such as the use of backlogs and Kanban boards can help you track the design progress and answer these questions.

### Use of a Design Kanban Board

Another tool that can be used to track design progress is a Kanban board, such as the diagram shown in Figure 20.5, with typical columns for different states of work items: "Not Yet Addressed," "Partially Addressed," and "Completely Addressed."

<p align="center">
  <img src="https://github.com/paulocer/DesignGoogleDrive/blob/main/Figure%2020.5.png" alt="Figura 20.5 A Kanban board used to track design progress"/>
</p>

**Figure 20.5** *A Kanban board used to track design progress*

At the beginning of an iteration, the inputs to the design process become entries in the backlog. Initially (in step 1), the entries in your backlog for this design round should be located in the "Not Yet Addressed" column of the board. When you begin a design iteration, in step 2, the backlog entries that correspond to the drivers that you address in the design iteration goal should be moved to the "Partially Addressed" column. Finally, once you finish an iteration, all entries addressed (step 7), the entry should be moved to the "Completely Addressed" column of the board.

It is important to establish clear criteria that will allow drivers to be moved to the "Partially Addressed" or "Completely Addressed" columns. A criterion for "Completely Addressed" may be, for example, that the driver has been analyzed or that it has been implemented in a prototype, and you determine that the requirements for that driver have been satisfied. Drivers that are selected for a particular iteration may not be completely addressed in that iteration. In that case, they should remain in the "Partially Addressed" column.

It can be the case that activities are added to the backlog and allow you to differentiate the entries in the board according to their priority. For example, you might use different colors for backlog entries depending on the importance of the entry.

A Kanban board makes it easy to visually track the advancement of design, as you can quickly see how many of the drivers and activities in the backlog have been addressed in the iteration. This technique also helps you see whether you need to perform additional iterations. Ideally, the design round is terminated when a majority of your drivers (or at least the ones with the highest priority) are located under the "Completely Addressed" column.

## 20.7 Summary

Design is hard. Methods are needed to make it more tractable (and repeatable). In this chapter, we discussed the attribute-driven design (ADD) method, in detail. It allows an architecture to be designed in a systematic and cost-effective way.

We also discussed several important aspects that need to be considered in the steps of the design process. These aspects include the identification and selection of design concepts, their use in producing structures, the definition of interfaces, the production of preliminary documentation, and ways to track design progress.

## 20.8 For Further Reading

The first version of ADD, initially called "Architecture-Based Design," was documented in [Bachmann 00].

A description of ADD 2.0 was subsequently published in 2006. It was the first method to focus specifically on QAs and their achievement through the selection of different types of structures and their representation through views. Version 2.0 of ADD was first documented in a SEI Technical Report [Wojcik 06].

The version of ADD described in this chapter is ADD 3.0. Some important improvements over the original version include giving more consideration to the selection and implementation context, a primary design backlog and analysis as explicit steps of the design process, and providing guidance in how to begin the design process and how to use it in Agile settings. An entire book [Cervantes 16] is devoted to architecture design using ADD 3.0. Some of the concepts of ADD 3.0 were first introduced in an *IEEE Software* [Cervantes 15].

George Fairbanks wrote an engaging book that describes a risk-driven process of architecture design, entitled *Just Enough Software Architecture: A Risk-Driven Approach* [Fairbanks 10].

The Value of Information technique dates from the 1960s [Raiffa 00]. A more modern treatment can be found in [Hubbard 14].

For a general approach to system design, you can read the classic tome by Butler Lampson [Lampson 11].

Using concepts of lean manufacturing, Kanban is a method for scheduling the production of a system, as described by Corey Ladas [Ladas 09].

## 20.9 Discussion Questions

1. What are the advantages of following an established method for design? What are the disadvantages?
2. Is performing architectural design compatible with an agile development methodology? Choose an agile method and discuss ADD in that context.
3. What is the relationship between design and analysis? What are some kinds of knowledge that you need for one but not the other?
4. If you had 10 pages for the value of creating and maintaining architectural documentation to your manager during the design process, what arguments would you put forward?
5. How would your realization of the steps of ADD differ if you were doing greenfield development versus maintenance?
