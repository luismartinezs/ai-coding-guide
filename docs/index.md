# Title: Guide to fix issues in existing codebases using AI (or how to make vibe-coding boring again)

# Using AI to code blindly will lead to slop

In "normal" coding sessions, where *you*, the human dev, are the one writing code. You follow a structured and logical approach. You are kind of forced to, because you cannot code without understanding exactly what you are doing.

It could very well be that this style of coding belongs to the past. Now we code with AI.

When coding with AI, there is the *illusion* that you can ask the AI to code for you, and you do not need to understand any of the code the AI is generating. The "vibe-coding" trend seems to reinforce this illusion.

If you use AI to generate code while you watch youtube videos, mindlessly pressing "tab" to autocomplete, without even looking at what the AI is doing, soon enough you are going to end up in a mess where the best solution will be to delete all the code and start over.

We want to avoid that.

That is that this guide is for: to use AI, but not mindlessly, mindfully.

# Quick Start (60-second read)

This is a tl,dr; of the whole guide, so you get a super quick overview and decide whether to dive deeper.

0. **Glossary**

- `@file.md` → Giving Cursor a file as context

1. **Prep**

- Drop the **`memory-bank/`** folder at the root of your repo.
- Fill in `memory-bank/metadata.md` with your repo details.
- (optional) Setup coding rules → `@p-rules-gen.md`

2. **Pick an issue**

- Create/choose a GitHub issue (e.g. `#123`).

3. **Scaffold**

```md
instructions: @p-issue-scaffold.md
metadata: @metadata
github issue number: 123
```

→ AI creates `memory-bank/issues/123/…` files.

4. **Clarify & Outline**

```md
instructions: @p-issue-clarify.md
task: @123-ticket.md
Once the task is clear, write the outline here: @123-outline.md
```

→ Answer any AI questions until clarity ≥ 95.

5. **Map the code**

```md
instructions: @p-issue-review-codebase.md
task: @123-outline.md
Append your findings to the @123-outline.md file
```

6. **Plan**

```md
instructions: @p-issue-plan.md
task: @123-outline.md
Write the plan here: @123-plan.md
```

7. **Implement** - use the plan and work one checkbox at a time (copy step into chat, review, commit).

8. **PR draft → body → review → merge**

- (optional) → Setup coderabbitai
- `@p-pr-create.md` → get PR URL
- `@p-pr-write.md` → generate body
- `@p-pr-update.md` → push to GitHub
- (Optional) `@coderabbitai full review`

> switch the AI to Ask / read-only mode if the agent tries unwanted edits

That's the loop. Read AI output, fix mistakes, repeat.

# Structured AI coding. Quick outline

At the core, remember: AI is not a wizard. The output quality heavily depends on the context you provide. AI often does mistakes, which are difficult to detect if you do not understand what it's doing.

This guide describes the step by step process to use AI to *pick* one issue from a codebase, fix it, and create a Pull Request. The context is you, a developer, working on an already existing codebase, with some issues and features already created to work on:

- You pick an issue to work on, it could be a bug fix, a new feature, etc.
- With the help of AI, you make sure you understand the issue and know what parts of the codebase are related to it
- You use AI to create a plan to work on the issue
- You ask AI to work on the issue step by step, using the generated plan
- You use AI to create a Pull Request, from a template

Yes, this smells like a normal development workflow.

Since I use the Cursor IDE (https://www.cursor.com/) and Github, that is what this guide uses, but this workflow can be followed with any of the common IDEs, AI tools and git providers. Cursor and Github are *not* mandatory.

# Context is king (aka you need a memory-bank)

The only way AI can know what to do, is to provide it with optimal context, including anything from prompts to codebase snippets. Slop comes in slop comes out. We do not want to write any of this by hand over and over. That is why we always store pre-written prompts and context in markdown text files.

This workflow uses lots of markdown files. They are all stored in a `memory-bank` folder in the root of the codebase. In fact this folder contains everything related to the AI workflows, prompts and responses that we want to remember, in an organized way.

When we interact with the AI, we reference files from the memory-bank. In Cursor this is done with `@` like this:

```md
Summarize this: @my-file.md
```

After each such prompt, I will mention briefly about model type (inference, thinking) and mode (agent, ask, etc).

> Note on Modes. Cursor has a "mode" selector, which determines what the AI is allowed to do. the main two modes are "Agent" and "Ask". In Agent mode, it can make edits. In Ask mode, it can only read. I default to use Agent mode, but sometimes the AI gets crazy trying to edit files, and I switch to Ask mode.

Other AI tools will have similar syntax, but this should be possible. As a last resort you can copy paste the file content into the chat.

# Initial setup and prep

The initial setup involves a few simple steps (we'll go into detail afterwards):

- Have an existing codebase to work on. I will assume it is on github.
- Have at least one existing issue you want to tackle (a bug, a feature, etc.). I assume the issues are created on github.
- Add the `memory-bank` folder to the codebase

Optional steps:

- add "rules" to the AI
- make git ignore the `memory-bank` folder
- setup a github MCP server

## Clone the repo with the memory-bank folder

Clone this repo locally [REPO_URL]. Within, you will find a `memory-bank` folder. Copy paste that folder and all its contents into the root folder of your codebase.

Then, open the `metadata.md` file, and change the placeholders to the values that fit your codebase.

## Optional: Create coding rules

If your codebase does not have coding rules setup, you can optionally add them.

In Cursor, you can add "rules" in the way described here: https://docs.cursor.com/context/rules#project-rules

Other AI tools will have similar ways to add rules (or the equivalent). Follow their documentation.

If you want to setup basic AI coding rules and you don't have any setup, the following prompt will do that:

```md
instructions: @p-rules-gen.md
```

> Thinking model

This will inspect the codebase and generate a `memory-bank/CODING_RULES.md` file. If you are using Cursor, take its content and add it to a file within the `.cursor/rules` folder, e.g. `.cursor/rules/coding-rules.mdc`. If you are using a different IDE or extension, follow their documentation to add the new rules.

## Optional: Make git ignore the `memory-bank` folder (and the rules folder)

If you don't want to add the `memory-bank` folder to git, you can add `memory-bank` to the `.gitignore` file.

If you do not want to change the `.gitignore` file, just run this from the root folder of your codebase:

```bash
echo "memory-bank" >> ".git/info/exclude"
```

If you are not sure whether you should do it or not, you can skip it.

> Note: if you are the only dev working in the project, it is perfectly fine to commit the memory-bank to the repo. If you work in a team, there are reasons you shouldn't, see the FAQ. Ultimately it is your call.

## Optional (but cool): Setup a github MCP server

The github MCP server (https://github.com/github/github-mcp-server) allows the AI to directly "talk" to github.

Add the github MCP (if the codebase is on github) to Cursor:

Go to Cursor Settings > MCP, and add a new MCP server, then paste this inside the `mcpServers` object:

```json
"github": {
  "command": "npx",
  "args": [
    "-y",
    "@modelcontextprotocol/server-github"
  ],
  "env": {
    "GITHUB_PERSONAL_ACCESS_TOKEN": "XXXXXXXXXXXX"
  }
}
```

You can get the `GITHUB_PERSONAL_ACCESS_TOKEN` from your [Github Settings](https://github.com/settings/personal-access-tokens)

If you don't use Cursor, this step will be a bit different, find the docs for your AI IDE/extensions to find out how to do this, it will be very similar. It's fine if you skip it though.


# How to use AI to work on issues step by step, detailed workflow

The following steps consist in you sending a sequence of predefined prompts to the AI. Use always a thinking model such as `gemini-2.5-pro` unless otherwise specified.

Other state-of-the-art thinking models are also OK, I just find Gemini pretty good in general. If you want, try other state-of-the-art thinking models available such as `claude-4-sonnet`, `o3`, `o4-mini`, and `deepseek-r1`. This is a matter of balancing quality, speed and cost. For the sake of simplicity, I stick to `gemini-2.5-pro`.

> Note: Since AI models evolve very quickly, check whatever the latest / best model is at the time of you reading this.

In between each step, you should *ALWAYS* read the AI output. AI is not a magical super wizard, it does mistakes. Never assume its output is correct or perfect. Tweak each output manually as needed, before moving to the next step.

I personally open a new chat for each individual step. I think AI can get overwhelmed if the conversation gets too long and I feel more confident working in small self-contained chunks. You can do the same.

## Retrieve issue

We are gonna get the issue from Github. If you use a different way to track issues, you might simply copy paste from the issue tracker.

```md
instructions: @p-issue-scaffold.md
metadata: @metadata
github issue number: [github_issue_number]
```

> Thinking model

Replace `[github_issue_number]` with the issue number you want to tackle.

Just because this is the first time we see this, I will explain what we are doing here. This is a prompting style that we will reuse over and over, so subsequently I will skip explanations for brevity.

- `instructions: @p-issue-scaffold.md`, this tells Cursor to use the content of the `p-issue-scaffold.md` file as instructions. Unfortunately, in Cursor you can't just paste `@p-issue-scaffold.md` into the chat and have it know what file to reference, so you have to manually type `@` and then start typing the file name, until Cursor finds it. If Cursor does not find the file, open the file in your IDE and try again.
- `p-issue-scaffold.md` is a file within the `memory-bank/prompts` folder. It reads the issue from github and creates a folder and a handful of files to get started with it. I name all prompt files starting with `p-` so that when you type in Cursor, you can quickly reference them without having to remember the exact file name.
- `metadata.md`: contains general info about the project, that the AI might need. In this case, it tells the AI the github repo URL, so that it can fetch the issue.
- `github_issue_number`: this should be the github issue number you want to tackle, e.g. `123`. It is the only dynamic part of the prompt, that will be different every time, so you need to provide it manually.

Just to provide a clear example:

Imagine your team lead has assigned you github issue #123. You are going to type this in the AI chat:

```md
instructions: @p-issue-scaffold.md
metadata: @metadata
github issue number: 123
```

> Thinking model

The `metadata.md` file content might look like this:

```md
Repository URL: https://github.com/acme-org/acme-website
```

You can read `memory-bank/prompts/p-issue-scaffold.md` to understand the instructions.

The AI should:

1. Retrieve issue from github via the github MCP server (from `https://github.com/acme-org/acme-website/issues/123`)
2. Create new folder `memory-bank/issues/123`
3. Within said folder create empty files:
   - `123-ticket.md`
   - `123-outline.md`
   - `123-plan.md`
   - `123-pr.md`
4. Populate `123-ticket.md` with the issue content

The ticket, outline, plan and pr files are the files we will need later in the workflow.

Basically, it sets you up to start working on the issue locally.

Before you continue, you should create a new branch to work on the issue.

Note on naming of prompt files:

- As I mentioned, I prepend all prompt files with `p-`.
- The infix is a reference to the workflow, in this case I use `-issue-` to indicate this prompt is for the issue workflow.
- The suffix, in this case `scaffold`, indicates what this specific prompt does

> Note: If you did not setup the MCP server, you can simply copy paste the issue content into the 123-ticket.md file.

## Clarify issue

Before doing anything, as a dev you would read the issue content and make sure you understand it. AI provides a neat solution for this: we ask AI to read the ticket, and score it in terms of clarity. Unless the score is high enough, it will keep on asking questions. Once the score is high, it will write the task outline in the outline.md file. If curious, you can read `memory-bank/prompts/p-issue-clarify.md` to see the prompt.

And this is how we would prompt the AI:

```md
instructions: @p-issue-clarify.md
task: @123-ticket.md
Once the task is clear, write the outline here: @123-outline.md
```

> Thinking model

Note that I keep on using `123` as an example issue number, because it looks better than writing `[issue_number]-ticket.md` everywhere. For each issue, the number will obviously be different. The reason I prepend the issue number to the filenames should be pretty obvious now: it makes it trivial to find them.

With these instructions, the AI will:

1. Read the ticket
2. Give it a clarity score from 0 to 100
3. If the score is below 95, it will ask questions to improve the score
4. Once the score is high enough, it will provide the task outline, with a very clear task

> Note: Cursor has multiple agent modes: Agent, Ask, etc. In Agent mode, I have found that, no matter how strongly I ask the AI to not make code changes, sometimes it **stubbornly** tries to one-shot everything. If this unwantedly happens to you, switch to Ask mode. In this mode, the AI is not allowed to edit the codebase.

AI should write the ticket outline to the outline.md file created in the previous step. If it didn't, either copy paste it manually from the chat, or insist to the AI to write the outline on the file.

It is absolutely critical that you closely follow the AI output and tweak it, because any errors the AI does will carry over to the next steps. Do not rush. Read the entirety of the output of the AI, and adjust it as much as necessary. Do not skip this. If you skip this and use AI mindlessly, you will end up with slop that you will have to delete.

So, from your perspective these are the steps:

1. Prompt the above to the AI
2. If the AI ask questions, answer them accurately. It is possible that you have to ask some of these questions to the issue author. In which case, please do not copy paste the questions to them. Trim the questions to what you need, plus try to use your own expertise and research before asking.
3. Assuming the issue is clear, read the newly created task outline in the 123-outline.md file
4. Manually apply any changes that you think are needed, and fix any errors you see. You might ask the AI to do it for you, but it's probably more work than just doing it manually.

## Review codebase to find related code

We have read the github issue, and we have clarified it. Now, we would ask ourselves "what files in the codebase are related to this issue?". Of course, we do this by seamlessly prompting the AI:

```md
Instructions: @p-issue-review-codebase.md
Task: @123-outline.md
Append your findings to the @123-outline.md file
```

> Thinking model

We ask AI to read the codebase, and find files related to the outlined task. It will append what it finds to the outline file. then, you must read the output and crosscheck that it is correct. As before, you can manually edit the output if you see mistakes or inaccuracies. If everything went well, now the 123-outline.md file contains all or most of the information required to complete the issue.

> Note: if Cursor stubbornly tries to implement everything, you can switch to Ask mode, like suggested previously.


## Write implementation plan

We have a detailed outline of the task. We also know what parts of the codebase are related to it. We can start working on it. What do we do first? How do we do it? Let's create an implementation plan. I mean, let's ask AI to create a plan. You know the drill.

```md
instructions: @p-issue-plan.md
task: @123-outline.md
Write the plan here: @123-plan.md
```

> Thinking model

The `p-issue-plan.md` contains instructions on creating a plan in a specific format:

- The plan is a list of checkboxes (so they can be checked upon completion)
- Each checkbox corresponds to a commit-size change
- The last step is always about testing

Once you prompt the above, the AI should write the plan to the 123-plan.md file, after which you should fully read it and manually fix any errors or inaccuracies. You direct the AI, not the other way around. You have the ultimate word on the plan, do not feel like you have to dogmatically follow it. Also feel free to iterate on it with the AI if you have any doubts, ad hoc prompting.

Some issues might even require deeper analysis, and online research, e.g. choosing an additional library or tool, which goes beyond the scope of this guide.

Anyway, at this point the ticket, outline and plan files should be complete, we are ready to implement the plan step by step.

> Note: If the AI in Agent mode tries to go beyond planning and start with the implementation no matter what, try switching to Ask mode as I already suggested before.

## Implement plan step by step

Now we start to make changes to the code. Go through the plan step by step.

for this, *honestly* I would simply suggest you copy paste each the step one by one from the plan into the chat:

1. Copy paste step 1 of the plan into the AI chat and submit
2. Let AI complete the step
3. Evaluate that changes are correct
4. Commit the changes
5. Repeat for step 2 of the plan

Each step is probably simple enough for an inference model to implement, meaning you might not need a thinking model. So if speed and cost are a serious bottleneck, you might switch to a cheaper model. I personally don't bother and stick

> Note: This is not sponsorship of any kind, I have zero affiliation with Coderabbitai. I integrated it recently in my workflow and I think it's quite useful.

[Coderabbitai](https://www.coderabbit.ai/) is an automated code reviewer that integrated with Github and other git platforms. It has a generous free tier whose limit you are unlikely to hit under normal conditions.

To integrate it into Github or whatever provider you use, simply follow their docs: https://docs.coderabbit.ai/platforms, spoiler alert, it's really easy. You can skip all the coderabbitai configuration part, I am providing you with a config file (`files/.coderabbit.yaml`).

You can activate coderabbitai now, if you want.

As for the config, after you have activated coderabbitai, copy the `.coderabbit.yaml` file into the root of your codebase. The config file contains some general defaults that are applicable to any web app. You don't have to bother reading it now, simply look at two sections, `labeling_instructions` and `path_instructions`.

- labeling_instructions: provide rules so that coderabbitai will suggest labels for the PR
- path_instructions: provide general rules, as well as rules for specific files using glob patterns (e.g. `**/*.{js,ts}` to match all js and ts files)

Even if the file is not optimized for your project or your project is not a web app, it doesn't matter at this point, so let's just move forward. Things will be clearer at the end.

For this config to take effect, the file `.coderabbit.yaml` must be present and committed in your repo, and pushed to remote (do not gitignore it!).

### Create a PR draft on Github

> Note: If the git flow of your team is more complicated than simply merging your feature branch to the upstream main branch, or this step confuses you, feel free to create the PR manually as you usually do and skip this step.

Before writing the PR body, we are going to create the PR as a draft on Github. Since we have integrated the Github MCP server, the AI can do it for us:

```md
instructions: @p-pr-create.md
@metadata.md
```

> Thinking model

We are providing the metadata so that the AI can find the remote repo, and knows what is the name of the branch we use as "main" (which is named "main" by default). In your case it might be different, feel free to tweak.

You should actually read the `p-pr-create.md` file this time. Make sure you checkout to the feature branch where you have made your changes, before running the prompt.

Also, make sure the upstream repo is set correctly in the `metadata.md` file. If you work in a fork of the original repo, the upstream repo will be different than your origin repo. Otherwise, both origin and upstream are likely the same.

If everything went correctly, you should get the URL for the new PR.

In the following we are going to assume the PR you got from Github is "https://github.com/acme-org/acme-website/pull/789".

### Write the PR body

With the Github MCP server, the AI can retrieve the PR diff just from the PR URL, which we can use to write the PR body.

> Note: If you did not set the MCP server, you can retrieve a PR diff using the github cli, which you would have to install: `gh pr diff https://github.com/acme-org/acme-website/pull/789 > memory-bank/issues/123/123-pr-diff.diff`, then simply reference it in the chat.

```md
instructions: @p-pr-write.md

PR URL: https://github.com/acme-org/acme-website/pull/789

Issue number: 123

PR template: @t-pr.md

Write the PR body here: @123-pr.md
```

> Thinking model

Here we ask the AI to get the PR diff and the issue context, and write the PR body based on them, using the reference template. The context is retrieved finding the appropriate folder based on the issue number. The prompts are relatively elaborate, but there's not much to say here. Run the prompt and the AI will write the PR body to the 123-pr.md file.

Also, check if your codebase already has a PR template, e.g. a file named `pull_request_template.md`. If it has, you can use it instead of the template mentioned above.

As always, read the generated PR, fill in any gaps and fix errors and inaccuracies.

There might be "TODOs" left in the PR, some of which you might prefer to complete directly on Github (next step); for example adding screenshots, which you can just drag and drop in the Github editor.

### Update the Github PR

After the PR body is ready, we have to update it on Github. Because we use the Github MCP server, we can do this with a simple prompt:

```md
instructions: @p-pr-update.md
PR URL: https://github.com/acme-org/acme-website/pull/789
PR body: @123-pr.md
```

> Inference model likely enough, feel free to stick to thinking model

This will generate a PR title, and update the PR on github with the new title and the body. Of course, you can also copy paste the body into Github yourself directly.

You can go to Github and cross-check that the PR has been updated correctly, and also fill any missing TODOs.

### Ask coderabbitai to review the PR (optional)

Here is were we actually use Coderabbitai. I assume you setup the Coderabbitai config in a previous step. If not, it's okay since Coderabbitai has already some defaults.

To ask Coderabbitai to review your PR, simply go to github, and add the following comment in the comment box at the bottom of the PR page:

```
@coderabbitai full review
```

This will trigger the review, and after a few minutes it will show up as additional comments in the PR. The Coderabbitai output is self evident. You can read it and decide whether to apply it or not. Do not mindlessly do what Coderabbitai suggests. Think whether it makes sense to apply it and then do it.

Coderabbitai even provides a "Prompt for AI Agents" that you can copy paste into the AI chat, so that it will apply the changes for you.

### Complete the PR

Once you're finished with the changes, switch the PR status from "draft" to "ready for review", and delegate the work to your fellow reviewer. Or just merge it into production if it's a solo project.

And we are done!

# Thanks

The core workflow that I currently use is the one I described above. Hope it's useful, drop questions and feedback in luismartinezwebdev@gmail.com

Thanks for reading! (do not only read, apply!)

# 📝 Cheat-Sheet (Just the commands)

> Replace **`123`** with your issue number and update paths as needed.

```md
#  Scaffold
instructions: @p-issue-scaffold.md
metadata: @metadata
github issue number: 123

#  Clarify
instructions: @p-issue-clarify.md
task: @123-ticket.md
Once the task is clear, write the outline here: @123-outline.md

#  Review codebase
instructions: @p-issue-review-codebase.md
task: @123-outline.md
Append your findings to the @123-outline.md file

#  Plan
instructions: @p-issue-plan.md
task: @123-outline.md
Write the plan here: @123-plan.md

#  Implement (loop)

Copy paste each step into the chat, review, commit.

Alternative:

instructions: @p-issue-implement.md
plan: @123-plan.md

#  Create PR draft
instructions: @p-pr-create.md
@metadata.md

#  Write PR body
instructions: @p-pr-write.md
PR URL: https://github.com/<org>/<repo>/pull/<id>
Issue number: 123
PR template: @t-pr.md
Write the PR body here: @123-pr.md

#  Update PR
instructions: @p-pr-update.md
PR URL: https://github.com/<org>/<repo>/pull/<id>
PR body: @123-pr.md
```

**Optional extras**

```md
#  Generate coding rules
instructions: @p-rules-gen.md

#  Trigger Coderabbit review
@coderabbitai full review
```


# FAQ

Q: Should everyone using AI for coding use this guide?

A: No.

You should use this guide if:

- You are working on a relatively complex task (in planning poker terms, anything 3 or above)
- Your codebase is large, complex, legacy code, etc. with many layers, some of which you might not be familiar with

You should probably not use this guide if:

- You are working on a very simple task (e.g. a couple lines of code changed, a typo, adding one paragraph of static content, etc), in which case this guide is overkill
- You are a solo developer and know the codebase like the back of your hand, in which case coding manually + ad hoc prompting might take less time than using this highly structured guide

Q: Shouldn't I commit the memory-bank to the git repo?

A: If you are the only developer in the project, feel free to commit it. However, if you are working in a team, I would highly suggest you don't:

- Other team members might not want to be bothered with the memory-bank, or feel coerced to use a specific coding workflow, or might want to use a completely different memory-bank content.
- If multiple devs are modifying the same files in the memory-bank, solving those merge conflicts would be a miserable waste of time.

Obviously, keep in mind that by having the memory-bank only locally, if you delete the repo, you will lose it.

Q: Should I always use a thinking model? Isn't that expensive?

A: In my experience and levels of usage, LLM thinking models are cheap enough to not have to bother with cost. In theory, you could balance cost, speed and quality, using inference models (i.e. not-thinking) for fast, simple, single-step tasks, and thinking models for slower, more complex multi-step tasks. In practice, I don't bother with that and default to whatever thinking model is cheap and giving me good results (right now `gemini-2.5-pro`).