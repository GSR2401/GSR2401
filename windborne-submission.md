# WindBorne — Full Stack Security Software Engineer Application

**Shreyas Reddy**

---

## Q1: What's the most pain you've personally experienced running a 24/7 production system?

During my internship at EchoStar, I built an internal tool that needed to render data from both the production and development environments simultaneously — cross-environment reads. The workflow was standard: test locally, deploy to dev, then promote to prod. The pain was that the code passed every local and dev test cleanly and then broke in production, and every failure traced back to IAM or security policy: a role with the right permissions in dev that lacked them in prod, or a cross-account read blocked by a policy that only existed in the production environment.

The harder problem was that I couldn't freely debug in prod. I couldn't add logging, re-run requests, or reproduce the exact failure in a controlled environment — production was the only place where the real data and the real policies lived together, and poking at it was off-limits. That meant the normal feedback loop (observe, hypothesize, test) didn't apply.

The fix was methodical rather than iterative. I mapped every IAM role, trust relationship, and resource policy involved in a cross-environment read, then reasoned explicitly about where production's policy differed from dev's rather than discovering it by trial and error. I got the cross-reads working, but the bigger change was how I now approach environment promotion: I no longer treat dev success as sufficient validation. Before promoting anything that touches permissions or cross-account resources, I explicitly diff the policy and role configurations between environments — because in a system you can't debug live, the failures you can't reproduce are the ones that cost the most.

---

## Q2: What's a "best practice" you disagree with after real-world experience?

I've become skeptical of the belief that a green staging run gives you real confidence something will work in production. The common wisdom is "if it passes in staging, it's safe to ship" — but watching code pass cleanly in dev and then break in prod on IAM and security-policy differences taught me that staging only validates what it actually mirrors. The things that diverge silently between environments — permissions, policies, scale, real data shapes — are exactly the things most likely to break you, and staging won't catch any of them.

I still value staging. It catches logic bugs and integration failures that would be obvious regressions. But I no longer treat a green staging run as proof of production safety. I now assume prod has differences staging can't capture, and I validate the specific things most likely to diverge directly and explicitly, rather than trusting that "it worked in dev" transfers. The shift is treating staging as necessary but not sufficient, and building deliberate checks for the things staging structurally cannot test.
