# Denizen Codex Skill

A Codex skill for creating, reviewing, debugging, refactoring, and explaining [Denizen](https://github.com/DenizenScript/Denizen) scripts for Minecraft Java servers. It gives Codex a structured workflow for working with `.dsc` files and checking DenizenScript syntax against the official documentation.

The skill itself is written in Russian and instructs Codex to respond in Russian. This repository contains a Codex skill, not a Minecraft plugin or a standalone script.

## What it helps with

- Build and maintain task, world, command, procedure, inventory, item, and data script containers.
- Work with events, commands, tags, flags, definitions, custom items, inventory GUIs, and script interactions.
- Diagnose errors using project files, debug logs, [Denizen Meta](https://meta.denizenscript.com/), and the [Denizen Beginner's Guide](https://guide.denizenscript.com/).
- Review older scripts and migrate them carefully, checking version-specific syntax and dependencies.
- Separate temporary queue definitions from persistent flags, keep procedures free of side effects, and consider permissions and GUI interactions.
- Check the installed server and plugin versions, identify uncertainty, and test on a server when one is available.

The skill asks for clarification on substantial new systems and for consent before introducing plugin integrations or new visual and audio effects. Its default conventions include two-space indentation, `debug: true`, and no comments inside generated `.dsc` code unless the user requests otherwise where applicable.

## Version guidance

The skill is **aimed at Minecraft Java Edition 1.21.1 and newer**, with a Denizen build compatible with the exact server version. This is the skill's intended target, not a guarantee that every Denizen release supports every Minecraft version in that range. It does not specify or certify a minimum Denizen version.

As a concrete reference, the [official Denizen repository](https://github.com/DenizenScript/Denizen) lists **Denizen 1.3.4** as compatible with Spigot **1.21.11, 26.1.2, 26.2, and 26.3** (as well as several older versions). That list describes the plugin release, not a separate test of this skill. Check the current upstream compatibility information for your exact server, and verify commands, tags, events, and add-ons against the installed Denizen build. Older Minecraft or Denizen versions may need additional adaptation.

The [official test-server guide](https://guide.denizenscript.com/guides/first-steps/local-test-server.html) recommends a release build for production and a development build for a test server. Test generated scripts on a suitable test server before using them in production; the skill cannot guarantee correctness without runtime testing.

## Installation

Clone this repository into your personal Codex skills directory:

```bash
git clone https://github.com/S3lfuve/denizen-codex.git ~/.codex/skills/denizen-codex
```

On Windows, the destination is normally `%USERPROFILE%\.codex\skills\denizen-codex`. Start a new Codex turn after installation, then ask Codex to use `denizen-codex` for your Denizen task.

## Resources

- [Denizen Meta documentation](https://meta.denizenscript.com/)
- [Denizen Beginner's Guide](https://guide.denizenscript.com/)
- [Official Denizen repository and supported versions](https://github.com/DenizenScript/Denizen)

This is an independent Codex skill and is not an official Denizen project.
