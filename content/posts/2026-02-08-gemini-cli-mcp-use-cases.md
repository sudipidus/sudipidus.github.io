---
title: "Interesting Use Cases of Gemini CLI and MCP"
date: 2026-02-08T00:00:00Z
draft: false
tags: ["Gemini CLI", "MCP", "Go LSP", "PostgreSQL", "GitHub"]
---

### Empowering Development Workflows with Gemini CLI and MCP

The Gemini CLI, augmented by the Agent Skills and the Multi-Capability Protocol (MCP) server, offers a powerful and interactive way to streamline software engineering tasks. This post explores some interesting use cases, highlighting how I've leveraged these tools, including Go LSP, PostgreSQL, and the GitHub MCP server, to enhance my development workflow.

#### 1. Go Language Server Protocol (LSP) Integration

As a Go developer, understanding the codebase quickly and accurately is crucial. The Go LSP integration through the Gemini CLI's `go_workspace`, `go_search`, and `go_diagnostics` tools has been incredibly helpful:

- **`go_workspace`**: Provides an immediate overview of the Go module structure, helping to orient myself within large projects. This is essential for quickly grasping project layout.
- **`go_search`**: Allows for fuzzy searching of Go symbols (types, functions, variables). For instance, when I needed to understand the `Server` struct, a quick `go_search({"query":"server"})` immediately pointed me to its definition, saving valuable time navigating files manually.
- **`go_diagnostics`**: After making changes, running `go_diagnostics({"files":["/path/to/my_file.go"]})` helps catch syntax errors, type mismatches, and other issues instantly, long before a full build or test run.

#### 2. PostgreSQL Database Interactions

Interacting with databases is a common task. The Gemini CLI's `query` tool provides a straightforward way to execute read-only SQL queries directly from the command line, which is great for quick data inspections or verification during development.

- **`query({"sql":"SELECT * FROM users WHERE status = 'active' LIMIT 5;"})`**: I've used this to quickly peek into database states, verify data after a migration, or confirm the impact of certain operations without leaving my terminal. This avoids context switching to a separate database client.

#### 3. GitHub MCP Server for Repository Management

Managing Git repositories and collaborating on GitHub are integral parts of modern software development. The GitHub MCP server, exposed via the Gemini CLI, offers programmatic control over common GitHub operations.

- **`search_repositories`**: I used this tool at the beginning of this very session to find my blog repository (`sudipidus/sudipidus.github.io`). For example, `search_repositories({"query":"user:sudipidus blog in:name,description"})` quickly located it.
- **`get_file_contents`**: To identify the latest blog entry, I used `get_file_contents({"owner":"sudipidus","repo":"sudipidus.github.io","path":"content/posts"})` to list the content of my `posts` directory.
- **`create_or_update_file`**: This tool is what I used to add new blog posts or modify existing ones, like the previous entry I created for today. For example, `create_or_update_file({"owner":"sudipidus", "repo":"sudipidus.github.io", "path":"content/posts/2026-02-08-new-entry.md", "content":"...", "message":"..."})`.
- **`create_branch`**: The new branch for *this very article* was created using `create_branch({"owner":"sudipidus","repo":"sudipidus.github.io","branch":"agent/new-gemini-cli-mcp-article","from_branch":"main"})`.
- **`create_pull_request`**: Once this article is finalized, I will use `create_pull_request` to propose these changes back to the `main` branch, completing the cycle of development and collaboration directly from the CLI.

#### Conclusion

The ability to integrate and orchestrate various development tools and platforms through a unified CLI experience, powered by Gemini CLI and MCP, significantly boosts productivity. The examples above demonstrate how I've used these capabilities to understand code, interact with data, and manage repositories—all within a seamless, interactive workflow. In fact, this very blog post was conceived and largely drafted using this integrated flow, showcasing the practical utility of these advanced CLI capabilities.
