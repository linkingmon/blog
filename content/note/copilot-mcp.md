---
title: "Copilot with MCP & Skills"
date: 2026-03-10
tags: ["cli", "copilot", "mcp", "ai-agent"]
---

Recently I'm playing withe the copilot CLI (an agent), other counterparts include codeX CLI, claude code ,and gemini CLI.

I've studied and played with AI agnet in n8n in my [https://linkingmon.github.com/n8n/](n8n repo) with n8n, and I think the most inportant compenents are:

1. AI model: the model to do planning and understand the semantic of the input/output.
2. memory: to store the contect for the converstaion.
3. tools: this would conncets all kinds of tools like wolfram to do arhitmetric compuation, to query data from the wikipedia, etc.
4. output schema: to deifne hte format for the output, this would allow to connect to next level API.

I thin kthe AI-agent is the same, tje AI model stills remain the same, and the memory is implmented internally or it can also be stored in Skills.md or intrictions.md (some agent like codex called agents.md). The tools are connected with mcp server (most of the useful tools server can be found on the web).

## MCP
I've implement the mcp for jira as:
```bash
{
  "mcpServers": {
    "mcp-atlassian": {
      "command": "uvx",
      "args": ["mcp-atlassian"],
      "env": {
        "JIRA_URL": "https://your-company.atlassian.net",
        "JIRA_USERNAME": "your.email@company.com",
        "JIRA_API_TOKEN": "your_api_token",
        "CONFLUENCE_URL": "https://your-company.atlassian.net/wiki",
        "CONFLUENCE_USERNAME": "your.email@company.com",
        "CONFLUENCE_API_TOKEN": "your_api_token"
      },
      "tools": ["jira_search", "jira_get_issue"]
    }
  }
}
```
and mcp for gitlab as:
```bash
{
  "mcpServers": {
    "gitlab": {
      "command": "npx",
      "args": ["-y", "@zereight/mcp-gitlab"],
      "env": {
        "GITLAB_PERSONAL_ACCESS_TOKEN": "--- personal token ---",
        "GITLAB_API_URL": "--- the url for gitlab ---",
        "GITLAB_READ_ONLY_MODE": "true"
      }
    }
  }
}
```

put this in hte config. In copilot put it in `~/.copilot/mcp_config.json`.

I then test to ask copilot my recent issue, or my recent code change, and it can responce correctly (using hte codex-5.3 model).

This is done in my company, and notice that hte jira and gitlab are self-hosted (security issue). Also, I think a important point is that the mcp server should be read only for non-self-hosts tools since we are not able to send any data from inside to otuside (this is also the true for the built-in github mcp server, it is set in read-only mode).

## Skills
I found that skills are really helpful, this would be configured in `~/.copilot/skills/*/skill.md`, such as `~/.copilot/skills/jira-search/skill.md` (this is simliar as in codex CLI". The skills would be written in yaml which include the skill name and descrtiption, and then followed by contect with markdown.

I tried to implmenet this with the planning mode of copilot, and ask copilot to as kme if there are any ambiguty for my instcrctiuon, and it take really less effort for me to create the skill files for the jira-search and gitlab-query.

After that, I ask the same problem and the agent can reaposne much quicky. I think this can also reduce the amount of token and prevent it from asking the same question again and again.

## Next step
I think I would try to implement more mcp and setup more skilles to see if hte copiot can be more powerful. Also it seems that we can build agents in `~/.copilots/agents` and would like to give it a try.
