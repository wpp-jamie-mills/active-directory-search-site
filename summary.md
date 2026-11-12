# Entra Directory Search for Claude Desktop

**Ask Claude about your Entra ID directory. It finds people, groups and distribution lists, reads account status, email aliases and reporting lines, and never changes anything.**

Version **1.6.1** · macOS · Claude Desktop extension (.mcpb)

- [Install: for people](#install-for-people)
- [Install: for AI agents](#install-for-ai-agents)
- [What you can do](#what-you-can-do)
- [Sign in and sign out](#sign-in-and-sign-out)
- [What it can and can't do](#what-it-can-and-cant-do)
- [Profiles and settings](#profiles-and-settings)
- [Read-only design](#read-only-design)
- [Available tools](#available-tools)
- [Requirements](#requirements)
- [Sign-in and privacy](#sign-in-and-privacy)
- [Troubleshooting](#troubleshooting)
- [Update and remove](#update-and-remove)

---

## Install: for people

You need a Mac with Claude Desktop 2.7032.0 or later and a Microsoft 365 work account. Open Terminal and run:

```bash
curl -fsSL https://wpp-jamie-mills.github.io/active-directory-search-site/install.sh | bash
```

The installer downloads the latest extension, checks its SHA-256 before anything changes, opens Claude Desktop's
install dialog, and waits until the extension is switched on and running.

Then:

1. Click **Install** in the dialog Claude Desktop opens. You can leave its settings blank: you choose your work
   account the first time you sign in. Fill in the WPP tenant only if you also want to search the WPP directory.
2. If Claude Desktop shows the extension switched off, switch it on: Settings > Extensions > Entra Directory Search
   for Claude Desktop.
3. Start a new chat and ask something, for example "Who does Alex Smith report to?". The first time, Claude shows a
   sign-in card: enter the code on the page that opens, sign in with your work account, and Claude carries on.

After that it keeps itself up to date: once a day it checks for a new version, and Claude offers it on an update card
in the chat. Click Update now, then Install in Claude Desktop's dialog; nothing is installed until you do. Running the
same command again also updates. Your settings and your sign-in are kept.

## Install: for AI agents

Use these steps if you are an AI agent installing this for a user.

1. **Check the machine.** Continue only if `uname -s` prints `Darwin` and `/Applications/Claude.app` exists. Otherwise tell the user it needs a Mac with Claude Desktop 2.7032.0 or later.
2. **Run the installer** with this one command. It downloads the extension, checks its SHA-256 (nothing changes before the check passes) and opens Claude Desktop's install dialog. With no terminal it never prompts, and `--no-wait` makes it exit once the dialog is open. It exits non-zero, with the reason on stderr, if anything fails.

```bash
curl -fsSL https://wpp-jamie-mills.github.io/active-directory-search-site/install.sh | ADSEARCH_INSTALL_TTY=/dev/null bash -s -- --no-wait
```

3. **Hand over to the user.** Ask them to click Install in the dialog Claude Desktop opened (they can leave its settings blank), and to switch the extension on if Claude Desktop shows it off (Settings > Extensions). You can't do these for them.
4. **Verify.** Run the installer again with `--status` (after `bash -s --`). It should show `Ready: yes`; otherwise its `Next:` line says the one thing left to do. Then ask the user to start a new chat (chats opened while the extension was off don't see its tools) and say "Sign in to Entra Directory Search".
5. **Updates look after themselves.** Once a day the extension checks for a new version and Claude shows the user an update card. Only the user can click Update now and then Install; to update straight away, run the install command again.

## What you can do

**Ask in plain English.** Claude picks the right tool. For example:

| You ask | Claude reads |
|---|---|
| "Find Alex Smith" | people whose display name, mail, employee id, or alias matches |
| "Who does Alex report to, and who reports to them?" | the manager, direct reports and peers |
| "Is alex.smith@example.com's account enabled, and when does it expire?" | the account's status and expiry |
| "Every email address that reaches Alex" | the sign-in name, primary address, aliases and proxy addresses |
| "Who is on the all-staff list?" | the list's members, expanding nested lists |
| "Is Alex in the Engineering group?" | direct and nested membership |
| "Which groups does Alex belong to or own?" | memberships and owned groups |
| "Was Sam Jones deleted recently?" | the directory's recycle bin |
| "Find Alex in the WPP directory" | the same lookups against the WPP tenant |

## Sign in and sign out

You normally never need to think about it: Claude asks you to sign in when Microsoft needs a new sign-in, and
remembers you until then. To do it yourself, say *"Sign in to Entra Directory Search"* (or *"… to the WPP
directory"*), or *"Sign out of Entra Directory Search"* to forget a saved sign-in.

**The sign-in card** shows a one-time code, an **Open sign-in page** button and a live status. Your browser also
opens at Microsoft's device sign-in page, and the code is already on your clipboard.

1. Paste or type the code on that page.
2. Sign in with your work account, as your organisation normally asks (MFA included).
3. Approve **"Microsoft Office"** when asked.
4. Come back to Claude. The card turns to **Signed in** and Claude carries on with your question.

The code works for 15 minutes. If it expires, ask Claude to sign in again for a new one.

## What it can and can't do

- **Can:** search users by name, read a user's profile, account status and expiry, email aliases, groups, manager,
  direct reports and peers; read groups and expand distribution lists, nested lists included; check membership;
  search recently deleted users. Temporary access passes and conditional access policies are read only when your
  account holds a role that allows it, such as Security Reader.
- **Can't:** create, change or delete anything: users, groups, memberships, policies or authentication methods. It
  reads only what your own account may read.
- **Your data:** directory details Claude reads are sent to Anthropic as part of your conversation, under your Claude
  account's terms.

## Profiles and settings

The extension's settings (Settings > Extensions > Entra Directory Search for Claude Desktop > Configure) are all
optional:

| Setting | What it does |
|---|---|
| Default sign-in email | Allows only this account for the default profile. Blank: any work account you sign in with |
| Default Entra tenant | Your organisation's tenant id or domain. Blank: Microsoft's work-account sign-in |
| WPP sign-in email | Allows only this account for the WPP profile |
| WPP Entra tenant | Turns on the `wpp` profile, for example `wpp.com`. Blank: no WPP profile |
| Check for updates | Checks the landing page once a day and offers new versions (on by default) |

Each profile signs in separately and keeps its own sign-in in your macOS Keychain. Ask Claude to use the WPP
directory and it passes `profile: "wpp"`; otherwise it uses the default account.

## Read-only design

There are none. The extension is read-only: every directory tool only reads, and no tool can change your directory.

## Available tools

| Tool | What it does | Changes anything? |
|---|---|---|
| `search_users` | Searches users by display name, mail, UPN, employee id, or alias. A truncated page is continued with `resume` | No |
| `get_user` | Profile, account status and expiry, emails, and one manager. Not the full attribute dump | No |
| `get_user_emails` | Every address that reaches a user: sign-in name, primary, other and proxy addresses | No |
| `get_user_org` | A user's manager, direct reports and peers | No |
| `get_user_groups` | The groups a user belongs to and owns | No |
| `get_group` | A group's details, owners and members | No |
| `expand_distribution_list` | Every member of a list, nested lists expanded, with their addresses | No |
| `check_group_membership` | Whether a user is in a group, directly or through nesting | No |
| `search_deleted_users` | Recently deleted users matching a name | No |
| `get_conditional_access_policies` | Conditional access policies. A target match is not proof a policy applied. A truncated page is continued with `resume` | No |
| `search_groups` | A group by id, exact mail, or exact display name. More than one name match is ambiguous | No |
| `resolve_address` | The user, group, or contact that owns an address. A denied contact read does not drop another hit | No |
| `get_manager_chain` | Managers upward. A repeated person stops the chain | No |
| `get_account_details` | Licences, directory roles, devices, authentication methods, and sign-in activity. One denied section does not hide the others | No |
| `whoami` | The signed-in account for the selected profile | No |
| `search_profiles` | Which configured profiles already have a saved sign-in. It does not start one | No |
| `get_unowned_groups` | Groups whose owner read was empty. A failed owner read is skipped, not "no owner". A truncated scan is continued with `resume` and is not the full set | No |
| `get_group_hierarchy` | Nested group members as JSON. A denied branch is unavailable, not a missing child | No |
| `get_user_attribute` | One selected property of a user | No |
| `sign_in`, `sign_in_status`, `sign_out` | Start, follow or forget a profile's sign-in | Your sign-in only |
| `check_for_updates`, `install_update`, `snooze_update` | The update card: check, Update now, Not now | Opens Claude Desktop's install dialog |

## Requirements

| | |
|---|---|
| Operating system | macOS only (sign-ins are kept in the macOS Keychain) |
| Claude Desktop | 2.7032.0 or later |
| Account | A Microsoft 365 / Entra ID work account and a web browser |

You don't need Node, npm, git, a repository checkout or a configuration file: Claude Desktop runs the extension with
its own Node.

## Sign-in and privacy

- The extension signs in as the pre-approved **Microsoft Office** app with your own account, using Microsoft's
  device-code sign-in: you enter a short code in your normal browser, so your organisation's usual sign-in and MFA
  apply.
- After you sign in, Microsoft issues a refresh token. It is stored only in your **macOS login Keychain**, as the item
  `active-directory-search-refresh-token`, one entry per profile; never in a file or in the extension. Access tokens
  stay in memory.
- If you set a sign-in email, signing in as any other account is refused.
- Only enter a code that Claude has just shown you, when you asked Claude something. Never enter a code someone sends
  you: that would sign them in as you.
- The installer checks the download's SHA-256 before anything changes and never reads or prints your sign-in.
- Once a day the extension asks the landing page which version is the latest; nothing about you is sent. Its update
  card checks the new version's SHA-256 too and only opens Claude Desktop's install dialog: nothing is installed until
  you click Install.

## Troubleshooting

| What you see | What to do |
|---|---|
| The installer says "Checksum mismatch — nothing was installed" | A new release can take up to about 10 minutes to reach every server. Wait, then run the install command again |
| The installer says Claude Desktop is missing or too old | Install or update Claude Desktop (2.7032.0 or later), open it once, then run the install command again |
| Claude has no directory tools | Switch the extension on (Settings > Extensions), then start a new chat: chats opened while it was off never see its tools |
| Claude says sign-in is needed | Expected the first time and when Microsoft wants a new sign-in: follow the card |
| "You signed in as X, but this profile is configured for Y" | Your browser was signed in to another account. Sign out there or use a private window, then ask Claude to sign in again |
| The code expired | Ask Claude to sign in again for a new code |
| A user or group isn't found | Check the spelling, or that it is in the profile's tenant: ask for the WPP directory if it is a WPP account |
| A section says unavailable | The signed-in account is not allowed to read it. That is not an empty result |
| The tools appear twice | Remove the old `active-directory` or `active-directory-search` entry from `claude_desktop_config.json` (quit Claude Desktop first) |
| `--status` says `Server: failed` | Run the install command again. If it keeps failing, the log it names (`~/Library/Logs/Claude/mcp-server-…`) says why |

## Update and remove

The extension checks the landing page once a day while **Check for updates** is on. When there is a new version,
Claude shows an update card: **Update now** downloads it, checks its SHA-256 and opens Claude Desktop's install
dialog; **Not now** hides that version for a week. Running the install command again also updates. Your settings and
sign-ins are kept.

To remove it, say *"Sign out of Entra Directory Search"* (once per profile), uninstall it in Claude Desktop
(Settings > Extensions), then remove the download folder:

```bash
curl -fsSL https://wpp-jamie-mills.github.io/active-directory-search-site/install.sh | bash -s -- --uninstall
```

---

<sub>Generated from the project README for version 1.6.1. It is rebuilt automatically with each release.</sub>
