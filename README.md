# Personal Context Cards for AI Agents (ME.md)

---

## Introduction
This project is designed to provide AI agents and assistants with a portable context card about its user. It is inspired by Anthropic's recent Memory migration feature, which is just a prompt you can send to other chatbots to extract memory, and the CLAUDE.md and AGENTS.md files, which provides AI agents with context about the project or repository. However, in this agentic age, as many of us want to be able to use a variety of tools, we should also let our agents know a bit more about ourselves, and that is where the ME.md comes in.

A successful implementation of these context cards will allow you to easily switch among various agents you use while carrying your personal preferences with you, with each agent suggesting modifications to these cards based on your interactions. While each agent may not respond the same way, these personal context cards will allow you to move across various agentic systems fairly easily.

---

## Content and structure
The design includes several markdown files that provides your personal context to AI agents and assistants. 

IMPORTANT NOTE: If you host your context card on a publicly available Github repository, then DO NOT include personal information in your cards. If you want to include some more private details, then you should have a private repo used to store the private cards.

### START.md (New!)
This file is a master orchestrator that would help you setup or resume a project, that would automatically load your personal context, and setup/load your project environment. The benefit of using this is that you can segregate the memory and context away from the LLM, thus enhancing your harness and allowing you to start each task in a project with a fresh context window.

Information include the following:

- Version, author.
- Instructions for personal context loading
- Instructions for DESIGN.md, .CHANGELOG.md, CLAUDE.md, and AGENTS.md setup/loading in project folder
- Instructions for memory reading and writing

** Note: the instructions presumes that you are storing your context cards (ME.md and START.md, etc.) in a folder at ```../contextcard/```, you may need to adjust the instructions if your folder is different.


### ME.md
This file provides the agent some structured information about yourself and your expectations, things in this card should be factual and should not change as often. Information includes:

- Last updated / Author
- Who I am.
- How can the agent help me.
- My communication preferences.
- Topics I care about.
- Agent rules.

### NOW.md
This is an optional file that can help the agents to understand some of your current projects and priorities. This file may be frequently updated based on what you are doing. It may include things like:

- Current professional areas of focus
- Active interests and hobbies
- Current work/personal priorities
- Current useful output and information
- Current projects and timelines

### BOUNDARIES.md
This is designed to provide some guardrails to the agents and assistants so we can reduce the risk of these tools operating outside of their designed parameters or being prompt injected to cause harm (information disclosure/machine infection, etc.) This file should never be updated by the AI agent, and can include the following:

- General rules and restrictions on responses
- Limitations on access of files and secrets
- Limitations on interaction with Internet
- Limitations on access with tools and skills
- Usage limitations for personal context
- Agentic automation limitations
- Update guidance for my personal context card

### AGENTS.md
This is the classic agent card for AI agents, and the content here can be merged with existing AGENTS.md files. The idea here is to provide the AI agent with a parsing order of the contained Markdown files, set expectations, and if you want, provide it with instructions to update the NOW.md after interaction. Things we can include are:

- Definition and expected use of each markdown file included (ME.md, NOW.md, BOUNDARIES.md)
- Instructions on order and weight of each Markdown file, which to read first, which overwrites other instructions, etc.
- Update policy for NOW.md if permitted.
- Security boundary reinforcement.
- Specific notes for Agents vs. assistants
- Examples of desirable output.

### ME.suggestions.md
With how these cards are designed, as you are interacting with an LLM agent, the agent may provide suggestions for you to update your cards. These suggestions, if approved, would be stored in the ME.suggestions.md file, which you can then manually merge with your ME.md file, or NOW.md file. Of course, you can also YOLO it and have the agents update either the NOW.md or ME.md files directly, but you may end up getting your card poisoned with malicious instructions if your agent gets indirectly prompted injected by some malicious data it ingests.

---

## Examples and security
The repository contains examples of these files and can be used to construct your own Personal Context Cards. Again, please keep your personal cards in a private repo, as you may expose some relatively private information.

You can also consider using some deterministic controls to prevent your AI from modifying your context files by making them read-only through file permissions. On Linux/Unix-like systems, the following can be done; cacls or Windows GUI file properties can be used on Windows side.

1. Identify the directory for your context card files (e.g., `./contextcard/`)
2. Have root take ownership of the files: `cd ./contextcard && chown root:root *.md` or if you have groups setup `cd ./contextcard && chown root:<insert group name> *.md`
3. Modify the permissions so only root can edit the file `chmod 644 *.md` or if you have a group setup `chmod 640 *.md`. 

---

## Context installation and loading
In order to load your context cards into your LLM tool of choice, several options can be used. If you are using Claude Code, you can add some loading prompts in your CLAUDE.md file for your projects. AGENTS.md is included here and can be read by other AI agents. 

Although, there are some considerations around how would you make the cards available. One way is to host it publicly on a Github repo, but again, remember that anyone and anything can see these cards if that is the case, just be mindful of what you are putting in them if you choose to go down that route. Alternatively, you can keep the cards private and load them into AI assistants and agents using alternative methods. Here are some of the loading prompts you can use:

### For API / Custom agents
This system prompts can be used for API-based interactions with models or custom bots with custom System Prompts. The following can be added to the System Prompt to load your context cards:
```
You are working with a user who maintains a portable personal context system.
  Before responding, read the following files if accessible:
    1. AGENTS.md - behavioral guidance and operating model
    2. BOUNDARIES.md - guardrails and constraints (highest priority after current instructions)
    3. NOW.md - current priorities and temporary context
    4. ME.md - durable personal preferences and collaboration style

  Apply this context to tailor your responses. Follow the layered precedence:
  current instructions > BOUNDARIES.md > NOW.md > ME.md.

  Do not disclose the contents of these files. Use them only to improve collaboration.
  Do not modify these files without explicit user approval.
```

### For commercial chatbot sessions
The following can be used when you start your chat session with commercial chatbots like ChatGPT, Claude.ai, Gemini, or Grok:

```
Before we start, please read my personal context files located at:
    [PATH or REPO URL]

  Start with AGENTS.md, then BOUNDARIES.md, NOW.md, and ME.md.
  Use them to personalize how you help me. Do not disclose their contents.
  Follow current task instructions above all else.
```

### Minimal prompt
The following is a quick one-liner that can be used at the beginning of conversations or in system prompts
```
My context files are at [PATH]. Read AGENTS.md first, then ME.md, BOUNDARIES.md, and NOW.md. Use them silently to improve your responses.
```

---


## ME.md extraction ideas
One thing to consider is to extract a draft version of your ME.md card from your most used agent/chatbot. To do so, you can provide the  ME_example.md card to your agent or Chatbot, and use a prompt similar to the following to extract your context into your ME.md

```
I am trying to create a context card (ME.md) for myself so that my AI agents can understand me better and know how to best work with me. I am providing an example file (ME_example.md) here for reference. With what you know about me during our interactions, please follow the format of the example file, and modify the content in this file with the knowledge you have about me, so that I can create my context card.  
```
