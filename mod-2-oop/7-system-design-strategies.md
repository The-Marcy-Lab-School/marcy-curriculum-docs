# System Design Strategies & Principles

**Table of Contents**

- [Design Challenge: A School System](#design-challenge-a-school-system)
- [System Design Strategies \& Principles](#system-design-strategies--principles-1)
  - [Strategy 1: The Coordinator Pattern](#strategy-1-the-coordinator-pattern)
  - [Strategy 2: Creator Responsibility](#strategy-2-creator-responsibility)
  - [Strategy 3: Status-Driven Behavior](#strategy-3-status-driven-behavior)
  - [Strategy 4: Cascading Updates](#strategy-4-cascading-updates)
  - [Strategy 5: Bidirectional References](#strategy-5-bidirectional-references)
  - [Strategy 6: Validation Boundaries](#strategy-6-validation-boundaries)
  - [Strategy 7: Private vs. Public Properties](#strategy-7-private-vs-public-properties)
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

Recall the system design prompt from the last lesson:

> Imagine that a nation-wide Pet Adoption Center approaches you to build an application for them. The application needs to handle the following:
> - Each shelter location needs a way to accept new Pets to be put up for adoption and keep track of them.
> - Adopters need some way to apply for pet adoption.
> - The shelter needs a way to review applications and approve or reject them.

A UML diagram for the Pet Shelter might look like this:

![A UML Diagram shows the responsibilities and relationships of classes in a system.](./img/uml-diagram-pet-adoption-system.png)

Today, we'll look into the **process** for creating a system like this from scratch. What principles should we follow? What concrete strategies can we use to steer us in the right direction?

When facing a system design problem, you can use these strategies to make decisions about responsibilities and relationships:

### Strategy 1: The Coordinator Pattern
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

### Strategy 2: Creator Responsibility
**The question:** When object A needs to be connected to object B, who should create that connection object?

**Guideline:** The entity that initiates the action in the real world should create the object.

**In the Pet Adoption System:**
- Adopters create applications: `adopter.createApplication(pet, shelter)`
- Why? Because adopters fill out applications in real life
- The adopter instantiates the `Application` object and passes it to the shelter

**Other examples:**
- School: the school creates transcripts → `school.generateTranscript(student)`
- Online store: customers create shopping carts → `customer.createCart()`
- Event management: attendees create RSVPs → `attendee.createRSVP(event)`

**Tradeoff to consider:**
- **Creator pattern:** More realistic, allows "drafts" before submission, separates creation from validation
- **Coordinator creates:** Simpler (one less step), but less flexible

**Discussion question:** "Who experiences this action in real life?" usually points to the creator.

---

### Strategy 3: Status-Driven Behavior
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

---

### Strategy 4: Cascading Updates
**The question:** When one change needs to trigger other changes, where should that logic live?

**In the Pet Adoption System:**
- When an application is approved, the shelter manages the cascading updates by:
  - invoking `application.approve()` to update its status to `"approved"` (immediate change)
  - invoking `pet.setAvailability(false)` is called to update its availability to `false` (direct consequence)
  - invoking `this.rejectApplicationsForPet(pet)` to ensure only one application can be approved for the given pet (system-wide effect)

**Why this matters:** Without cascading updates, you'd have competing applications for a pet that's already adopted. The system would be in an invalid state.

**Alternative considered:** Let the application call `this.#pet.setAvailability(false)` AND call `this.shelter.rejectApplicationsForPet(this.#pet)`. This would centralize the logic but would require the application to have a reference to the `shelter`.

### Strategy 5: Bidirectional References
**The question:** If object A needs to know about object B, does object B need to know about object A?

**Options:**
- **One-way:** A knows about B, but B doesn't know about A
- **Two-way:** A knows about B, AND B knows about A

**In the Pet Adoption System:**
- Application → Pet (one-way): Applications reference pets
- Application → Adopter (two-way): Applications reference adopters AND adopters reference applications
- Application → Shelter (one-way): Applications reference their shelter
- Shelter → Applications (one-way): Shelter stores all applications
- Shelter → Pets (one-way): Shelter stores all pets

**When to use one-way:**
- Only one direction is queried frequently
  - e.g. Pets don't need to know what applications reference them (the shelter tracks this)

**When to use two-way:**
- Both directions get queried frequently
  - Adopters need to see which applications they have created
  - A shelter should be able to find the adopter via the application.

**Tradeoff:**
- One-way: Simpler, less code, less chance of bugs
- Two-way: More flexible queries, but need coordinator to keep both sides synced

---

### Strategy 6: Validation Boundaries
**The question:** Where should validation logic live?

**Guideline:** Validate at the boundary where external data enters your system.

**In the Pet Adoption System:**
- `shelter.addPet(pet)`: Validates it's a Pet instance before storing
- `shelter.receiveApplication(application)`: Validates the pet belongs to this shelter and is available
- `application.approve()`: Validates status is "pending" before approving

**Validation pattern:**
```js
methodName(input) {
  // Validate input
  if (/* invalid */) {
    console.log("Why it's invalid");
    return; // or return false
  }
  
  // Proceed with action
  // ...
}
```

**Why validate?**
- Prevents bad data from corrupting your system
- Makes debugging easier (fail fast with clear messages)
- Enforces business rules

---

### Strategy 7: Private vs. Public Properties

**The question:** Which properties and methods should be private, and which should be public?

**Guideline:** Default to making properties private (`-` in UML diagrams). Only make properties public (`+`) when absolutely necessary—such as when the information is truly just data and requires no validation or protection.

**In the Pet Adoption System:**
- Shelter's array of pets should be private: Only shelter methods should modify this directly.
- The status of an application should be private: It should only be updated by carefully controlled methods (like `approve()` or `reject()`).
- Public properties can include things like a pet's name or an adopter's contact info, assuming they're just data and not core business logic.

**When to use private properties (`-`):**
- Internal state that could be set to invalid values if changed carelessly
- Properties that should only change under controlled, specific conditions
- Properties that are core to the object's behavior or identity

**When to use public properties (`+`):**
- Data fields that require no validation and can be freely read or set
- Basic configuration/settings exposed for convenience

**Tradeoff:**
- Private properties: Require more code (getters and setters) but enforce encapsulation and invariants; prevent outside interference; reduce bugs
- Public properties: Simpler usage, but risk accidental/invalid mutations

---

## System Design Strategies Summary

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
