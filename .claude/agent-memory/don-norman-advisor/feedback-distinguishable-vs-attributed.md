---
name: feedback-distinguishable-vs-attributed
description: "distinguishable" output is a weaker UX contract than "correctly attributed" output — watch for it whenever a failure taxonomy gets tasked/tested
metadata:
  type: feedback
---

When a spec or task asserts that N failure classes are "distinguishable from output," that is a weaker contract than the human at the console actually needs. Two messages can be distinguishable (different strings) yet still leave the maintainer unable to tell *which world they are in* — slip (I broke it), mistake (the thing genuinely is wrong), or system change (the environment shifted under me).

**Why:** the gulf of evaluation closes only when the user can attribute the failure to its cause, not merely tell two failures apart. A passing "distinguishable" test can ship output that is different-but-misattributing. Surfaced on feature 015 Gate B (T013 "distinguishable from output", T027 per-mode recovery action). The infra failure class is usually the worst offender — its correct recovery action (wrong cwd / chmod +x) is the one most likely to get a generic "check your environment".

**How to apply:** when reviewing any tasked/specced failure taxonomy, push the assertion from "classes differ" to "each class names its own cause + a cause-specific recovery action." One-line strengthening, not a structural objection — keep the severity low unless the misattribution is user-blocking. Relates to [[feedback-recovery-not-user-job]].
