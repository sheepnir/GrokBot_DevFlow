# Skill: dispatch-cli

<!-- The fallback pool. Taught to the CTO Bot only after the install skill in setup.md has run. Runs Claude Code or Codex on the shared computer with a launch packet, in the background, and reports through the CTO webhook. -->

**Input:** the tool (`claude` or `codex`), the repository, and the launch packet text.

1. Clone or fetch the repository under `/workspace/repos/<name>` and check out the packet's branch at its starting SHA.
2. Write the packet to `/workspace/runs/<issue>/packet.md` and run the wrapper script with `nohup`, never in the foreground:
   - Claude Code, after sourcing `/workspace/.secrets/claude.env` for `CLAUDE_CODE_OAUTH_TOKEN`:

     ```bash
     claude -p "$(cat packet.md)" --append-system-prompt-file /workspace/cards/engineer.md \
       --permission-mode auto --permission-prompts none --max-turns 40 \
       --output-format json > result.json
     ```

     Never add `--bare`: bare mode ignores the subscription token.
   - Codex, which reuses the sign-in in `~/.codex/auth.json`:

     ```bash
     codex exec --sandbox workspace-write --json -o result.md "$(cat packet.md)"
     ```

3. The wrapper script ends by POSTing the result file to the CTO webhook as the report, with `via Claude Code CLI` or `via Codex` in `models`.
4. Post one comment on the issue: `Launched on <tool>, shared computer`, with the CTO signature. Stop.

**Guardrails:** one run per tool at a time. Never `--dangerously-skip-permissions`, never `danger-full-access`, never a path outside `/workspace`. When a tool reports an expired sign-in, stop and tell the founder: renewing it is theirs.
