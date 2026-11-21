---
name: add-stand-chat
description: Add Stand Chat to a website so visitors can chat with the owner, or with their Stand-in, an AI assistant they name and brief. Use when the user asks to add stand.chat, Stand or Stand Chat to their website, install the Stand chat, or set up a Stand-in for a website. Needs the website project and a shell; installs the Stand CLI from npm.
---

# Add stand.chat with a coding agent

Use this recipe when the user asks to add Stand Chat to their website. The user needs no existing Stand account. You need authorized access to the website project, a shell with Node.js 22+, network access, and a real browser for verification. On Windows, run the CLI in WSL. It also runs on macOS and Linux.

If you cannot edit the project or run commands, direct the user to open the website project in a coding agent and repeat “Add stand.chat”, or share the [manual installation steps](https://stand.chat/guide/quickstart#setup-checklist). If the user wants to ask about visitor conversations or change Site settings from their AI chat, follow the [MCP guide](https://stand.chat/guide/mcp).

## 1. Discover and install

The user only needs to say **“Add stand.chat”** in their website project. Discover this recipe through the setup links on https://stand.chat/ or https://stand.chat/llms.txt. Do not ask the user to supply a guide URL, paste a longer prompt, or explain Stand's setup steps. Follow the complete flow below, asking only for missing project decisions and the human signup/consent steps.

Read https://stand.chat/agent-setup.json and the OpenAPI contract at https://stand.chat/api/v1/openapi.json. Install the official package from npm’s `latest` tag with `npm install --global @standchat/cli`, then run `stand --version` and `stand commands --json`. Record the installed version for troubleshooting. If the official package is unavailable, stop and report that distribution is unavailable; never substitute an unrelated package or ask for pasted access tokens.

Inspect the intended project, its framework, existing Stand snippet and actual deployment hostname. Reuse a matching installation. Ask only when the project, account, website or approved content is ambiguous. The user's setup request authorizes creating and enabling dedicated resources for this website, crawling its approved public content and editing the authorized project. It does not authorize changing unrelated Sites, existing Stand-ins, billing, integration tools, or sharing private project files.

## 2. Human signup and consent

Run `stand auth login --setup --json`. Keep the process running while showing the user the trusted browser verification URL and user code emitted by the CLI. For a new account, offer Google signup on the authorization page. Alternatively, the user can complete the existing email signup at https://stand.chat/#register first, then return to CLI authorization with that same account. Existing accounts use their supported sign-in method and required-provider rules. The human completes signup/sign-in, terms and OAuth consent. Never automate accepting terms, ask for passwords, or copy browser cookies.

After approval, the browser shows **Signed in to Stand** and says it can be closed. Wait for the CLI to confirm that credentials were stored, then return to the terminal. This confirms authorization, not account onboarding or installation.

Run `stand setup status --json`. If it reports incomplete onboarding, open its `onboardingUrl` for the user to complete the existing Stand profile form. Use the same account as `stand whoami --json`. The user may need this additional browser step. Repeat the read after completion. Do not use profile writes to complete onboarding or create a second account to bypass an invitation, required provider or disabled organization.

Credentials stay in the CLI credential store, outside the website and repository. On a host without an available secure vault, choose `--credential-store file` explicitly; it uses a private local credential file. Do not silently change storage mode. Use the same named CLI profile throughout. Access tokens are short-lived; the CLI renews with proof-bound refresh within existing session limits. If authorization expires or is revoked, ask the human to authorize again and resume by inspecting existing resources.

## 3. Inspect before writing

Run `stand sites list --json`, `stand standins list --json`, `stand standins status --json`, `stand knowledge list --json`, `stand org plan --json` and `stand org usage --json`.

Read each command's schema with `stand schema OPERATION_ID --json` before constructing a body. Eight business permissions cover this flow: profile:read, org:read, sites:read/write, standins:read/write and knowledge:read/write. This setup grant does not include profile writes, conversations or integration tools. The broader [API and CLI guide](https://stand.chat/guide/api-cli.md) documents management operations and additional scopes; request them only for a separately authorized task.

Record the account, target domain, Site ID, Stand-in ID, knowledge-base ID and crawl task ID in your task progress as each operation succeeds. These identifiers are not credentials. On resumption, read and verify those resources before creating anything. If a write times out or returns an unknown outcome, inspect the resource lists; do not blindly retry it. If the result remains ambiguous, stop for reconciliation. Names alone are not unique identities.

## 4. Create the Site and a disabled Stand-in

Create a missing Site with `stand sites create --domain example.com --name "Example" --yes --json`. Use the real intended hostname, not a URL path. Preserve the returned `siteId` and `snippet`. If reusing a disabled Site, inspect its existing settings and obtain approval before enabling it with `stand sites update SITE_ID --body-file site.json --yes --json`. Preserve existing behavior. Site JavaScript experimentation and Stand-admin behavior access can only be enabled by the owner in the browser; basic installation does not need either permission.

Create a new Stand-in with `stand standins create --body-file standin.json --yes --json`. Example body (replace placeholders with approved facts):

```json
{
  "name": "Example website",
  "displayName": "Example assistant",
  "displayBrandName": "Example",
  "greeting": "What would you like to know about Example?",
  "prompt": "Answer questions about Example using the approved website knowledge. Say when the available information does not answer the question. Do not invent prices, policies or capabilities.",
  "siteId": "SITE_ID",
  "pathPrefix": "",
  "toolsEnabled": "{}"
}
```

The response is disabled. Retain its ID. Keep instructions within `org plan`'s promptCharacterLimit. Base supports this workflow without payment. For an unpublished project, approved factual project content can fit within the instruction limit; never upload private source/configuration merely because it is readable. Existing Stand-ins can be updated through `stand standins update STANDIN_ID --body-file standin.json --yes --json` when the user approves those changes. Inspect the current object and preserve unrelated configuration; a request to add this website does not authorize replacing someone else's briefing. Stand-in saves require the complete writable configuration and can return a replacement version; use the returned Stand-in ID for subsequent calls.

## 5. Add approved knowledge

Read `stand knowledge list --json` before choosing a source. Its `capabilities`, `ingestionAllowed`, `ingestionUnavailableReason`, `graceReadOnly` and usage describe the account's current options. Base supports approved facts in Stand-in instructions and one website knowledge base with up to ten indexed pages; payment, document uploads and sitemaps are not required. Reuse the intended approved base if it exists. Do not overwrite or repurpose unrelated knowledge to get around a plan limit.

If approved Stand-in instructions are sufficient and there is no approved source to ingest, skip base creation and attachment and continue to installation; do not claim indexed knowledge. Otherwise, create a missing base with `stand knowledge create --body-file knowledge.json --yes --json`, using `{"name":"Example website","description":"Approved public website content"}`. Keep its ID. A knowledge base has no Site ownership field in this API; the attachment links it to the Stand-in.

Choose the approved source that fits the account:

- **Public website:** queue a crawl with `stand knowledge crawl KNOWLEDGE_BASE_ID --body-file crawl.json --yes --json`. Start with `{"domain":"example.com","urlPrefix":"https://example.com/","maxDepth":0}`. More approved pages may be added within returned limits.
- **Local document:** only when `capabilities.documentUploadAllowed` and `ingestionAllowed` are true and `graceReadOnly` is false, use `stand knowledge upload KNOWLEDGE_BASE_ID --file /absolute/path/approved-document.md --yes --json`. Supported files are PDF, Word DOCX, Markdown (`.md`, `.markdown`) and plain text (`.txt`), up to 10 MiB each. Uploading does not require publishing the document on a public website. Get specific approval before sending private local content to Stand, and include only material appropriate for visitor answers; readability in the project is not approval. Do not upload secrets, configuration or unrelated source files. If uploads are unavailable, use approved facts within the instruction limit or crawl approved public content; do not publish private documents just to make them crawlable.

Retain the returned task and source IDs. Read `stand knowledge tasks KNOWLEDGE_BASE_ID --json`, `stand knowledge sources KNOWLEDGE_BASE_ID --json` and `stand knowledge documents KNOWLEDGE_BASE_ID --json` until the intended task finishes and usable documents are indexed. Inspect the intended document with `stand knowledge documents chunks DOCUMENT_ID --json`. A queued task, completed file transfer or empty document list is not success. Explain failed or unavailable ingestion; do not claim that knowledge is ready. The [API and CLI guide](https://stand.chat/guide/api-cli.md#knowledge-management) covers revised documents, sitemaps and ongoing management.

Attach with `stand standins knowledge set STANDIN_ID --body-file attachments.json --yes --json`, using `{"knowledgeBaseIds":["KNOWLEDGE_BASE_ID"]}`. This replaces the whole attachment list: first read `stand standins knowledge get STANDIN_ID --json` and preserve approved existing IDs. No conditional revision guarantee is available. Stop if another editor changes a reused configuration unexpectedly.

## 6. Enable and install

Read the Site, Stand-in and account-wide status separately. Enable the configured Stand-in with `stand standins set-enabled STANDIN_ID --enabled true --yes --json`. If the master switch is off, inspect existing Stand-ins before `stand standins master --enabled true --yes --json`: this enables eligibility for other enabled Stand-ins too. Disclose and obtain approval for effects beyond this new chat. Do not change human availability.

Use the snippet returned for the user's Site, never the homepage's `demo` Site ID. Install the returned Site `snippet` once, using the framework's supported client/script lifecycle. Preserve its public Site ID and script URL. In React/Next.js, use the framework's script component and avoid duplicate mounts during navigation. For static HTML, use the supplied script markup. Preserve the site's CSP and consent requirements; do not disable protections to make the widget appear. Never put OAuth credentials in client code, markup, source control or build variables.

Use authorized deployment access. If you cannot deploy the website, explain the exact remaining action and label the installation incomplete.

## 7. Verify a working visitor chat

Open the actual target hostname in a real browser. A production Site ID on an unrelated preview hostname can fail domain matching. Verify one widget instance, correct Site identity, successful loading and an eligible responder. Use `stand sites matches --json` as a diagnostic, not a substitute for browser verification.

Start a fresh visitor chat and ask about an approved fact from the configured content. Verify a substantive answer from the intended Stand-in. Also ask a question whose answer is absent; the assistant should acknowledge that limit. A script tag, installed flag, spinner, successful API response, or greeting alone is not a completed chat. Account for existing routing and any human representative still active in the dashboard; do not silently alter their settings.

Report the website URL, resources created/reused, code changes, knowledge outcome and actual chat verification. State any unfinished deployment or verification step. Do not report completion until a real visitor message received an answer.


### Contextual CLI help

Use `stand --help` for groups, `stand auth login --help` for command details and
`stand help workflows` for offline recipes. Explicit help remains text even with
`--json`. Machine discovery is `stand commands --json`; operation schemas include
transitive references, with `stand schema OPERATION_ID --full` for all components.
`setup status` identifies `assessment: "account_readiness_only"` and lists
website installation and visitor chat in `notChecked`. Its legacy configurationCreated and
chatVerified fields describe checks not performed, not the installed website state.
Ingestion progress is internal work units, not page counts. Inspect completed tasks
and indexed documents before relying on new content.
