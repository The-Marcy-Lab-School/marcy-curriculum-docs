## Deliverables

Unlike your solo projects where the project itself was the main deliverable, for a portfolio-level project, you will be required to do a bit more. The deliverables below will help keep you and your partner organized and will help your Engineering Manager (your instructor) informed on your progress. In the end, all of these deliverables will serve as valuable artifacts that document your work.

* **A Project Spec Sheet** — A project spec sheet is a document that provides key details about the project including the API being used, the intended features/user stories, and wireframes. 
  * [Project Spec Sheet Template](https://docs.google.com/document/d/1gSM_RWRAP_EJGQ7pp58Pti6PXKQJ_YWx3uvC4iRQ1EI/edit) 
  * [Example](https://docs.google.com/document/d/1ekNpWx_LKCFGPQ74qijXTMRJJoq4sHJIB1mv9Mg8HHk/edit#heading=h.dwz367p60bre)
* **A Github Organization with your Repository** — A GitHub Organization is a shared account on GitHub where you and your teammates can collaborate on repositories.
  * [Example](https://github.com/Art-viewer-mike-example) 
  * Note: your organization only needs one repository. This example has a few to show progress after each day, but you shouldn't do that.
* **A Scrum Board** — A scrum board is a tool for creating and assigning tasks ("tickets") within a project. Typically, a scrum board will organize tasks into three columns: *Backlog* (tasks waiting to be assigned), *In Progress* (assigned tasks that are being worked on), and *Done* (completed tasks).
  * [Example](https://github.com/users/benspector-mls/projects/3/views/1?layout=board)
  * [Read here to learn about creating a scrumboard](../how-tos/how-to-create-scrumboard.md)!
* **A Deployed Project Link**
  * [Example: Art Viewer](https://art-viewer-mike-example.github.io/art-viewer-day-3/)
  * [Example: PokiDash](https://poki-dash.github.io/pokemon/)
  * [Example: TeamFoodies](https://teamfoodiesismaelepifania.github.io/TeamFoodies/)
* **A Presentation + Walkthrough Demo Video** 
  * [Presentation Template](https://docs.google.com/presentation/d/1NvzL4yUaVSNFlmNtG3shNdK4j0oW3Ll2zbq8gK2vrzQ/edit?usp=sharing)
  * [Example 1](https://docs.google.com/presentation/d/1SRZRr3ioL3TYyPTPFwVcayp4q47ia9I6NV9kFSUofxQ/edit#slide=id.g2b32ee03b40_0_717) & [Example 2](https://docs.google.com/presentation/d/1zyfnu7PDpf5E3Ff7FIUdqAyvMGiAzdh_06RHAau4nF4/edit#slide=id.g2b32ee03b40_0_717)

## Milestones

As you work on this project, don't set your sights on the fully completed project from the jump. Give yourself smaller goals that you can reach each day and focus on one goal at a time. As long as your goals build on each other, you will eventually reach your final destination.

For this Art Viewer project, you can see the progress made on each day.

* [https://art-viewer-mike-example.github.io/art-viewer-day-1](https://art-viewer-mike-example.github.io/art-viewer-day-1/) (just the app is deployed. It doesn't do anything yet)  
* [https://art-viewer-mike-example.github.io/art-viewer-day-2](https://art-viewer-mike-example.github.io/art-viewer-day-2/) (we can fetch and then render a list of pictures)  
* [https://art-viewer-mike-example.github.io/art-viewer-day-3](https://art-viewer-mike-example.github.io/art-viewer-day-3) (we can click on a picture to fetch more details about that one picture)

*Note: no need to create a new repo for each day - this is just done this way for demonstration purposes.*

Eventually, you will need to develop the skill of planning out your objectives. However, for this project we've laid out some daily milestones that you can strive for with bare minimum requirements, ideal progress, and stretch goals:

{% tabs %}

{% tab title="Day 1" %} 

- **5pm:** The [proposal document](https://docs.google.com/document/d/1gSM_RWRAP_EJGQ7pp58Pti6PXKQJ_YWx3uvC4iRQ1EI/edit) is complete and posted in the group Slack channel.   
  - **Bare Minimum:** Two examples of API fetch calls. One for many things. One for one thing.  
  - **Ideal:** Descriptions of user stories. Wireframe shows thoughtful idea about what data is displayed.  
  - **Stretch:** Link to their created GitHub organization and repo in their group slack channel  
- Note: If you have not submitted your proposal and are undecided about your API by this deadline, you will be assigned an API by your Engineering Manager.

{% endtab %}

{% tab title="Day 2" %} 

- **1:30pm (soft deadline):** Create and fill out your scrum board with tickets.  
- **3pm (soft deadline):** Start working on deploying skeleton code to GitHub pages if you have not already done so  
  - **Bare minimum:** The repo has an `index.html` file with a title of the project name  
  - **Ideal:** The above, *plus* 2 console logs showing the two successful fetches. One fetch for the full set of data and a second fetch for details on just one resource.  
  - **The stretch:** The 2 fetch’s data is displayed *roughly* on the page somehow (e.g. you just dump the data into the body with `document.body.innerHTML += "..."`)  
- **5pm:** Deploy the skeleton code and share a link to your website in Slack. Repo has a filled-out scrum board.

{% endtab %}

{% tab title="Days 3" %} 

- **9am-5pm:** Work on your Minimum Viable Product (MVP). MVP is:  
  - **Bare minimum:** Full set of data is fetched and displayed  
  - **Ideal:** You can click on one item and fetch details about that one item (don't need to render it yet)  
  - **Stretch:** You can click on and render data about a single item  
- Meet with your Engineering Manager to provide a detailed update on progress.  
- **5pm:** Post a screenshot of the deployed MVP in their group chats.	

{% endtab %}

{% tab title="Day 4" %} 

- **5pm:** Post a screenshot(s) of their app with some “marked improvement” so either: a rough new stretch feature from their proposal, or a more fully styled site (layout, colors, fonts, animations)  
  - **Bare minimum:** Flexbox / Grid is used to organize fetched data set  
  - **Ideal:** Consistent fonts and colors used throughout the page with a mobile view and a desktop view.  
  - **Stretch:** A new feature is added (`localStorage`, a form to filter fetches, additional fetches, etc…)

{% endtab %}

{% tab title="Day 5" %} 

- **4pm:** Deploy whatever the “final” version of the app is with screenshots. The app should be frozen in its deployments. That should be:   
  - 2 fully completed MVP Features   
  - 1 or more fully completed stretch features   
  - Fully styled and responsive site   
- **5pm:** Complete Presentation with a walkthrough video that is no longer than 3 minutes.   
  - The walkthrough is a simple screen share of one user walking through the site and narrating what they’re doing. The minimum 2 features should be demonstrated on a deployed site.

{% endtab %}

{% endtabs %} 