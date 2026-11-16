# Token effort

Estimated ticket effort and actual agent token usage are separate metrics. Neither measures customer value directly.

Exact Token effort requires trustworthy host/runtime usage counters associated with ticket execution. Cadence MCP sees its own tool calls; those alone cannot establish all model usage. Do not estimate tokens from text length, elapsed time, context-window size, or intuition.

Accounting convention: measure runtime-reported input plus output tokens attributable to this ticket's execution, from intake through the reviewed-PR endpoint, including retries, verification, corrections, and resumed work. Include child-agent usage only when available and not already included in the parent total. Do not add cached or reasoning subtotals again when they are already included in provider totals. Exclude ticket creation, unrelated conversation, and external reviewer usage. Track shared batch preparation separately unless the runtime attributes it reliably; disclose any excluded/unattributable work rather than presenting partial coverage as exact total effort.

Use per-ticket execution/run IDs and authoritative usage events or baseline/end counters scoped to that work. Include retries and resumed work without counting the same event twice. Never divide a mixed-ticket session total arbitrarily or write the same session total to every ticket. If accurate attribution is impossible, report the limitation.

Prefer server-side idempotent usage events aggregated into the ticket field. If only an absolute numeric field exists, use the server's concurrency/version mechanism when supported, preserving previously recorded effort. Do not blindly increment or overwrite a cumulative total after a timeout. Reconcile existing records first.

At REVIEW PASSED (not merge/Done), call `update_ticket` with `ticketId`, freshly read `version`, and the measured cumulative `tokenEffort`, then read it back. Preserve the In Review lane. Use `add_comment.tokensSpent` only for a known non-overlapping work interval; this field does not automatically update the ticket's total. Store provenance, interval/run IDs, and coverage in a concise comment/checkpoint. A final runtime event or host integration is needed to include tokens emitted after the agent's last tool call; an agent-written snapshot must identify its cutoff rather than claiming to include its own future final response.

If the field, telemetry, or attribution is missing, leave Token effort unset and report “Unavailable” with the reason. Do not block otherwise authorized engineering solely because telemetry is absent, but do not report accounting as complete. Partial usage may be retained only with an explicit provisional/partial designation.
