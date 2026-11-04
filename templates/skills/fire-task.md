# Skill: fire-task

<!-- Taught to the CTO Bot once. Fires a Claude Code task with a launch packet. The trigger URLs and tokens live in /workspace/.secrets/claude-tasks.env, mode 0600, entered by the founder through the secure secret card. -->

**Input:** the task name (`engineer`, `qa`, or `brief`) and the launch packet text.

1. Source `/workspace/.secrets/claude-tasks.env` by path. It defines `ENGINEER_URL`, `ENGINEER_TOKEN`, `QA_URL`, `QA_TOKEN`, `BRIEF_URL`, and `BRIEF_TOKEN`. Never print, echo, or quote any of them.
2. POST the packet as the `text` field of the body:

   ```bash
   curl -sS -X POST "$URL" \
     -H "Authorization: Bearer $TOKEN" \
     -H "anthropic-beta: experimental-cc-routine-2026-04-01" \
     -H "anthropic-version: 2023-06-01" \
     -H "Content-Type: application/json" \
     --data-binary "$(jq -n --arg t "$PACKET" '{text: $t}')"
   ```

3. Read `claude_code_session_url` from the response and post one comment on the issue: `Launched on Claude Code: <url>`, with the CTO signature.
4. Stop. The task reports back through the CTO's webhook routine.

**Failure:** a non-2xx response is retried once after a minute. A second failure means the pool is closed for this launch: say so in the issue comment and route to the next pool in the routing. A task accepts 30 fires an hour and the account 100, so never loop on a 429.
