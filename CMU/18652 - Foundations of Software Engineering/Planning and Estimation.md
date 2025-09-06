Before defining a plan you need to have a vision for the project where you agree on the benefits, the goals, the features, and characteristics like platform specialization or power consumption. After you have this high-level understanding, you can start planning and over time your vision can change, leading you to refine your plans over and over throughout the project lifecycle.

Planning involves project constraints, planning strategy, multi-level plans, and prioritizing strategy

Project constraints can be represented by an "iron triangle". It is defined by 3 variables: scope, cost, and time; if you change one it will impact the other. Quality is not a variable as you usually do not want to compromise on quality.
![[Pasted image 20250828185200.png]]

Fixed-scope: When can we deliver \<scope>/product?
- scope is fixed, cost and time are variables
- the scope is a moving target/will change over time, so fixed-scope is not realistic
Fixed-date: How much can we deliver by \<date>? 
- Agile methods tend to gravitate toward fixed-date approaches


Multi-level planning: used by Iterative & Incremental, and Agile
![[Pasted image 20250828185432.png]]
- Daily plan is implemented in Agile in ways such as Scrum standup meetings
- Benefits
	- Helps us organize the work long term and short-term into detailed plans
- Drawbacks
	- Planning requires work = overhead; that's why Lean doesn't use it

![[Pasted image 20250828185643.png]]
Solution Roadmap: Outlines release cycles of entire project in terms of release theme and what features the release will include

Release Plan: Defines the iteration cycles of a given release in terms of a goal for each iteration and a list of requirements to be implemented for each iteration.

Iteration Plan: Defines a list of tasks to be completed by the team to reach the goal of the given iteration.

We don't need to create all these plans ahead of time aside from Solution Roadmap.

Work prioritization:
![[Pasted image 20250828185919.png]]

Strategy:
- Risk: Maintain a risk list involving the risk, its priority, and the risk management plan with categorization of high/medium/low
- Dependency
- Value: Talk to users/customers and attach value to features/requirements
- Effort: Estimate the effort and attach those estimates to features/tasks. Be cautious of underestimation of tasks and anchoring bias (i.e. tasks takes 6 months to complete but someone asks for it in 3 months, you're going to give them an answer closer to 3 months)

Definition of Done: The set of criteria needed to be satisfied to mark a work item as complete. This is decided before estimation of effort of tasks
Example:
![[Pasted image 20250828190116.png]]

Planning poker: combines analogy (relative judgement), disaggregation (breaking tasks into smaller chunks to estimate them easier), and expert judgement methods
- (analogy) Uses story points (fibonacci numbers) for estimates of tasks
- (disaggregation) breaking down product into tasks and estimating them
- (expert judgement) Group activity is performed by developer team to remove anchoring bias
- can be done at beginning of iteration/release
- cautions
	- need to balance estimation effort and estimation accuracy
	- document assumptions made during estimation

We measure
- productivity (velocity) - how good is our team
	- developer difficulty (points per iteration)
	- developer effort (spent per iteration)
	- customer value (points per iteration)
	- customer # user cases (delivered per iteration)
- predictability - how good is our process? (effort estimated vs. effort spent)
- quality - how good is our product?
	- number of issues/bugs
	- number of tests/test coverage