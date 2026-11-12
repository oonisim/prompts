# Codex permission management

## Give full access (`danger-full-access`)

To enable full access for commands run by Codex, set both values below in `config.toml`. The **sandbox** is Codex's boundary on file and network access; the **approval policy** controls when it asks permission to run commands.

```toml
approval_policy = "never"
sandbox_mode = "danger-full-access"
```

`never` disables command approval prompts. `danger-full-access` removes Codex's file and network sandbox restrictions. Commands still run with your operating system account's permissions; this does not automatically grant administrator access. See OpenAI's [sandbox settings](https://learn.chatgpt.com/docs/sandboxing#configure-defaults) and [configuration reference](https://learn.chatgpt.com/docs/config-file/config-reference).

Place these values at the **top level** of the file: outside any named section, preferably before the first line beginning with `[`. Update existing values rather than adding duplicate keys.

`/path/to/project` is a placeholder for the absolute path to a repository. `~` denotes your home directory. Choose where the setting should apply:

| Intended scope | Where to configure it | Persistence |
| --- | --- | --- |
| One terminal invocation | Use the command shown below | Does not change saved configuration files |
| This repository | `/path/to/project/.codex/config.toml` | Applies to future sessions loading this project's settings |
| Your default across projects | `~/.codex/config.toml` | Applies to future sessions unless a higher-priority setting overrides it |

OpenAI documents the file locations and which settings take priority in [Config basics](https://learn.chatgpt.com/docs/config-file/config-basic#configuration-precedence).

For the repository setup, create the `.codex` directory if needed, then put the two settings above in `/path/to/project/.codex/config.toml`.

Codex loads a project's configuration only when that project is trusted. In the global file `~/.codex/config.toml`, add or update this project entry:

```toml
[projects."/path/to/project"]
trust_level = "trusted"
```

This entry allows Codex to load the repository's settings. It does not change the global permission defaults. See [project configuration files](https://learn.chatgpt.com/docs/config-file/config-advanced#project-config-files-codexconfigtoml) and the `projects.<path>.trust_level` entry in the [configuration reference](https://learn.chatgpt.com/docs/config-file/config-reference).

The repository location determines when the configuration is loaded. **Full access still permits commands to access files outside that repository.** A local configuration file also persists beyond the current session.

For a single terminal invocation instead, run:

```bash
codex -C /path/to/project \
  --sandbox danger-full-access \
  --ask-for-approval never
```

`-C` selects the working directory. The other two options apply the corresponding settings for that invocation. See [running without approval prompts](https://learn.chatgpt.com/docs/agent-approvals-security#run-without-approval-prompts) and [changing settings for one run](https://learn.chatgpt.com/docs/developer-settings#change-settings-for-one-run).

After editing a configuration file, start a new Codex session in the directory and check its active permissions. Saving the file alone does not prove that an already running session has switched modes. In terminal Codex, `/permissions` opens the permissions selector, `/status` shows session settings, and `/debug-config` shows where configuration values come from. See [inspecting settings](https://learn.chatgpt.com/docs/developer-settings#inspect-your-settings).

To return this repository to restricted command execution, replace its two permission settings with:

```toml
approval_policy = "on-request"
sandbox_mode = "workspace-write"
```

`workspace-write` permits routine work within the configured writable directories; `on-request` lets Codex request approval when it needs broader access. See [sandbox modes and approval policies](https://learn.chatgpt.com/docs/sandboxing#configure-defaults).
