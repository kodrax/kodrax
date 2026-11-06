- Product can be with lowercase.
- The project is starting. I have the github org "kodrax" should I create the repo also?
- The ADR is right, as Kodrax is an agent harness built with Mastra and NextJS, but it is an application that can be installed in any project without disrupting the project. It acts as an overall assistant that can understand the project, use or create the available documentation, create the vector databases to have clear access to the system structure and details. It will use overseer as a tool of the harness to manage the planning, it will connect externally and should be enabled by default but the user can disable it. That's why this could be a multi-project, multi-app because it can be installed everywhere and it can start from zero and create a whole project. So it should have capabilities of handling the repos always with security of the code in mind, and as always, as an optional field enabled by default. 
- I will install overseer in the project when required.
- Ok.
-----
- Kodrax agent should have their own database, it should be used to store the project information with the vectors for the project documentation as a source of truth. The planning database will pertain to the overseer and the kodrax agent will consume that with the tools and MCP. The @kodrax/overseer is already published in npm
- Kodrax is not a competitor of Mastra Factory, because Factory only focusses on the development part of the software lifecycle, Kodrax agent will take care of the definition of the project and the planning of the project before even the issues exist in the repo. Even it could use Mastra Factory as a tool in conjunction with overseer. Check all that possibilities.

D1. a
D2. Check overseer npm package, it is a tool that can be installed everywhere https://www.npmjs.com/package/@kodrax/overseer?activeTab=readme
D3. Check if Kodrax agent can use Mastra Factory because they are different. Mastra Factory uses agents that use models with APIs, Kodrax Agent will be a tool that will connect to a harness cli.
D4. Separate, but consider this will be running locally.
D5. ACP first, with a vendor SDK where ACP lacks something. Remember this is a tool that will connect to a harness cli.
D6. It should work mainly on user's harness subscription, but an option to use API Keys should be allowed.
D7. It should use a Harness as its brain. It should be adaptable depending on the harness subscription and a timeline of new vendors should be considered.
D8. It should use a configurable folder set by the user at installation time that can also be changed later. Kodrax agent should not disrupt the user docs, but manage them, the user can select that Kodrax agent will fully manage or just propose.
D9. Kodrax agent should be a mono repo.
D10. It will be decided later.
D11. Kodrax agent should work with worktrees and if required with sandboxes.
D12. Approvals by default, but the user can select where it wants auto approvals.

------
1. - Database is a rebuildable index of the docs. The docs are the source of truth, and since the database is a reflect of that, it can be used like that. Documents can change over time.
- Enable by default means overseer should be enabled vy default. But the kodrax agent can also be disabled.
- No, this project is to build the kodrax agent, this repo will be a mono repo, the one of the agent. When the agent is installed in a project it only should manage the repos as a maintainer or contributor, not as part of the kodrax project.
2. - Yes
- Yes
- Yes
- Yes
- Elaborate on this auto-approved question.
3. Public, same as overseer
4. What do you mean "I'll ask Anthropic to confirm?" This should work similar to overseer, it can connect to the Claude or GPT or whatever subscription.
5. Explain me better the solo or teams builder. It will use omp (oh my pi) and claude code mainly, as that is what I have in the dev machine.
6. Propose SRS. What do you mean by "three spikes"