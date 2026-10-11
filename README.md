# oslo-ld-kit

The knowledge behind the OSLO SSN/SOSA data examples (Flanders), packaged for reuse in any project:
a modelling guide, workflows as Claude Code skills, and the enforceable rules as SHACL shapes.
Born in the OSLOthema-SSNSOSA-VL project; the validation server is
[ld-mcp](https://github.com/milieuinfo/ld-mcp) (`ld-vocab`).

## Contents

| Path | What |
|---|---|
| `guide/` | the modelling guide: linked data rules, SSN/SOSA rules R1–R15, recurring patterns, discussion document, exercise, diagrams ([`guide/README.md`](guide/README.md)) |
| `skills/` | workflows: `new-example`, `revise-mapping`, `alternative-mapping`, `map-source-data`, `decision-document`, `design-ontology`, `align-ontologies` |
| `rules/ssn-sosa-rules.ttl` | the enforceable rules (R2, R3, R5, R7, R9, R11, R12, R13, R15) as SHACL, for `validate_file` |
| `.claude-plugin/` | plugin manifest (`oslo-ld`) and a marketplace manifest, so the repository can be installed directly |

## Using it in a project

1. **Add the kit as a submodule**, so that the project pins the version of the guide it follows:

   ```bash
   git submodule add https://github.com/milieuinfo/oslo-ld-kit.git oslo-ld-kit
   ```

   Update later with `git submodule update --remote oslo-ld-kit`, review, and commit.

2. **Load the guide in every session** by importing it from the project's `CLAUDE.md` (paths
   relative to the project), so that the rules are in context also outside the skills:

   ```markdown
   @oslo-ld-kit/guide/linked-data.md
   @oslo-ld-kit/guide/ssn-sosa.md
   ```

   Add the other guide files as needed.

3. **Offer the plugin to everyone who opens the project** (skills become available as
   `oslo-ld:<skill>`), in the project's `.claude/settings.json`:

   ```json
   {
     "extraKnownMarketplaces": {
       "oslo-ld-kit": { "source": { "source": "github", "repo": "milieuinfo/oslo-ld-kit" } }
     },
     "enabledPlugins": { "oslo-ld@oslo-ld-kit": true }
   }
   ```

   Or install it by hand: `claude plugin marketplace add milieuinfo/oslo-ld-kit` and
   `claude plugin install oslo-ld@oslo-ld-kit`.

4. **Declare a project profile** in `CLAUDE.md`: examples directory, file names, document language,
   local time zone, unit registry, extra prefixes, validation (see
   [`guide/README.md`](guide/README.md#project-profile)).

5. **Configure validation** with [ld-mcp](https://github.com/milieuinfo/ld-mcp) (`ld-vocab`), built
   with `mvn package`. In the project's `.mcp.json`, with paths relative to the project and the jar
   from an environment variable (defaulting to a clone of ld-mcp next to the project):

   ```json
   {
     "mcpServers": {
       "ld-vocab": {
         "command": "java",
         "args": [
           "-jar", "${LD_MCP_JAR:-../ld-mcp/ld-mcp-vocab/target/ld-mcp-vocab.jar}",
           "--ontologies", "src/main/resources",
           "--generate-shapes",
           "--shapes", "oslo-ld-kit/rules/ssn-sosa-rules.ttl"
         ]
       }
     }
   }
   ```

## Contributing

A lesson learnt in a project goes here, not into a project's private notes: a rule into the guide
(next free R-number), a checkable rule also into `rules/`, a recurring pattern into
`guide/patterns.md`, a workflow into a skill. Check with `claude plugin validate --strict .` and run
the rules against the examples of OSLOthema-SSNSOSA-VL before publishing a change.

British English throughout. The guide is project-neutral: anything specific to one project belongs
in that project's profile.
