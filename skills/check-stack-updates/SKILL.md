---
name: check-stack-updates
description: "Updates outdated dependencies and tools after a compatibility check, fixes failing checks and publishes only after approval. Use when upgrading one project, a batch of projects or the machine toolchain."
---

# Check stack updates

Pick the scope from the request, then run its steps in order. A step is finished only when its completion criterion holds.

- Project: the project the user is working in.
- Batch: minor and patch updates across several or all projects in `~/progetti_locali`.
- Machine toolchain: Bun, Node, global Bun tools, Homebrew tools and the IDE command-line tools.

## Rules for every scope

- Read the Holds table in `~/.agents/agents-stack.md` first and leave held items out of every proposal.
- Project rules that restrict updates win. chstrategy updates are always the user's manual decision: report what is outdated there and stop.
- Propose stable releases at least 7 days old, the `minimumReleaseAge` in `~/.bunfig.toml`.
- Change nothing before the user approves. Each major jump needs its own approval.
- Use the terms outdated, major, minor, patch and toolchain.

## Scope: Project

1. Check. Run `~/.bun/bin/bun ~/.agents/stack-check.ts --project <directory>`. It reads the holds, the release age rule, the peer ranges declared by installed packages, the local Bun and Node versions and the working tree. Complete when it printed the Updatable, Major jumps and Blocked tables.
2. Read the Vercel runtime. Run `vercel project inspect <name>` for the Node version and read the Bun version from the latest production build log. Complete when you can state both versions, or have told the user which one could not be read.
3. Research the major jumps. For each row under Major jumps, read its release notes or migration guide on the web, one subagent per jump when there are several and the host can start them, each returning only the verdict line and its source. Save each page to a file (`browse cloud fetch <url> --output <file>`, or the host's fetch) and print only the lines matching `breaking|removed|deprecat|migrat|requires|minimum`, rather than reading it whole. Then compare the breaking changes with the project's code and with the local and Vercel runtimes. Move a jump to Blocked when a breaking change applies and has no fix inside this task. Complete when every major jump has a one-line verdict with its source.
4. Propose. Show three lists: updatable minor and patch, major jumps with their verdicts, and blocked items with the reason. Ask for approval in a question widget, with one question per major jump. Complete when the user has answered each question.
5. Baseline and update. Before changing anything, run every available check (lint, typecheck, tests, build) on the untouched tree and record each result as the baseline; when the build ignores type errors or no typecheck script exists, run `tsc --noEmit` and keep its error list. Then use `bun update <names>` for targets inside the declared range. Use `bun add <name>@<target>` for the others, with `--exact` for exact pins and `-d` for devDependencies. Complete when every check has a baseline result and a rerun of step 1 no longer lists the approved items as updatable.
6. Verify. Run the same checks as the baseline and compare. Handle each failure by its cause:
   - Caused by an update, with a small mechanical fix (a type annotation, a renamed import or option, a config key; at most 5 files and about 30 changed lines per update, no change in behavior): fix the code and keep the update. When the error does not name the package, find the cause by reverting one suspect at a time.
   - Caused by an update, with a fix that is larger, changes behavior or needs a product decision: revert that update, add a Holds row with the error, and rerun.
   - Already failing in the baseline because the check script itself is broken (a removed command such as `next lint`, a missing config): repair the script with the smallest working setup that follows the project's stack, for example `eslint .` with the config its framework documents, and fix the errors it then reports when they are mechanical. Keep this repair in its own commit, separate from the update.
   - Already failing in the baseline for another reason (existing type or lint errors in project code): leave the code as it is, confirm the failures are identical after the update, and report them. They do not hold the push.

   Complete when every check passes, or fails only with baseline failures that are unchanged and listed for the user.
7. Publish under the delivery policy in `~/.agents/AGENTS.md`: commit only `package.json` and `bun.lock` plus code changes the update required, with any check-script repair as a separate commit, then push. Unchanged baseline failures reported in step 6 count as accepted for this push. Complete when the push succeeded, or the user has been told why it is held.
8. Confirm the deployment. Poll `vercel ls <name>` until the deployment of that commit is Ready. On Error, read the build log, fix or revert, and publish again. Complete when a deployment of the final commit is Ready.
9. Record. Add a Holds row for each item that failed, with the reason and the condition that lifts it. Remove rows whose condition is now met. Complete when the Holds table matches the outcome and the user has a result table: package, from, to, result, followed by the code fixes made, the check scripts repaired and the baseline failures left in place.

## Scope: Batch

Batch covers minor and patch updates only. Report major jumps as a count per project and leave them for scope Project.

1. Select projects. List the projects the user named, or every folder in `~/progetti_locali` with a `package.json`. Leave out chstrategy and any project whose rules restrict updates. Complete when each project is marked included or excluded with a reason.
2. Check each included project with Project step 1, in parallel subagents when the host can start them. Mark a project skipped when its working tree has uncommitted changes. Complete when every included project has its tables or a skip reason.
3. Propose once for the whole batch: per project, the updatable minor and patch items and the blocked items with reasons. Complete when the user has approved or changed the list.
4. Treat each approved project independently with Project steps 5 to 8. A check that still fails after the handling in Project step 6 leaves that project unpublished and reverted with `git checkout -- package.json bun.lock && bun install`; the others continue. Complete when every approved project is either Ready on Vercel or reverted.
5. Record with Project step 9, then show one table: project, updated count, checks, deployment, note. Complete when every selected project has a row, including skipped and excluded ones.

## Scope: Machine toolchain

Start every proposal and every result with this sentence: these changes affect every project on this Mac.

1. Check. Read the Machine toolchain section of `~/.agents/stack-report.md`, after running `~/.bun/bin/bun ~/.agents/stack-check.ts` when it is more than a day old. Run `brew update` first when the user wants current Homebrew data. Complete when each tool has an installed and a latest version or is marked not checked.
2. Check compatibility. Compare each candidate with the Vercel build runtime, the lockfile version of the projects and the Node range the projects need. Read the release notes for every major jump. Complete when each candidate is updatable or blocked with a reason.
3. Propose in a question widget, with one question per major jump, and name the install method of each tool: `bun upgrade`, `bun update -g`, `brew upgrade <name>`, the tool's own updater, or the manager that installed it. Complete when the user has answered.
4. Update the approved tools one at a time. Complete when each tool's `--version` shows the target.
5. Verify. Run `bun install --frozen-lockfile` and the checks in one representative project per framework without committing. Roll a tool back when a check fails. Complete when the checks pass or the tool is rolled back.
6. Record with Project step 9 using scope `machine`, then rerun `stack-check.ts`. Complete when the new report shows the updated versions.
