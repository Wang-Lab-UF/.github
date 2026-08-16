# Preparation Part

## Download Claude Code

To use Claude Code on HiPerGator, there are two ways to do so:

- VS Code extension
- Terminal

The terminal version is recommended because it provides more instructions and functions, and it can be placed inside a `tmux` panel to maintain the session or use `/remote-control` (linked in a later section) even when the SSH connection is lost.

Use the following command to download Claude Code:

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

and then use:

```bash
claude
```

to check whether the session works. You might need to use `/login` to sign in to your account.

Also, if you encounter any problems during the installation process, check the [official documentation](https://code.claude.com/docs/en/quickstart), or you can ask ChatGPT to help solve them. Remember to tell ChatGPT that you are working on HiPerGator.

## Before Using Claude Code

### tmux

`tmux` is a terminal multiplexer that can keep multiple terminal sessions running and organized within one terminal.

Before opening a Claude Code session, open a `tmux` panel to hold all the different Claude Code sessions. This allows the sessions to remain active even when the SSH connection is closed.

To create a new `tmux` session:

```bash
module load tmux/3.5  # Load tmux
tmux                  # Start a new tmux session
```

If you already have a `tmux` session inside HPG, first try to SSH into the login node where it is running, and then load the module to access it. Suppose my `tmux` session is on login node 10 (`login10`):

```bash
ssh login10           # Connect to login node 10
module load tmux/3.5  # Load tmux
tmux a                # Attach to the existing session
```

After that, we can start the session using the command:

```bash
claude
```

### tmux Instructions

To run multiple sessions inside a `tmux` panel, use `Ctrl+B` and then `c` to create a new window, and use `Ctrl+B` + `number` to switch between panels.

For more instructions or more knowledge about `tmux`, which is a widely used tool, you can check the **Terminal Multiplexers** section or learn from the lecture at [The Missing Semester of Your CS Education](https://missing.csail.mit.edu/2020/command-line/).

# Technical Part

## What Does Claude Code Consist Of?

Under the home folder:

```text
.claude/
├── settings.json
├── projects/
│   └── projectABC/
│       └── memory/
│           └── MEMORY.md
├── plugins/
├── sessions/
└── other folders
```

`.claude/settings.json` is the ruleset for all projects under the user. `MEMORY.md` contains all memories about a specific project. `.claude/plugins/` contains any skills that are imported or downloaded, and Claude Code will call them when they match the description of the skill.

Under the project folder:

```text
project/
├── CLAUDE.md
└── .claude/
    ├── settings.json
    └── settings.local.json
```

`CLAUDE.md` is responsible for anything related to this project that the agent should know at the beginning of any session.

- e.g. what the designated function of each folder is, the goal of the project, dependencies and environment, knowledge about HiPerGator and SLURM, etc.
    
- It will be updated and improved as the project develops or as new rules are introduced.
    

`.claude/settings.json` is the project-level JSON ruleset that determines the scope of instructions the agent can use. The rules live under a top-level `"permissions"` object, whose keys are `"defaultMode"`, `"allow"`, `"ask"` and `"deny"`. My settings in this JSON file are roughly as follows:

- `deny` (never): `sudo`, reading SSH keys / tokens / `.env`, `rm` or `rm all`
    
- `environment`: description of the environment
    

Starting sequence:

```text
User-level settings.json
→ Project .claude/settings.json
→ .claude/settings.local.json (the latter overrides the former)
→ Inject CLAUDE.md + AGENTS.md + MEMORY.md
→ Run SessionStart
```

## What Are the Parameters We Usually Use?

### Permission Mode (`Ctrl+Tab`)

I personally use Auto mode for the project because AI is now quite reliable and automated. This can improve project efficiency and even allow the agent to work while we are sleeping.

### Thinking Effort (`/effort`)

In my experience, max thinking effort is much better than the other options. Any thinking effort below that is faster but more prone to errors, so I prefer to use them only for lightweight tasks. The `"extra high + ultracode"` mode consumes a lot of tokens and sometimes makes the engineering process unnecessarily complicated.

To use max thinking effort, use:

```text
/effort max
```

## What Are Some of the Commands We Usually Use?

### `/goal` + Prompt + Stop Condition

This allows the agent to work continuously until it reaches the stop condition, which is useful when:

- the process involves many different steps; or
    
- a process like SLURM needs to be supervised; or
    
- the task is supposed to be done overnight.
    

### `/loop` + Trigger Interval + Prompt

Similar to `/goal`, this allows the agent to trigger itself at regular intervals. It is usually used for tasks that need monitoring or are relatively simple, or when the project goal has not yet been determined or clearly defined.

### `/rc` or `/remote-control`

This allows the current session to appear in the Claude Code app on your phone or computer. In other words, you do not need to keep watching it in the terminal and can instead operate it directly from your phone. This feature is usually used together with `tmux`, because without `tmux`, `/rc` will automatically disconnect when the SSH connection ends.

### `/resume`

When we want to directly continue a previous session, we use:

```text
/resume
```

### `/usage`

To check how much of the current quota has been used:

```text
/usage
```

## Other Functions

### Talk Between Sessions

Recently, around August 2026, Claude Code introduced a feature that allows sessions to communicate with one another. This means that once you notice that the context of one session has become too long, you can ask it to directly transfer the information to a new session and continue working there. There is no special command for this feature; you can simply instruct the agent to do it.

### Optional: Hook

If you want the beginning of each session to contain not only `CLAUDE.md + AGENTS.md + MEMORY.md`, but also other context or specialized information, you can configure a hook in `.claude/settings.json`, so that this information will automatically be added when the next session starts. Of course, after the previous feature was introduced, this feature seems less necessary.

### Cron Job

Claude Agent can create its own cron jobs, meaning that it can trigger itself at scheduled times and perform certain tasks. This is different from `/loop` because it is initiated by the Agent itself, and the Agent can also stop it at any time depending on the state of the project. Therefore, this approach is more flexible and autonomous, and it can also be used for monitoring things such as GPU jobs.

However, one thing to keep in mind is that, in my experience, if the cron job interval it sets for itself is too short, this noticeably reduces the quality of its normal responses between intervals, because it may rush to finish before the next cron job arrives.

## What Are the Skills That We Usually Use?

In general, skills are more of an enhancement than a necessity, so they should not be overused. A clear description of the requirement, instruction, or desired outcome is much more important than any Skill.

### superpower

`superpower` includes quite a few useful paradigms, such as Brainstorming or Debugging. It can help the Agent approach tasks in a more structured way.

### deep-research

This is a very powerful built-in Claude Code skill that can conduct in-depth research on almost any field, search for relevant literature, and provide useful insights. The only downside is that once it is used, token consumption becomes extremely high and very fast.

## How Do We See What the Agent Does?

### Visualization

For coding agents, Markdown files are the format they are most familiar with, so using Markdown is usually the best way to present information. Inside a Markdown file, we can use Mermaid to present workflows or structures, and we can also let the Agent create statistical visualizations based on the data.

At the same time, other formats worth considering include HTML and PowerPoint. However, these two formats are relatively cumbersome and are more suitable for larger presentations (you can try the `frontend-design` skill or other skills).

### Obsidian or Notion

Usually, in addition to having many folders inside the project, I also ask the Agent to create a `docs` folder. This folder contains all of its reports, written outputs, visualizations, and other results. In other words, as the human working on the project, I usually only look at the outputs inside this folder.

I usually include, but am not limited to, the following folders inside the `docs` folder:

- `plan/`: records detailed plans for any task or any stage of progress
    
- `stage+number/`: records any substeps or files within a major stage
    
- `task/`: records observations, experiments, or statistics
    
- `thoughts/`: files in this folder are written manually by me rather than created by the Agent, and are used to record my own thoughts and questions about the project
    
- `progress/`: files in this folder are written manually by me rather than created by the Agent, and are used to record the progress and thoughts of each smaller stage
    
- `meeting/`: I put notes from Zoom meetings with my advisor in this folder for the Agents to reference
    
- `ChatGPT/`: records useful outputs from conversations with ChatGPT by directly saving the chat records into `.md` files
    
- etc.
    

At the same time, to work together with `/remote-control` and make sure we can view any file outputs while we are away from the computer, I synchronize this folder with my own Obsidian. If you also want to do this, you need to create an R2 bucket on Cloudflare for storage and use the free Remotely Save plugin in Obsidian. The detailed setup can be handled by Agents themselves; we mainly just need to create the required accounts.

Inside Obsidian, in addition to the Remotely Save plugin, I have found the Advanced Canvas plugin to be very useful. When we want to create a workflow or a relatively complex logical mind map, we can use the `.canvas` file format together with the Advanced Canvas plugin. Based on my current testing, this provides the best visual presentation and logical structure.

At the same time, another option is to synchronize everything with Notion, although I do not know much about this approach myself. You can still consider it as an alternative.

## Optional: Speech-to-Text

When collaborating with AI, we often need to describe many things, and typing is much slower than speaking. Therefore, speech-to-text functionality is very common and important.

Usually, if we only use one language—for example, if we only use English for prompts—Apple's built-in dictation feature is enough, and we can simply bind it to a convenient shortcut.

However, if you are bilingual like me and constantly mix Chinese and English while speaking, you will find that most current dictation tools only support one language well at a time. Therefore, I recommend Superwhisper. After trying many different applications and public repositories, I think it is the best bilingual speech-to-text tool. Its built-in standard model is completely sufficient, and it is completely free.

# Conceptual Part

Beyond technical usage techniques, we should also conceptually understand the strengths and limitations of different Agents, make use of these characteristics, and determine what role we ourselves should play in this collaborative process.

### Pros and Cons of Different Models

Before discussing any methodology, we should first understand the characteristics of different models.

For Claude Code, one of its biggest strengths is its strong engineering and execution capability. In most cases, as long as the requirements are described properly, it can find a good way to implement them. However, it is important to note that what I mean here is that its engineering methodology is strong, not necessarily its research methodology.

The downside of Claude Code is that it is often not quite smart enough, and it can be difficult for it to look at many problems from the perspective of the entire project and make decisions that are most useful for the project itself. A second weakness is that many of its explanations or observations can become overly complicated, which can become mentally exhausting after reading them for a long time.

In comparison, Codex has characteristics that are relatively complementary to Claude Code. Codex also has strong engineering capabilities, but it can often over-engineer a problem, meaning that it becomes overly focused on describing, fixing, or designing certain details. One advantage, however, is that Codex tends to explain things very clearly and concisely, so it creates less cognitive load. At the same time, I personally think that Codex, or GPT more generally, has stronger problem-solving ability and overall intelligence than Claude Code.

Many of the methods or ideas described below are essentially based on these characteristics. It is important to note that as the models continue to develop, these characteristics will probably also change. Therefore, the most important thing is to decide what your prompts or overall way of thinking should be based on the strengths, weaknesses, and characteristics you observe in the models themselves.

## How to Utilize These Features?

Based on the characteristics above, my current workflow looks like this (although it is always improving). Whenever I have an idea or a step that needs to be implemented, I discuss the specific implementation process with GPT or Codex: what steps it should be divided into, what parameters are involved, and what key Decisions need to be made. If I do not yet have a clear idea, I can also Brainstorm with GPT and gradually organize my thoughts through discussion until we produce an executable plan file. (During the discussion, remember to remind GPT—or yourself—to stay aware and avoid over-engineering the problem.)

Then, I take this file and let Claude Code execute it, and during the execution process, I can use any of the technical techniques mentioned above.

After it is finished, I ask Claude Code to produce some output or report files. However, because these files are often not very readable, I then ask Codex to look at the progress or results and explain to me what actually happened and what was observed. (Usually, my prompt explicitly asks it to directly explain or answer a specific question about the results or observations in one sentence, one paragraph, or two paragraphs.)

If we encounter any Agent or workflow bugs during execution, in practice, 99% of these problems can be solved by AI itself. We can also ask it to add the lesson learned from a problem, as well as the strategy for improving it next time, into `CLAUDE.md` or memory.

## Mindsets

### What Does Each Session Do?

Many times, if we put too many things or too much content into the same session, we may experience context loss, reduced attention, or the feeling that the AI has become less intelligent. Therefore, we need to distribute content appropriately across different sessions.

For assigning functions to sessions, I think there are two approaches.

The first approach is to let each session handle a different function. For example, one session is responsible for completing all of the technical work for an entire Stage, while another session is responsible for visualizing and observing everything.

The second approach is to let each session handle one substage within a larger stage, and then use the `talk between sessions` feature mentioned above to transfer information between different sessions.

Both approaches have their own advantages. The first approach may cause the implementation session to have an extremely long context while the visualization or other functional sessions have relatively short contexts, but the division of responsibilities is very clear. The second approach ensures that the context of each session does not become too long, because once it becomes too long, we simply move to the next substage. The potential problem, however, is that within each session, the Agent may need to handle many different functions, meaning that it may not only need to write code, but also make observations and create presentations.

Therefore, you can decide which approach you prefer, combine the two, or create a new pattern of your own.

### How Is the Project Divided?

For me, I divide a project into two parts: **stage** and **task**.

A `stage` is responsible for everything related to the main progress of the project, such as initial data processing, building models, and so on. It can be understood as the main workflow of the project.

A `task`, on the other hand, is responsible for observations, statistics, experiments, or relatively short-term work that can help us generate new ideas.

This division essentially reflects one characteristic of research: the process is nonlinear. Many times, linear processes such as data processing and model building are certainly important, but a creative idea may come from an observation or a sudden insight.

Therefore, in order to answer a research question, or even to develop a good research question in the first place, we may need to conduct many observations and statistical analyses outside the main workflow of the project.

### Less Is More

With models developing, less is more.

At first, because I had seen news about Agents deleting entire email inboxes, as well as other similar AI safety incidents, I was very cautious about Agents. Therefore, in the beginning, I wrote many restrictions into `settings.json` to limit their behavior. For example, before submitting a SLURM job or making any big move, the Agent had to ask me to approve every step.

However, during actual use, I found that as models continued to develop, along with improvements in AI trustworthiness across different companies, current models have become quite safe. Adding too many safety restrictions or manual approval processes only slows down the entire project. Therefore, I now keep only some basic restrictions in `settings.json`, such as not allowing `sudo` or `remove`, while placing relatively few restrictions on other instructions.

This can also be extended into a longer-term mindset: as model capabilities become stronger, we will probably need to provide less and less detail when describing requirements or giving specific instructions.

### Naming Sometimes Matters

Usually, when discussing with an Agent how a Project should be planned and implemented, we also let it name some of the core metrics, concepts, or modules. However, I have found that the names it creates are often somewhat verbose or awkward. Naming some metrics ourselves can reduce cognitive load and can also help us develop a deeper understanding of some core concepts.

### Energy Allocation

Research is cognitively demanding work. To achieve higher efficiency, we should use our limited mental energy as effectively as possible and prevent it from being exhausted too early. Therefore, many of the methods above can essentially be understood as serving this goal. More broadly, we can also use this perspective as a starting point for optimizing our strategies when working with Agents.