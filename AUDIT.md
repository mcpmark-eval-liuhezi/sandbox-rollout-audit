# Terminal Sandbox Rollout — Security Audit

Compliance record for the move to the new sandboxed terminal environment.

## What the sandbox permits

Authoritative source: the sandbox's self-reported security policy, retrieved on
rollout day by running the sandbox's own security-rules report. Do not
reproduce this list from memory.

**Scope**
- Working directory: `/tmp/mcp_ws` (all commands are jailed to this directory)
- Allowed commands: 59 in total, all flags permitted
- Shell operators: `&&`, `||`, `|`, and `;` are enabled and work
- Output redirection (`>` / `>>`) is rejected by the command parser, as are
  stdin redirection (`<`, `<<`) and `2>&1` — the parser treats a redirect
  target as a command and blocks it
- Security limits: max command length 2048 characters; command timeout 60 seconds

**Permitted commands (verbatim, alphabetically)**
awk, basename, cat, cd, chmod, clear, cp, curl, cut, date, df, diff, dirname,
du, echo, env, file, find, git, grep, gunzip, gzip, head, helm, history,
hostname, ifconfig, kubectl, less, ls, md5sum, mkdir, more, mv, netstat,
nslookup, ping, ps, pwd, python, realpath, rm, sed, sha256sum, sort, stat,
tail, tar, touch, tr, traceroute, tree, uniq, unzip, wc, wget, which, whoami, zip

**Not permitted**
Anything outside the allow-list above — e.g. `bash`/`sh`, `sudo`, `apt`, `make`,
`node`, `python -m pip`, arbitrary binaries — plus output/stdin redirection.

## Per-bucket counts

Buckets and their labels are defined in `notes/sandbox-onboarding.md` and must
be used verbatim and in this order. A permitted command that fits none of the
four buckets is excluded from the audit table (no extra category, no "Other" row).

| Category | Allowed commands |
| --- | --- |
| File and directory tools | 26 |
| Text processing | 14 |
| Network and transfer | 8 |
| Version control and containers | 3 |

**Total: 51 commands counted across the four buckets.**

Excluded (8 commands that fit no bucket): `clear`, `date`, `echo`, `env`,
`history`, `ps`, `python`, `whoami`. 26 + 14 + 8 + 3 + 8 = 59, which matches the
sandbox's self-reported total exactly, confirming the tally is complete.

## Where the numbers came from

1. `terminal_show_security_rules` — the sandbox's own self-reported security
   policy. This is the authoritative source for the allowed-command list, the
   working directory, the flag policy, and the security limits. The full
   59-command allow-list was copied verbatim from this output.
2. `notes/sandbox-onboarding.md` — defines the four audit buckets and their
   labels. Membership rules follow the "and the like" guidance in each bullet
   (e.g. archive handling such as `tar`/`zip`/`unzip`/`gzip`/`gunzip` counts as
   file and directory tools; `md5sum`/`sha256sum`/`realpath`/`basename`/
   `dirname`/`which`/`file` count as file inspection tools).
3. Shell-operator behaviour (`&&`, `||`, `|`, `;` work; `>` does not) was
   confirmed empirically by running test commands in the sandbox and observing
   whether they executed or were rejected as a security violation.
4. Bucket membership was cross-checked programmatically: the union of the four
   buckets plus the excluded set was verified to equal the 59-command allow-list
   with no missing and no extra entries.

## Rollout artifacts

- `rollout_review.xlsx` — `Audit` worksheet with the four-bucket count table
  and a bar chart, "Allowed commands by category".
- `rollout_briefing.pptx` — title slide, "What the sandbox permits" summary
  slide, and "Allowed commands by category" chart slide with the pull request
  link as a caption.

Both artifacts live in the sandbox working directory `/tmp/mcp_ws`.
