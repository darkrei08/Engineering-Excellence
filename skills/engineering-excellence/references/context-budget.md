# Context budget and compaction

Treat model context as a finite runtime budget. Measure the effective limit for the exact `provider/model` before changing settings; never infer it from a model family name or an upstream provider's advertised maximum.

## Trigger and reserve

For runtimes that expose Pi-style compaction, automatic compaction starts when:

```text
contextTokens > contextWindow - reserveTokens
```

The default `reserveTokens: 16384` protects the next model response. `keepRecentTokens: 20000` controls the recent history retained after compaction; it does not delay the trigger. A 90% rule is therefore only an approximation and changes with the effective context window:

| Context window | Default trigger | Approx. percentage |
|---:|---:|---:|
| 131,072 | 114,688 | 87.50% |
| 200,000 | 183,616 | 91.81% |
| 1,000,000 | 983,616 | 98.36% |

For a verified context window `C` and desired trigger percentage `p`:

```text
reserveTokens = C - floor(p * C)
```

Keep at least the larger of the verified output budget and 16,384 tokens. For long tool-heavy sessions, start around an 80-85% trigger and measure before tuning further. Do not disable automatic compaction as a general fix: it replaces controlled summarization with provider overflow or request rejection.

## Measurement and verification

- Verify the exact model with the runtime's model listing command.
- Read the live session's context usage and effective context window.
- For gateways, treat the gateway model catalog and request limits as authoritative.
- Record the model, effective window, reserve, calculation, and post-change usage.
- Repeat the measurement after changing provider, gateway, model, or routing.

Keep sessions focused, read bounded file ranges, and preserve the goal, constraints, decisions, changed paths, tests, and next steps before a context boundary. Windows and Linux use the same token arithmetic; only the configuration and verification commands may differ.
