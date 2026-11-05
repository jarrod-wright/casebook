# n8n security and platform changes, December 2025 to November 2026

Advisory review. Reviewed 5 November 2026. Written by Jarrod Wright.

## The event

n8n's 2026 disclosures repeatedly involved code execution, credentials and access between projects.
Over the same period, n8n expanded agent tools and announced another major version.
I reviewed who could execute code, reach secrets or publish workflows. [Official advisories][S001], [Agents announcement][S002] and [version 3 announcement][S003]

My inventory covered advisory publication dates from 1 December 2025 through 5 November 2026.
It contained 229 distinct records after excluding withdrawn duplicates: 95 carried CVE identifiers and 134 did not.
Those were disclosure counts; I did not treat them as counts of attacks or compromised installations. [n8n advisory records][S001]

The count combined 228 records from n8n's repository with CVE-2026-1470, which n8n's February bulletin referenced.
I checked every assigned CVE against its own record and kept bulletin dates separate from record publication dates.
That distinction mattered when an advisory appeared before its CVE record. [February bulletin][S004] and [CVE-2026-1470][S005]

December began with two different changes to execution.
The 8 December Git-node advisory described remote code execution through Git hooks and named 1.119.2 as the fixed release.
On that date, n8n also announced its 2.0 beta, with task runners enabled by default and tighter access to host capabilities. [CVE-2025-65964][S006] and [n8n 2.0][S007]

CVE-2025-68613 followed on 19 December.
It described expression injection that let an authenticated user execute code in the n8n process.
The record listed fixes in 1.120.4, 1.121.1 and 1.122.0, so I retained the release-specific details instead of reducing them to one timeless minimum. [CVE-2025-68613][S008]

The Pyodide issue, CVE-2025-68668, concerned Python Code nodes and escape from their execution restrictions.
Its CVE record appeared on 26 December, two days after the GitHub advisory.
The record identified versions from 1.0.0 below 2.0.0 as affected, with 2.0.0 carrying the fix. [CVE record][S009] and [n8n advisory][S010]

January added an exposure that did not require an editor account.
CVE-2026-21858 described unauthenticated file access through particular form workflows, with a CVSS 3.1 score of 10.0.
Its affected range began at 1.65.0 and ended before the fixed 1.121.0 release. [CVE-2026-21858][S011]

n8n's 8 January notice narrowed that scenario to an active Form Submission workflow using Form Ending to return a binary file.
It said the patch had shipped on 18 November.
The disclosure therefore belonged in this review, although its patch predated the review window. [n8n's January notice][S012]

CVE-2026-21877 concerned authenticated arbitrary file writing that could lead to code execution; its record named 1.121.3 as fixed.
The published structured range and narrative used different affected-version descriptions.
I kept that discrepancy visible in my evidence rather than presenting an inferred correction as an upstream fact. [CVE-2026-21877][S013]

The subsequent expression issue was CVE-2026-25049.
Its record described an authenticated workflow editor bypassing restrictions and named 1.123.17 and 2.5.2 as fixes.
The similar identifier CVE-2025-25049 was a rejected, unused record; it did not substantiate an unauthenticated n8n webhook claim. [CVE-2026-25049][S014] and [rejected record][S015]

February's disclosures crossed several execution paths.
They included further expression evaluation, Python sandbox and JavaScript task-runner weaknesses, plus code execution through Merge-node SQL and Git-related file writing.
Reading only an expression headline would have missed distinct node types and prerequisites. [CVE-2026-25115][S016], [CVE-2026-27495][S017], [CVE-2026-27497][S018] and [CVE-2026-27498][S019]

The bulletin titled 25 February was posted on 26 February and grouped seven high or critical issues.
It named 1.123.22, 2.9.3 and 2.10.1 as the corresponding release-line patches.
I read those as the bulletin's combined coverage, while retaining each underlying advisory's own affected range and fixed versions. [February bulletin][S020]

The pattern continued into spring.
March records included Merge-node SQL execution and prototype pollution in Google Workspace processing.
April added XML issues; May disclosed a bypass of an earlier fix. [CVE-2026-33660][S021], [CVE-2026-33696][S022], [CVE-2026-42231][S023], [CVE-2026-42232][S024] and [CVE-2026-44791][S025]

That May bypass changed how I interpreted a previously completed patch task.
A recorded upgrade demonstrated that a particular release had been installed; later findings could still reopen the same feature for review.
I would have retained the original test evidence and added a new test for the newly disclosed path. [XML fix-bypass record][S025]

n8n's disclosure policy dated its move to updates every other Wednesday to 13 May.
It retained immediate notification for urgent critical issues.
I treated that cadence as a regular review appointment, with separate handling for urgent notices between scheduled updates. [n8n disclosure policy][S026]

June and July disclosures also tested credential boundaries.
One advisory described expressions resolving external-vault secrets despite the workflow editor lacking permission to that vault.
Another described a mismatch between credential validation and execution that exposed secret values. [Vault-expression advisory][S027] and [credential-validation advisory][S028]

August's records included a prototype-reference leak through the agent-tool parameter helper and Git configuration that enabled code execution.
Their relevance depended on the capabilities a workflow could invoke.
For this review, those examples connected the earlier execution failures to the expanding agent interface. [Agent-tool advisory][S029] and [Git configuration advisory][S030]

The September-to-October updates continued with project access, secret disclosure and node-specific weaknesses; [entry 001][S031] recorded those five bulletins.
This wider review also found a separate 3 September OAuth advisory and a bulletin-versus-record severity difference.
I therefore kept the five-bulletin count separate from the full date-window inventory. [OAuth advisory][S032] and [credential-error advisory][S033]

On 27 October, n8n announced version 3 as a beta.
Its announcement described Docker-only self-hosting and a planned stable release in early December.
My 5 November check still put 2.46.6 on stable; the version-3 patch reference did not establish channel status. [Version 3 announcement][S003] and [npm distribution tags][S034]

These sources documented vulnerabilities, mitigations and platform changes.
I found no official incident report in the reviewed material that established a breach narrative for this entry.
That research limit left exploitation and impact on any particular installation unproven. [Official security notices][S035]

## What it changes for an n8n or agent operator

My first concern was the authority attached to workflow editing.
Several critical records required an authenticated workflow editor.
A login requirement narrowed access while leaving an execution boundary that needed protection. [Expression advisory][S014] and [task-runner advisory][S017]

Version 2 tightened defaults, including environment-variable access and availability of command-execution nodes.
Its separate save and publish actions also changed when edits became active.
I would have checked the deployed configuration and publication procedure, because a release announcement alone did not establish how an existing instance was configured. [Version 2 migration notes][S036]

The February JavaScript task-runner record distinguished internal execution from an external runner.
It described possible host compromise in internal mode and impact on other runner tasks in external mode.
I would have recorded that distinction before assessing the exposure, because enabling task runners alone did not establish the isolation in use. [CVE-2026-27495][S017]

My proposed runner test would have used a separate environment with harmless sample data.
I would have checked the process boundary and the resources assigned to that runner, then retained the observed result.
That was a proposed configuration review, not a claim that external execution removed every possible sandbox failure. [Task-runner advisory][S037]

Execution isolation needed a workflow inventory as well.
The 28 October legacy-node advisory said Function, Function Item and LangChain Code paths could bypass task runners.
Its patch blocked newly saved or modified workflows containing those nodes, while existing workflows continued running. [Legacy-node advisory][S038]

That existing-workflow detail was consequential.
My review plan included finding those node types in stored workflows, then testing their replacement before publication.
A check limited to the installed version would not have established whether old workflows still exercised the legacy path. [Legacy-node patch behavior][S038]

Public inputs required a different inventory.
The January form issue depended on a particular form-and-binary-response arrangement, while the spring XML issue involved parsing incoming content.
I would have listed exposed triggers and response behavior before deciding which advisories applied to each workflow. [Form record][S011] and [Webhook XML record][S023]

I also retained the XML record's workflow-editing prerequisite.
Its description linked crafted XML processing with Git SSH operations; describing it as any public webhook being exploitable would have discarded that context.
For an exposure decision, I would have read the impact narrative alongside the affected versions and scoring vector. [CVE-2026-42231][S023]

The 12 May changelog added signature verification to fourteen trigger integrations.
It also said existing webhook registrations without a stored signing secret retained their previous behavior.
My proposed check therefore included registration history and a test of an invalid signature, rather than assuming every older endpoint inherited the new default. [n8n changelog][S039]

Credential controls became more granular during the year.
March changes introduced project-scoped external secret stores and then project-team management.
The documented feature had Enterprise requirements, so I would have verified the available edition before treating project vaults as an implemented control. [External-secret documentation][S040] and [dated changelog][S039]

The vault advisories made a useful distinction between storing a secret and authorizing its use.
I would have tested both with an ordinary project member, using a synthetic value.
My acceptance condition covered direct expression access and execution of credentials created by a role lacking the required secret permission. [Vault-expression advisory][S027] and [validation advisory][S028]

Execution-data redaction addressed another boundary: who could see stored inputs and outputs.
The Enterprise documentation described controlled reveal access and audit events.
I treated that as a visibility control; it did not justify claiming that execution data had been erased or encrypted by redaction. [Execution-data redaction][S041]

Agent controls expanded alongside these disclosures.
January brought human review for individual tool calls, and July's Slack approval changes added response timestamps and restrictions on who could approve.
My proposed tests covered an allowed reviewer, a different user and a stale approval attempt, using a harmless tool action. [Dated changes][S039], [tool review][S042] and [Slack approvals][S043]

January's Chat Hub announcement brought shared access to models and workflow agents.
The dedicated chat-user role was restricted to specified paid plans.
I would have tested that role's access separately from an editor account before using shared chat as the entry point to an operational workflow. [Chat Hub announcement][S044]

The April instance-MCP announcement added workflow creation and updating through an external assistant.
n8n distinguished this instance interface from a workflow's MCP Server Trigger.
That changed my access review: I would have checked who could modify automation through MCP as well as through the browser editor. [n8n MCP announcement][S045]

I would also have separated permission to draft a workflow from permission to publish it.
The MCP announcement described a model-driven build-and-test process, but that did not establish a guaranteed test before every change.
My proposed acceptance check required an observable test result and an explicit publication decision. [MCP workflow-building behavior][S045]

Model choice acquired more operational detail.
April's releases added Kimi, Alibaba Cloud and MiniMax integrations; June added NVIDIA embeddings.
My proposed provider tests covered tool arguments and failure handling, without assuming improved accuracy. [Dated integration changes][S039]

The self-hosted Assistant documentation described sending prompts and relevant workflow or execution context to the configured model provider.
Its setup also required Docker and carried preview qualifications.
My review plan included identifying the destination and selecting synthetic test data before enabling that path. [Self-hosted Assistant setup][S046]

Cloud Gateway offered a shared prepaid balance across supported providers, with node-level choices about the credential source.
Its documentation excluded Enterprise and self-hosted installations.
I would have recorded the eligible nodes using that balance separately from nodes using their own provider credentials. [Cloud Gateway credits][S047]

n8n's September Agents announcement described tool approvals, memory and publication while identifying the feature as a preview.
It also said the existing AI Agent node remained available.
I would have evaluated the new interface separately before moving an established workflow into it. [Agents announcement][S002]

October's Gemini 4 Argon article described its use through MCP, Agents and the Assistant, including compatible provider endpoints.
My review drew no model-performance conclusion from that integration announcement.
The proposed evidence was a recorded result from the actual workflow and provider combination under consideration. [n8n's Gemini integration article][S048]

Enterprise node policies described instance and project restrictions, but their documentation identified them as a preview and advised against production reliance.
Nodes remained available without a restricting policy, and a deny decision took precedence.
My proposed evaluation belonged in a test environment, with the effective result checked for each project role. [Node availability controls][S049]

Version 3 required a migration plan beyond replacing an image tag.
Its notes described changes to manual execution in queue mode, timeouts, encryption-key handling and legacy functionality.
They also described rollback through restoration of a pre-upgrade backup, which made a restore rehearsal part of my proposed migration work. [Version 3 migration notes][S050]

The same notes directed version-1 installations through version 2 before version 3 and documented a migration report.
The announcement gave 31 December 2026 as the planned end of version-1 support.
I would have scheduled that work against the announced date while checking the current support statement again before making a production change. [Migration notes][S050] and [version 3 announcement][S003]

## A check you can run

On 5 November I ran the package-registry check below.
It requested public distribution tags and returned stable and latest as 2.46.6, with beta, next and rc at 2.47.2.
This measured the registry at that time; it did not inspect an n8n installation. [npm distribution tags][S034]

```sh
npm view n8n dist-tags --json --registry=https://registry.npmjs.org
```

I then read the newest official security post in full.
The 28 October post named fixes for the version-1, stable, beta and version-3 lines.
My comparison rule was the current supported release line and its applicable advisories, with another measurement on the day of any upgrade. [28 October security update][S051]

For an individual installation, my proposed first step was recording its displayed version and deployment method.
I would have compared those with the current upstream release information before choosing a migration path.
I did not run that installation check during this source review. [n8n release information][S052]

The next proposed step was a read-only workflow inventory: public forms, XML parsing, legacy code nodes, Git operations and agent tools.
Each item would be matched to the relevant advisory's prerequisites.
The output would contain node types and review results, with credentials and execution payloads excluded. [Form record][S011] and [legacy-node record][S038]

For each match, I would have retained the advisory identifier and the exact release line under review.
A conflicting version field would remain an unresolved source discrepancy until checked against the underlying record and release.
The September OAuth advisory showed why I would not silently turn inconsistent patch fields into an operational instruction. [OAuth advisory][S032]

My planned permission checks used an isolated test instance and synthetic secrets.
They covered a project member attempting an unauthorized vault reference, an unapproved tool action and a blocked node.
A useful result would record the role, attempted action, expected rejection and observed behavior. [Vault advisory][S027], [tool review][S042] and [node policies][S049]

The final proposed check was restoring a pre-upgrade backup into a separate environment and running representative workflows there.
I would have recorded the restored version and the test outcomes before approving a migration.
None of these proposed instance tests were presented as completed evidence in this review. [Version 3 rollback guidance][S050]

## What I would do

As of 5 November, I would have started with exposed input paths and the people allowed to edit workflows.
The form disclosure and authenticated execution issues described different ways to reach consequential behavior.
My priority order would have followed the actual reachable feature and privilege on the installation under review. [Form record][S011] and [expression record][S014]

I would have assigned one owner to the recurring advisory review and another person to verify the resulting changes.
The every-other-Wednesday notices provided the regular prompt; urgent notifications would have triggered an earlier review.
Each decision would have recorded the applicable record, affected workflow, selected release and evidence of the resulting test. [Disclosure cadence][S026]

For agent workflows, I would have required human approval around consequential external actions and tested the refusal path.
A proposed tool call would remain pending until the designated reviewer approved it.
I would have treated the model's explanation as input to that decision, with the approval mechanism providing the recorded control. [Human review for tools][S042]

I would have kept migration work separate from enabling a new agent interface or changing providers.
That would have left a smaller change to diagnose when a test failed.
My proposed evidence included the model, provider route, workflow revision and observed tool arguments for each candidate configuration. [Assistant provider configuration][S046] and [MCP workflow changes][S045]

My proposed test set included normal requests and deliberate failures.
It covered an unauthorized request and a failed external service, with expected tool choices and arguments recorded before any publication decision.
These were acceptance criteria for review, with no assertion that a model or workflow had already passed them. [Agent tool controls][S042]

Before a version-3 move, I would have required a tested restore and a review of the migration report.
I would also have retained the earlier workflow inventory so legacy behavior could be checked after the move.
The recorded acceptance decision would name the tests that passed and the unresolved limits. [Version 3 migration guidance][S050]

## Sources

Sources were read on 4 and 5 November 2026. Inline links identify the CVE records, n8n advisories and official announcements supporting each passage. Counts reflected the records returned during that review, and proposed checks were distinguished from tests performed.

[S001]: https://github.com/n8n-io/n8n/security/advisories
[S002]: https://blog.n8n.io/introducing-n8n-agents/
[S003]: https://blog.n8n.io/introducing-n8n-v3/
[S004]: https://community.n8n.io/t/security-bulletin-february-6-2026/261682
[S005]: https://cveawg.mitre.org/api/cve/CVE-2026-1470
[S006]: https://cveawg.mitre.org/api/cve/CVE-2025-65964
[S007]: https://blog.n8n.io/introducing-n8n-2-0/
[S008]: https://cveawg.mitre.org/api/cve/CVE-2025-68613
[S009]: https://cveawg.mitre.org/api/cve/CVE-2025-68668
[S010]: https://github.com/n8n-io/n8n/security/advisories/GHSA-62r4-hw23-cc8v
[S011]: https://cveawg.mitre.org/api/cve/CVE-2026-21858
[S012]: https://community.n8n.io/t/security-advisory-security-vulnerability-in-n8n-versions-1-65-1-120-4/247305
[S013]: https://cveawg.mitre.org/api/cve/CVE-2026-21877
[S014]: https://cveawg.mitre.org/api/cve/CVE-2026-25049
[S015]: https://cveawg.mitre.org/api/cve/CVE-2025-25049
[S016]: https://cveawg.mitre.org/api/cve/CVE-2026-25115
[S017]: https://cveawg.mitre.org/api/cve/CVE-2026-27495
[S018]: https://cveawg.mitre.org/api/cve/CVE-2026-27497
[S019]: https://cveawg.mitre.org/api/cve/CVE-2026-27498
[S020]: https://community.n8n.io/t/security-bulletin-february-25-2026/270324
[S021]: https://cveawg.mitre.org/api/cve/CVE-2026-33660
[S022]: https://cveawg.mitre.org/api/cve/CVE-2026-33696
[S023]: https://cveawg.mitre.org/api/cve/CVE-2026-42231
[S024]: https://cveawg.mitre.org/api/cve/CVE-2026-42232
[S025]: https://cveawg.mitre.org/api/cve/CVE-2026-44791
[S026]: https://blog.n8n.io/how-n8n-handles-vulnerability-disclosure-and-why-we-do-it-this-way/
[S027]: https://github.com/n8n-io/n8n/security/advisories/GHSA-2434-3x6q-8r99
[S028]: https://github.com/n8n-io/n8n/security/advisories/GHSA-jp7m-xcgx-57qm
[S029]: https://github.com/n8n-io/n8n/security/advisories/GHSA-9x83-43r8-5hwc
[S030]: https://github.com/n8n-io/n8n/security/advisories/GHSA-mwp5-2m32-r54h
[S031]: 001-n8n-advisories-sep-oct-2026.md
[S032]: https://github.com/n8n-io/n8n/security/advisories/GHSA-cw9w-vv67-hf73
[S033]: https://github.com/n8n-io/n8n/security/advisories/GHSA-qmvv-q3c9-wpp7
[S034]: https://registry.npmjs.org/-/package/n8n/dist-tags
[S035]: https://community.n8n.io/c/security-advisories/40
[S036]: https://docs.n8n.io/2-0-breaking-changes.md
[S037]: https://github.com/n8n-io/n8n/security/advisories/GHSA-jjpj-p2wh-qf23
[S038]: https://github.com/n8n-io/n8n/security/advisories/GHSA-9p7v-45m2-wxgj
[S039]: https://docs.n8n.io/changelog/readme.md
[S040]: https://docs.n8n.io/administer/manage-credentials/use-external-secret-stores.md
[S041]: https://docs.n8n.io/deploy/host-n8n/configure-n8n/security/redact-execution-data.md
[S042]: https://docs.n8n.io/build/integrate-ai/ai-examples/human-in-the-loop-for-tools.md
[S043]: https://docs.n8n.io/integrations/builtin/app-nodes/n8n-nodes-base.slack/approvals.md
[S044]: https://blog.n8n.io/introducing-chat-hub/
[S045]: https://blog.n8n.io/n8n-mcp-server/
[S046]: https://docs.n8n.io/deploy/host-n8n/configure-n8n/set-up-n8n-assistant.md
[S047]: https://docs.n8n.io/deploy/use-n8n-cloud/gateway-credits.md
[S048]: https://blog.n8n.io/build-n8n-workflows-with-gemini-4-argon-mcp-the-assistant-and-agents/
[S049]: https://docs.n8n.io/administer/manage-users-and-access/control-node-availability.md
[S050]: https://docs.n8n.io/changelog/v30-breaking-changes.md
[S051]: https://community.n8n.io/t/security-update-28-october-2026/325135
[S052]: https://docs.n8n.io/changelog/release-notes.md
