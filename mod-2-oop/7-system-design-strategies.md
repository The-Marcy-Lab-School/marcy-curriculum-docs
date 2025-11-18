# System Design Strategies & Principles

**Table of Contents**

- [Design Challenge: A School System](#design-challenge-a-school-system)
- [System Design Strategies \& Principles](#system-design-strategies--principles-1)
  - [System Design Strategies](#system-design-strategies)
    - [The Coordinator Pattern](#the-coordinator-pattern)
    - [Status-Driven Behavior](#status-driven-behavior)
    - [The Intermediary Object Pattern](#the-intermediary-object-pattern)
  - [Common Design Decisions](#common-design-decisions)
    - [Private vs. Public Properties](#private-vs-public-properties)
    - [Bidirectional References](#bidirectional-references)
    - [Creator Responsibility](#creator-responsibility)
- [System Design Strategies Summary](#system-design-strategies-summary)

## Design Challenge: A School System

For today's lesson, you will design a system from scratch. Here is your prompt:

> Imagine that the Marcy Lab School has approached you to design a learning management system (LMS) to help the instructors track student enrollment, grades, etc... The application needs to handle the following:
> - Track all students who attend Marcy
> - Enable students to enroll in particular classes at Marcy (e.g. Technical Lecture, Technical Interview Prep, Leadership & Development, Data Analytics Lecture, Data Challenge)
> - Enable instructors to assign grades to students for their class

**Your Task: Take 15 minutes and sketch out a rough draft UML Diagram of this system.** 

Your design should answer these three questions:
1. What are the entities/classes in your system?
2. What are the responsibilities (properties/methods) of each entity?
3. What are the relationships between entities (references between classes)?

**As you design, write down any decisions you're struggling with. For example:**

* "Should students or courses keep track of grades?"
* "Who should be responsible for enrolling a student in a course?"
* "Should there be an Instructor class or just a property on a Course to indicate who teaches it?"

We'll discuss these decision points as a class.

## System Design Strategies & Principles

When you're designing a new system, ask:

1. **Do I need a coordinator?** 
   - Are there multiple entity types that interact?
   - Do I need to enforce rules about their interactions?

2. **Who creates new objects?**
   - Who initiates this action in real life?

3. **Do objects move through stages/states?**
   - Are there multi-step processes?
   - Can an action only happen at certain times?

4. **When something changes, what else needs to update?**
   - Map out the consequences

5. **What needs to know about what?**
   - Map out who queries whom
   - Only add two-way references if truly needed

6. **Where can bad data enter?**
   - Add validation at every boundary

7. **Should properties/methods be private or public?**
   - Is this property core to the object's behavior or needs to be tightly controlled?
   - Does it need validation or restricted access?



Recall the system design prompt from the last lesson:

> Imagine that a nation-wide Pet Adoption Center approaches you to build an application for them. The application needs to handle the following:
> - Each shelter location needs a way to accept new Pets to be put up for adoption and keep track of them.
> - Adopters need some way to apply for pet adoption.
> - The shelter needs a way to review applications and approve or reject them.

A UML diagram for the Pet Shelter might look like this:

![A UML Diagram shows the responsibilities and relationships of classes in a system.](./img/uml-diagram-pet-adoption-system.png)

Today, we'll look into the **process** for creating a system like this from scratch.  What are common design decisions that you will face? What principles should we follow to make those decisions? What concrete strategies can we use to steer us in the right direction?

When facing a system design problem, you can use these strategies to make decisions about responsibilities and relationships:

### System Design Strategies

#### The Coordinator Pattern

**What it is:** The Coordinator Pattern is a design strategy where one class (the "coordinator") is responsible for managing and overseeing interactions between multiple related entities in a system.

**When to use:** Your system has multiple entities that need to interact with each other in controlled ways.

**How it works:** Create a central class that:
- Stores collections of the entities it manages
- Validates interactions between entities
- Enforces business rules (capacity limits, availability, etc.)
- Provides search/filtering methods for the entities it manages

**In the Pet Adoption System:**
- The `Shelter` is the coordinator
- It stores both `#pets` and `#applications`
- It validates: "Does this pet belong to this shelter?" before accepting an application
- It enforces: "Only one adoption per pet" by rejecting other applications
- It provides: `getAvailablePets()` and `getPendingApplications()`

**When NOT to use:** Simple one-to-many relationships where there's no interaction between managed entities (like Library/Book - books don't interact with each other).

**Alternative pattern:** Direct relationships (entities interact without a coordinator). This is simpler but harder to validate and maintain.

#### Status-Driven Behavior

**What it is:** Status-Driven Behavior is a design strategy where objects maintain an internal state (status) that controls what actions they can perform and how they behave at different stages of a process.

**When to use:** Your system has multi-step processes where objects move through different states.

**How it works:**
- Add a private status property (e.g., `#status`)
- Guard methods with status checks before allowing state transitions
- Make status transitions explicit through methods (not direct property access)

**In the Pet Adoption System:**
- `Application` has status: "created" → "pending" → "approved"/"rejected"
- `application.approve()` checks: "Can only approve pending applications"
- `Pet` has availability: `true` or `false`
- Status changes trigger other actions: approving sets `pet.setAvailability(false)`

**Why use status?**
- Prevents invalid operations (can't approve an already-approved application)
- Makes the process explicit and debuggable
- Allows you to track "where things are" in the workflow

**Pattern:**
```js
methodName() {
  if (this.#status !== "expected_status") {
    console.log("Can't do this action from current status");
    return;
  }
  // perform action
  this.#status = "new_status";
}
```

#### The Intermediary Object Pattern

**What it is:** The Intermediary Object Pattern introduces a new class to represent the *relationship* or *transaction* between two or more entities. This intermediary (sometimes called a "join" or "association" class) acts as a connector that not only links the related objects, but also stores information relevant to their interaction.

**When to use:** Use this pattern when you have two classes that are connected and you need to keep track of extra data about the connection or if you have two classes that can be connected in a "many-to-many" capacity (a pet can have many adopters applying for it, an adopter can apply for many pets).

**How it works:**
- Create an intermediary class (e.g., `Application` in pet adoption) that links the two main entities.
- Store references to those entities in the intermediary.
- Move any connection-specific properties (status, application date, notes, etc.) to the intermediary.

**In the Pet Adoption System:**
- The `Application` class connects an `Adopter` and a `Pet`.
- It includes properties for the adopter, the pet, the application status, and any other details related to the application.
- The system tracks *which* adopter applied for *which* pet, the current status, etc., *per-application*.

**In a School System:**
- The `Enrollment` class connects a `Student` and a `Course`.
- It can store the `grade` the student received in the course, their enrollment date, etc.
- You don't put the grade on the `Student` or the `Course`—it lives on the *Enrollment object* because it relates specifically to that pairing.

**Benefits:**
- Keeps your system flexible and realistic—supports many-to-many relationships (a student can take many courses, and a course has many students).
- Prevents bloated entity classes by moving connection-specific logic and data into its own class.
- Makes it easy to handle complex business rules and queries (e.g., find all courses a student has grades for, or all adopters who applied for a pet).

**Pattern Example:**
```js
class Enrollment {
  constructor(student, course, grade = null) {
    this.student = student;
    this.course = course;
    this.grade = grade;
  }
}
```
- Here, each `Enrollment` connects a unique pairing of student and course, with their grade specific to that pairing.

### Common Design Decisions

#### Private vs. Public Properties

**Guiding question:** Which properties and methods should be private, and which should be public?

**Guideline:** 
* Default to making properties private (`-` in UML diagrams) for "business logic" data—data that if set to an invalid state would break the system. 
* Only make properties public (`+`) when the data requires no validation or protection.

**Tradeoff:**
- Private properties: Require more code (getters and setters) but enforce encapsulation and invariants; prevent outside interference; reduce bugs
- Public properties: Simpler usage, but risk accidental/invalid mutations

**In the Pet Adoption System:**
- Shelter's array of pets should be private: Only shelter methods should modify this directly.
- The status of an application should be private: It should only be updated by carefully controlled methods (like `approve()` or `reject()`).
- Public properties can include things like a pet's name or an adopter's contact info, assuming they're just data and not core business logic.

#### Bidirectional References

**Guiding question:** If object A needs to know about object B, does object B need to know about object A?

**Options:**
- **Uni-Directional / One-way:** A knows about B, but B doesn't know about A
- **Bi-Directional / Two-way:** A knows about B, AND B knows about A

**Guidline:**
- Use one-way when only one direction is queried frequently
  - e.g. An application needs to list the pet that is being applied for but pets do not need to know which applications are open for it (the shelter tracks this).
- Use two-way when both directions get queried frequently
  - e.g. An application needs to list the adopter that is applying for the pet AND an adopter should be able to see which applications they have created.

**In the Pet Adoption System:**
- Application → Pet (one-way): Applications reference pets
- Application → Adopter (two-way): Applications reference adopters AND adopters reference applications
- Application → Shelter (one-way): Applications reference their shelter
- Shelter → Applications (one-way): Shelter stores all applications
- Shelter → Pets (one-way): Shelter stores all pets

**Tradeoff:**
- One-way: Simpler, fewer references, less chance of bugs
- Two-way: More flexible queries, but need coordinator to keep both sides synced

#### Creator Responsibility

**Guiding question:** When object A needs to be connected to object B, who should create that connection object?

**Guideline:** The entity that initiates the action in the real world should create the object.

**In the Pet Adoption System:**
- Adopters create applications: `adopter.createApplication(pet, shelter)`
- Why? Because adopters fill out applications in real life
- The adopter instantiates the `Application` object and passes it to the shelter

## System Design Strategies Summary

When you're designing a new system, ask:

1. **Do I need a coordinator?** 
   - Are there multiple entity types that interact?
   - Do I need to enforce rules about their interactions?

2. **Do objects move through stages/states?**
   - Are there multi-step processes?
   - Can an action only happen at certain times?

3. **Do I need to represent data about a relationship?**
   - Create an intermediary object

4. **Should properties/methods be private or public?**
   - Is this property core to the object's behavior or needs to be tightly controlled?
   - Does it need validation or restricted access?

5. **Is this relationship bi-directional?**
   - Map out who queries whom
   - Only add two-way references if truly needed

6. **Who creates new objects?**
   - Who initiates this action in real life?

---

**Practice:** Look at the School/Course/Student system. Which strategies does it use?

<details><summary>Answer</summary>

- **Coordinator Pattern:** School coordinates students and courses
- **Creator Responsibility:** School creates the enrollment relationship (debatable - could be student)
- **Status-Driven Behavior:** Not used (no multi-step process)
- **Cascading Updates:** `school.enrollStudentInCourse()` updates both student AND course
- **Bidirectional References:** Students ↔ Courses (both know about each other)
- **Validation Boundaries:** School validates student and course belong to it before enrollment

</details>
