# 001. n8n security updates, September to October 2026

Advisory review. Published 2026-11-04. Facts as of 2026-11-01.

## What happened

Between 2 September and 28 October 2026, n8n published five security updates covering 75 advisories, 35 of them rated High. n8n now publishes its security fixes as a bi-weekly update.

| Update | Advisories | High | Fixed in stable 2.x | Fixed in 1.x |
|---|---|---|---|---|
| 2 September | 18 | 5 | 2.37.7 | 1.123.76 |
| 16 September | 16 | 12 | 2.39.6 | 1.123.80 |
| 1 October | 14 | 10 | 2.41.4 | 1.123.83 |
| 14 October | 15 | 5 | 2.43.5 | 1.123.85 |
| 28 October | 12 | 3 | 2.45.5 | 1.123.89 |

## What it changes for you

If you self-host n8n below 2.45.5 on the stable line, or below 1.123.89 on 1.x, these fixes are missing from your instance. n8n Cloud is patched by n8n.

Some of these need no n8n account at all. The 14 October update fixed a Slack Trigger webhook that could leak its token without authentication (GHSA-3c3p-5959-3ghr). The 28 October update fixed an embedded chat that did not enforce user auth on every request (GHSA-pwj5-wwm6-g83j).

Others name an ordinary member account as the attacker, for example a member spoofing another workflow's identity to use its credentials (GHSA-866p-xg8v-g2q7, 1 October). If people outside your core team can edit workflows, count those as reachable too.

Each update also names a beta version. The beta is a prerelease. The fix for a production instance is the stable version in the table, or any later stable release.

## A check you can run

Run this on the host running n8n to see your version.

~~~bash
docker compose exec n8n n8n --version
~~~

Run this anywhere with Node.js to see the newest stable release.

~~~bash
npm view n8n dist-tags.stable
~~~

If the first number is below 2.45.5, or below the second number, you are behind.

## What I would do

Upgrade to the newest stable release, not the beta number. n8n fixes these advisories in the stable line, and the beta is a prerelease.

Before upgrading, back up the instance. The database and the encryption key both need to be in that backup, because a restore only comes back with the same key.

Read the release notes between your version and the target before you start, and look for changed environment variables.

Then re-run the check above and confirm the new version. The reference deployment in [n8n-hardened-reference](https://github.com/jarrod-wright/n8n-hardened-reference) was re-pinned to the current stable release, with digests, a verify script and a boot test you can copy.

## Sources

| Source | Link |
|---|---|
| n8n security update, 2 September 2026 | https://community.n8n.io/t/security-update-02-september-2026/310852 |
| n8n security update, 16 September 2026 | https://community.n8n.io/t/security-update-16-september-2026/314102 |
| n8n security update, 1 October 2026 | https://community.n8n.io/t/security-update-01-october-2026/318054 |
| n8n security update, 14 October 2026 | https://community.n8n.io/t/security-update-14-october-2026/321284 |
| n8n security update, 28 October 2026 | https://community.n8n.io/t/security-update-28-october-2026/325135 |
| n8n advisories on GitHub | https://github.com/n8n-io/n8n/security/advisories |
| n8n release channels on npm | https://www.npmjs.com/package/n8n |
