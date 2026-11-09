# Subagents: the delegation pattern

Claude Code lets you split one assistant into a main session plus a set of named specialists. This folder explains how that works and how to add your own. For a complete, runnable example, see [`example-specialist.md`](./example-specialist.md).

## How Claude Code discovers subagents

Each subagent is a single markdown file under `.claude/agents/` in your project (or `~/.claude/agents/` to make it available everywhere). Every file has two parts:

1. **Frontmatter** (YAML between `---` lines) that tells Claude Code how to route to it:
   - `name`: the identifier used to invoke the subagent.
   - `description`: a plain-language statement of *when* to route work to this subagent. The main session reads this to decide whether to delegate, so write it as a routing rule, not a slogan.
   - `tools`: the tools this subagent is allowed to use. Restricting this is the main safety lever. A read-only specialist should not have `Edit` or `Bash`.
2. **Body**: the subagent's system prompt. This is its persona and working instructions, scoped to one domain.

## The delegation pattern

The main session is a generalist. Its job is to understand the request, decide whether a specialist fits, and either handle it directly or hand it off. A specialist runs in its own context window, does its focused task, and returns a result. The main session then summarizes or acts on that result.

Delegation earns its keep in three situations:

- **Context isolation.** A research sweep that reads twenty files should not fill the main session's context with the file contents. The specialist reads them and returns the conclusion.
- **Repeatable expertise.** A specialist's instructions encode how a domain should be handled (sources to check, formats to produce, things never to do), so the behavior is consistent every time.
- **Least privilege.** A specialist can be given only the tools its job needs, which limits the blast radius of a mistake.

## Keep each specialist's scope bounded

Routing only works if the main session can tell specialists apart. Two specialists with overlapping scope produce ambiguous routing and duplicated or conflicting work.

For every specialist, write down:

- **What it does.** One or two concrete task types.
- **What it does not do.** Name the adjacent work that belongs to a different specialist or to the main session.

A good test: read two subagents' `description` fields side by side. If you cannot say in one sentence which one should handle a given request, tighten the scopes until you can.

Good boundaries tend to follow the kind of output or the kind of tool access, for example "reads and summarizes" versus "modifies files" versus "takes external actions." Bad boundaries are vague topics like "helps with work stuff."

## Writing a good `description`

The `description` is what the main session matches against, so include:

- The kinds of request that should route here (be specific about the verbs and objects).
- Explicit exclusions where a neighboring specialist is the better fit.

Avoid generic phrasing such as "a helpful assistant for many tasks." It gives the router nothing to match on.

## Adding your own

1. Create `.claude/agents/<your-name>.md`.
2. Fill in the frontmatter: `name`, `description` (routing rule with exclusions), and the narrowest `tools` list that works.
3. Write the body: role, scope, the steps it should follow, and the output format it should return.
4. Start a Claude Code session in the project and ask for something that should route to it. Confirm it is picked up and does only its own job.

Start with one or two specialists. Add more only when a real, repeated task keeps needing the same focused instructions.
