---
cover: .gitbook/assets/Screenshot 2024-09-05 at 4.29.43 PM.png
coverY: 0
layout:
  width: default
  cover:
    visible: true
    size: full
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
  metadata:
    visible: true
---

# Welcome

Welcome to Marcy Lab School's Fullstack Software Engineering Curriculum! Here, you will find all of the lecture notes, recordings, slides, and code examples as well as cheat sheets, guides, and miscellaneous resources.

Over the next year you will learn how to be a Fullstack software engineer! You'll learn the basics of the JavaScript syntax, how to use JavaScript to solve real problems, and then how to organize and optimize your code like a pro!

{% embed url="https://docs.google.com/presentation/d/1ErVMKugQc4ObNVhreLYplYZP3OsA5AmFyNSDcPKr8PA/embed?delayms=3000&loop=false&start=false" %}

## The Curriculum

In the Software Engineering Fellowship at Marcy Lab School, you will learn how to build full-stack web applications using the PERN stack (Postgres, Express, React, Node). In addition, you will learn a variety of languages, technologies, developer tools, and more including:

* **Languages:** Bash, JS, HTML, CSS, SQL
* **Frameworks & Libraries**: DOM API, React, Express, Knex, Jest, Bcrypt
* **Technologies**: Nodejs, PostgreSQL
* **Developer Tools**: VS Code, Git, GitHub, Command-Line, Chrome Developer Tools, Cursor, TablePlus, Postman

### Course Modules

The core curriculum is delivered over the course of 9 months with a final 3-month "Capstone" period with individual modules spanning 1 to 6 weeks:

* [Module 0: Command Line Interfaces, Git & GitHub](mod-0-command-line-interfaces-git-and-github/)
* [Module 1: JavaScript Fundamentals](mod-1-javascript-fundamentals/)
* [Module 2: Object-Oriented Programming (OOP)](mod-2-oop/)
* [Module 3: HTML, CSS, and DOM](mod-3-html-css-dom/)
* [Module 4: Asynchronous Programming with APIs](mod-4-async/)
* [Module 5: Backend Servers & Databases](mod-5-backend/)
* [Module 6: React & Fullstack Applications](mod-6-react/)
* [Civic Tech Hackathon](mod-9-civic-tech-hackathon/)
* [Capstone](mod-10-capstone/)

## Technical Competencies

Software Engineering has always and continues to require mastery over syntax and deep knowledge of relevant technologies, libraries, and APIs.

However, writing code is is simply the medium through which engineers express ideas, design systems, and solve problems. The competencies listed below are the durable skills that will allow a software engineer to transcend any single tech stack and become a leader of their team.

These competencies provide shared vocabulary and alignment between Marcy instructors, who design curriculum and model excellence in the classroom, and Marcy fellows who seek guidance on their strengths and areas of growth.

_Note: these competencies often will overlap and support each other. For example, technical communication requires effective mental modeling and systems-level thinking._

<details>

<summary><strong>1. Technical Communication: Clearly articulating the "what, how, and why" of technical decisions and product development to diverse audiences, from stakeholders to teammates.</strong></summary>

As a software engineer, you must be able to communicate effectively for a variety of audiences: hiring managers, teammates, junior engineers, AI chatbots. Across the many domains in which you must be able to communicate effectively, the _what_, the _how_, and the _why_ and all three must be delivered with the utmost clarity:

* _Communicating Product Development:_ What are you building and why? What users did you have in mind? How did you prioritize which features to build first and which features did you decide to leave out?
* _Communicating Tool Selection:_ What specific tools and technologies are you using? What alternatives did you consider and why did you choose this specific stack? What were the tradeoffs?
* _Communicating Code Structure:_ How is your solution organized? What are the key components and how do they interact? What considerations were you making for the team members who might have to maintain this code?
* _Communicating Problem Solving:_ What were the key implementation challenges? What limitations did you face? How did you work within or around them?

The ability to communicate well improves team collaboration and instills confidence in your competence as an engineer. The impression you make through your communication may be the single most important factor in securing a job. This is true of all industries, not just software engineering.

**Indicators**

* Shares not just what they did, but how they did it and why it matters.
* Articulates technical decisions and their impact on the project timeline, performance, or user experience
* Uses effective analogies, diagrams, and code snippets to enhance explanations
* Adapts communication style based on audience (technical vs. non-technical stakeholders)
* Can effectively communicate in both writing and in oral presentations.

**Pitfalls:** struggling to explain code clearly, lack of audience awareness, difficulty articulating technical decisions and tradeoffs

</details>

<details>

<summary><strong>2. Building Mental Models: Creating simplified abstractions of complex systems to focus on essential concepts, enabling faster learning and better problem-solving.</strong></summary>

Software engineering is full of complexity. Not only is there a vast scope of concepts, tools, and technologies to learn, any given software project can contain millions of lines of code, hundreds or more files, and dozens or more interdependent parts. To deal with this complexity, you need the ability to quickly build mental models for any topic.

A mental model distills a complex concept, system, algorithm, tool or technology into its most essential parts such that it can be used or explained simply without getting lost in the details. In other words, it is an **abstraction**.

Mental models also enable us to organize the great variety of concepts we must learn into recognizable categories. Then, when we encounter problems that we've seen before, we know which tools and technologies to reach for.

**Indicators**

* Can illustrate a concept using an analogy or a diagram.
* Can explain a concept clearly with simplified language.
* Corrects misconceptions when new evidence emerges.
* Can communicate algorithms using pseudocode.
* Can identify essential vs. extraneous details when analyzing a problem.
* Accurately applies known solutions, data structures, and algorithms to new but similar problems.
* Makes informed technical decisions based on understanding of high-level tradeoffs.

**Pitfalls:** memorizing syntax without understanding why, holding misconceptions, difficulty transferring knowledge to new situations.

</details>

<details>

<summary><strong>3. Thinking in Systems: Analyzing how individual components and dependencies interact within a larger framework to design reliable, scalable, and maintainable software.</strong></summary>

Any set of things that work together can be considered a system. Systems can range in scale from a simple algorithm to a complex, multi-layered application.

_Thinking in Systems_ is the ability to zoom out and see how a system works as a whole, to break down the system into smaller components, and to identify the dependencies between connected components.

For example, the diagram below represents the layers of a full-stack PERN stack application. As software engineers, we need to be able to view an application in this manner and understand how the layers communicate with each other.

![A full-stack PERN application system diagram.](.gitbook/assets/full-stack-diagram.svg)

Systems-level thinking enables us to design, reason about, implement, and debug complex algorithms all the way up to complex applications. It is what separates “programmers” (those who can write functional code) from “software engineers” (those who can design reliable and maintainable code).

**Indicators**

* Sees the big picture and how pieces connect (front-end, back-end, DB, APIs).
* Anticipates ripple effects of a change.
* Designs for extensibility, debug-ability, and reliability, not just “getting it to work.”
* Breaks large problems into smaller ones.
* Identifies dependencies between subtasks.

**Pitfalls:** tunnel vision on one layer of the stack, failing to anticipate ripple effects, struggling to “zoom out.”

</details>

<details>

<summary><strong>4. Methodical Debugging: Applying a structured, methodical approach to identify root causes and implement comprehensive fixes rather than relying on guesswork.</strong></summary>

Mental models may allow you “vibe-code” your way to a sloppily hacked-together application, but once things break, a real engineer will know how to follow a methodical approach to debug the problem, identify the root cause, and implement a fix. Randomly trying solutions or guessing at the root cause won’t cut it!

**Indicators**

* Debugs with a structured approach instead of randomly trying fixes. (see [Problem Solving & Debugging Process](https://www.notion.so/Problem-Solving-Debugging-Process-262b1966ed7480b5842ed3d5c8d0b189?pvs=21) )
* Reads error messages and test output carefully and investigates root causes.
* Tries multiple strategies when initial approach fails.
* Seeks to understand root causes rather than applying surface-level fixes.
* Can trace through code execution to understand program behavior.
* Tests solutions comprehensively, ensuring edge cases are covered.

**Pitfalls:** frequently “guessing” at what the problem is without systematically finding the root, jumping into code without planning an approach first, ignoring error messages, not asking for help in a timely manner, giving up too quickly when the initial approach doesn’t work.

</details>

<details>

<summary><strong>5. Detail Orientation: Taking professional pride in producing clean, well-documented code and error-free materials that adhere to industry best practices and team standards.</strong></summary>

As a professional software engineer you are going to be working on a team. Therefore writing functional code is just the prerequisite. The quality of _how_ you code, communicate, and collaborate will be what distinguishes you from the crowd as an engineer that teams want to work with.

Detail Orientation is about taking pride in every line of code you write and in every document that you produce.

**Indicators**

* Reads documentation and instructions thoroughly.
* Writes clean, readable code that follows established style guides and coding conventions.
* Creates well-structured, error-free documentation, READMEs, and presentations
* Double-checks work before submitting for review or presentation.
* Proactively seeks feedback and implements learnings in subsequent work.
* Follows git best practices

**Pitfalls:** Submitting code that raises numerous linting flags and doesn’t adhere to known style guides. Creating documentation, technical writing, and technical presentations that are error-filled or contain typos or technical inaccuracies. Repeating the same mistakes without incorporating feedback.

</details>

## Learning at Marcy (with AI)

{% embed url="https://docs.google.com/presentation/d/1G0kbcmXU5PUGbXWz5XB5F_XWFdnQDrRcpmxrdz413bs/embed?delayms=3000&loop=false&start=false" %}

Learning is a skill and, just like any other skill, it can be developed and improved upon with intentional practice. At Marcy, we will help you hone your learning skills by encouraging you to follow the 4-step iterative process:

1. Gather Information
2. Utilize, apply, and organize that information
3. Reflect on your learnings and gaps
4. Gather more information to refine your understanding

Generative AI tools can massively increase your learning potential but only if you use them intentionally! You have to be an active participant in your learning — AI can't learn for you. Some strategies for using AI intentionally include.

Ask ChatGPT to help you by...

* **Making complex topics easier to understand**:
  * "Break down this concept into smaller, digestible parts"
  * "Connect this idea to something I already know"
  * "Provide multiple perspectives or explanations for this topic"
* **Overcoming challenges with AI support**:
  * "Here is my code. Identify the mistakes and explain it step-by-step so I can fix it myself"
  * "Check out my definition for the forEach function. It feels like something is off. Can you check my work for misconceptions?"
  * "I just studied how JavaScript arrays work. Generate 5 practice questions for me ranging from easy to challenging that test my understanding of array methods."
* **Planning next steps**
  * "I am trying to learn about React hooks, what should I master first?"
  * "I am building a project, where should I start first?"
  * "I've just finished an interview, what should I do to follow up to leave a good impression?"

## Tips and Tricks to Success at Marcy

* [Ben's Teacher User Manual](https://marcylabschool.notion.site/Ben-s-Teaching-User-Manual-afe86d2a9e314c6e91e8fa44ac3b2fa8)
* [What is Computational Thinking?](https://youtu.be/qbnTZCj0ugI)
* [How to Boost Your Confidence as a New Programmer](https://marcylabschool.notion.site/marcylabschool/How-to-Boost-Your-Confidence-as-a-New-Programmer-3e08b5dc231444adb5770228696041ac)
* [The Surprising Truth About Note-taking During Lectures](https://www.youtube.com/watch?v=cRQqH18wJgw\&ab_channel=BenjaminKeep%2CPhD%2CJD)
* [How to use Notion to create notes for coding](https://www.youtube.com/watch?v=0h-WSrckaq8\&ab_channel=InternetMadeCoder)
