# Skill: fire-task

<!-- Taught to the CTO Bot once. Fires a Claude Code task with a launch packet. The trigger URLs and tokens live in the secrets file the private ops repo names, entered by the founder through the secure secret card. This skill covers this procedure only, never an ad-hoc request with a token. -->

**Input:** the task name (`engineer`, `qa`, or `brief`) and the launch packet text.

1. Source the secrets file the private ops repo names, by path. It defines a trigger URL and a token per task. Never print, echo, or quote either.
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

**Checked** on 2026-11-04 against the [scheduled tasks documentation](https://code.claude.com/docs/en/routines): the `/fire` endpoint, the `experimental-cc-routine-2026-04-01` beta header, the `text` field and its `routine-fire-payload` wrapper, and the limits above. The header is dated and changes with the research preview, so re-check it when a fire starts failing.
