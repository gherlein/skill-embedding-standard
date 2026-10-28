# Skill Embedding Standard

**A skill is the new man page.** A command-line tool should carry the thing that
teaches an agent to drive it, inside its own binary or package, and installed as
part of the package. Whoever has the tool has everything: no separate download, no
registry, no copy step. The tool becomes the distribution channel for its own
documentation.

That is the core idea. A second, independent specification then answers the
companion question — *once the skill is unpacked, where does it go so every
agent can find it?* — by naming one shared location and providing a tool to
mirror skills into each agent's proprietary folder.

## The primary idea: carry the skill in the tool

For decades a program shipped with a man page so a *person* could learn to use
it. An AI agent needs the same thing in a form it can read: a **skill** (a
directory with a `SKILL.md`). The problem is that the binary and its skill are
usually shipped and placed separately — the binary lands on `$PATH`, and the
skill has to be fetched, unpacked, and copied somewhere by a different step.
Whoever obtains only the binary has a working tool that no agent knows how to
drive.

The [Skill Self-Installation Specification](SKILL-INSTALL-SPEC.md) removes that
second step. A conforming tool either provides the skill in the package, or, **embeds its own skill inside the executable.** If it uses the latter, then it should expose two fixed subcommands:

| Subcommand | Does |
| --- | --- |
| `install-skill` | Writes the carried skill into the shared store, ready for any agent. |
| `print-skill` | Prints the carried skill to stdout. |

Because the subcommand names are fixed by the spec, the install step is
*discoverable*: an agent holding a conforming binary it has never seen can
install that binary's skill without being told how.

## The companion idea: one place to write skills, mirrored per agent

The `SKILL.md` spec settles the skill *format* but says nothing about a
*location* — and agents have not agreed on one, so each reads only its own
directory (`~/.claude/skills`, `~/.copilot/skills`, and so on). So a skill
wanted everywhere must be copied into each agent's folder, and every tool that
ships skills must know about every agent.

The [Skills Directory Specification](SKILLS-DIRECTORY-SPEC.md) removes that
fan-out by naming **one agent-neutral store** (`$XDG_DATA_HOME/skills/`) and
defining the contract each skill directory must satisfy. A producer — a tool's
`install-skill`, a package, a git checkout, an agent — writes a skill once, to
that store. Any per-agent placement still needed during the transition is done
by a separate mirroring tool, not baked into every producer.

## The two specifications

| Document | Defines | Answers |
| --- | --- | --- |
| [Skill Self-Installation Specification](SKILL-INSTALL-SPEC.md) | How a tool embeds its own skill and installs it, via the fixed `install-skill` and `print-skill` subcommands. | *How a tool carries and places its own skill.* |
| [Skills Directory Specification](SKILLS-DIRECTORY-SPEC.md) | One agent-neutral store (`$XDG_DATA_HOME/skills/`) and the contract each skill directory must satisfy. | *Where a skill lives and what it is.* |

The two are **independent draft proposals** (Experimental status), laid out in
the style of an IETF Internet-Draft to aid review. They are not products of the
IETF, the IRTF, or any other standards body. Either can be adopted without the
other.

## Reference implementation

`sm` and `ms` ([github.com/gherlein/sm](https://github.com/gherlein/sm))
implement both specifications: `sm` mirrors the store into each agent's folder,
`ms` ingests an agent's existing skills into the store, and both embed and carry
the skill that documents them — installable with `sm install-skill` /
`ms install-skill`, or printed with `print-skill`.

## Status

Each document is an independent draft, versioned in its own header. Comments are
welcome via issues or pull requests.

## License

The specifications are licensed under [CC BY 4.0](LICENSE).
