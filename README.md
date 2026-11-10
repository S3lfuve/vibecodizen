# Denizen Development Skill

A reusable AI-agent skill for developing, reviewing, migrating, debugging, securing, and optimizing [Denizen](https://github.com/DenizenScript/Denizen) scripts for Minecraft Java servers. The skill provides a single `SKILL.md` with guidance, reference patterns, and a verification workflow for `.dsc` files.

The skill is intended for agents that support `SKILL.md` instructions. Installation and skill discovery depend on the agent; the repository name and skill identifier `denizen-codex` are retained for existing installations. The guidance is written in English and currently instructs the agent to answer in Russian. This is an independent skill, not an official Denizen project or a Minecraft plugin.

## Capabilities

- Create and maintain command, task, world, procedure, inventory, item, and data containers, including GUIs, events, flags, permissions, and economy-related workflows.
- Record the actual Minecraft, server, Denizen, and relevant addon versions in the task context; check changed APIs and deprecations against [Denizen Meta](https://meta.denizenscript.com/).
- Review event timing, inventory interactions, input validation, permissions, concurrency, replay protection, and state integrity.
- Prefer documented native behavior and safely nonblocking operations, while keeping thread-sensitive server operations on their supported thread.
- Add command completion and actionable errors; inspect performance, hot events, and integration risks where relevant.
- Use compact reference templates, debug logs, and the [Denizen Beginner's Guide](https://guide.denizenscript.com/) to diagnose problems and migrate legacy scripts.
- Distinguish static review from runtime verification. Test only on an existing authorized server or runner and report unverified behavior honestly.

The skill uses two-space indentation and comment-free generated scripts by default. It specifies `debug: true` during development and testing, then normally switches tested production containers to `debug: false`, followed by another test of that release configuration. It asks before adding plugin integrations or new sounds, particles, or visual effects.

## Version guidance

The skill targets **Minecraft Java Edition 1.21.1 and newer** with a Denizen build compatible with the exact server version. This is the skill's intended scope, not a guarantee that every Denizen release supports every Minecraft version in that range. The skill does not specify or certify a minimum Denizen version.

For example, the [official Denizen repository](https://github.com/DenizenScript/Denizen) lists **Denizen 1.3.4** as compatible with Spigot **1.21.11, 26.1.2, 26.2, and 26.3**, alongside several older versions. That is an upstream plugin compatibility statement, not a test of this skill. Check current upstream information and verify commands, tags, events, and addon requirements against your installed build. Older server or plugin versions may require additional adaptation.

Runtime tests depend on an already available, authorized test server or runner; the skill does not provision one. A static review alone cannot guarantee that a script works on a live server.

## Installation

Copy `SKILL.md` into the skill location required by your agent, following that agent's documentation. Keep the file named `SKILL.md`. Some agents may require a particular directory structure or a new session before discovering it.

For example, to install it for Codex using Git:

```bash
git clone https://github.com/S3lfuve/denizen-codex.git ~/.codex/skills/denizen-codex
```

On Windows, the corresponding directory is normally `%USERPROFILE%\.codex\skills\denizen-codex`. Once your agent has discovered the skill, ask it to use `denizen-codex` for your Denizen task.

## References

- [Denizen Meta documentation](https://meta.denizenscript.com/)
- [Denizen Beginner's Guide](https://guide.denizenscript.com/)
- [Official Denizen repository and supported versions](https://github.com/DenizenScript/Denizen)
