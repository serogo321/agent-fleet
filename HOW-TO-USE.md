# How to use the fleet

You installed the agents. Now what? This page is the plain-words version.

## The short version

1. **Open Claude Code in your project** and talk to it the way you always do.
2. **Describe one milestone:** what you want built, and how you'll know it's done.
3. **The `project-manager` replies with a milestone card.** That's its summary of what it understood. Fix any line
   that's wrong, then reply **"go"**.
4. **The fleet builds it.** The project-manager picks the right specialists, checks their work, runs the reviewers, and
   reports back with a done card.

That's it. There is no special command and no magic word.

## Where it works

| Where you run Claude Code | Does the fleet work? |
|---|---|
| Terminal (the `claude` command) | Yes |
| VS Code or JetBrains extension | Yes |
| Desktop app, local sessions | Yes |
| Claude Code on the web (claude.ai/code), or a cloud session in the desktop app | **Only if the agents are inside the project.** Cloud sessions run on a fresh machine that can't see your computer's `~/.claude/agents/` folder. To use the fleet there, install it "for this project only" (into `.claude/agents/`) and commit those files to the repo. |

**To check it's installed,** type `/agents`, or ask "which agents do you have?". You should see `project-manager`,
`executor`, `code-reviewer` and the rest. If they're missing, restart Claude Code.

## Do I have to type anything special?

No. There are three ways to get the fleet working, from relaxed to strict:

1. **Just ask.** Claude reads each agent's description and hands work to the one that fits. The `project-manager`'s
   description tells Claude to use it for any multi-step job, so a normal request usually gets there on its own.
2. **Name it.** "Have the project-manager build this." Naming an agent makes it very likely Claude uses it.
3. **@-mention it.** Type `@` and pick `project-manager (agent)` from the list, or type `@agent-project-manager`. This
   **guarantees** that agent runs.

**Optional: make it automatic.** Add this one line to your project's `CLAUDE.md` file:

> Hand any multi-step work to the project-manager agent; it dispatches the rest of the fleet.

After that, a short request like "here's the milestone and the test, have the fleet build it" works every time.

**You only ever talk to the project-manager.** It decides which specialists to use, so you never need to call the
architect, the executor or the reviewers yourself. You can if you want one specific job, like
"have the code-reviewer look at my changes", but you don't have to.

## Think in milestones

The fleet works best when you hand it **one milestone at a time.**

**A milestone is one thing someone could watch working**, small enough to finish in one sitting, with a test you can
say in plain words.

| Good milestone | Not a milestone |
|---|---|
| "Users can reset their password by email. The link expires after one hour." | "Build the app." |
| "The orders page has a Download CSV button that exports the rows on screen." | "Improve the code." |
| "Signing up with an email that has a plus sign (a+b@example.com) works." | "Fix all the bugs." |

**A good request has four parts.** You don't need all four, but each one you add saves a round of questions.

1. **What:** the capability, in one or two sentences.
2. **Done when:** a test in your own words. "It's done when…" You don't write code for it; the fleet turns your
   sentence into a real test and makes it fail **before** building, so it can't pass by accident.
3. **Not in this one:** what to leave out. This stops the fleet doing more than you asked.
4. **How to work:** narrow or scaled, and whether you'll be around (both explained below).

## What the milestone card looks like

Before building anything, the project-manager sends you something like this:

```
MILESTONE CARD — reply "go" to start, or correct any line
Your words:      "Add a Download CSV button to the orders page..."
Done means:      Clicking the button downloads a CSV with the same rows and columns as the table.
How I'll check:  A test clicks the button and compares the file with the table.
Not in this one: Changing the table itself; Excel format; scheduled exports.
Parameters:      none
Mode:            narrow   Attended: yes
Question:        (only if your request could mean two different things)
```

**Read it before you say "go".** Fixing one wrong line here is the cheapest fix there is. Once you say "go", the fleet
works until it's done, blocked, or needs you.

## Narrow or scaled?

The project-manager asks you this once, then remembers it in a file called `BUILD_STATE.md`.

- **Narrow: start here.** It builds exactly what you asked and nothing more, and the reviewers check the finished
  change once at the end. This is right for most work, and for any project that already exists.
- **Scaled.** It adds design work up front: the architect designs, and the critic attacks the design before anyone
  builds. Reviews go wider. Use it for a brand-new project, or for anything hard to undo: the data model, logins,
  payments, or secrets.

Whichever you pick, **anything touching money or secrets always gets a security review.**

## Will you be around? (attended or unattended)

- **Attended (the default):** when there's a real choice to make, it stops and asks you.
- **Unattended:** say "I'm away, don't wait for me." It then takes the safe, undoable choice and writes it down so you
  can overrule it later. Anything risky, such as money, secrets or deleting data, it **parks** and leaves you a question
  instead of doing it. Your questions are waiting at the top of its next report.

## Examples

### Simple

**1. Fix a bug**

> The signup page crashes when the email has a plus sign, like a+b@example.com. Fix it. Done when signing up with
> a+b@example.com works and a test proves it. Narrow mode.

**2. Add a small feature**

> Add a "Download CSV" button to the orders page that exports the rows currently on screen. Done when clicking it
> downloads a CSV with the same rows and columns as the table. Don't change the table itself. Narrow mode.

**3. Just a review**

> Have the code-reviewer and the security-reviewer look at the changes on this branch before I merge.

*(This is one bounded job, so it goes straight to the two reviewers. No milestone card.)*

**4. The one-liner, once your milestone is written down**

> Here's the milestone and the acceptance test. Have the fleet build it. Use narrow mode.
>
> *(then paste the milestone and its test)*

### Complex

**5. A milestone that touches money (scaled, design first)**

> Milestone: paid plans. Users can upgrade to Pro through our payment provider's checkout page.
> Done when:
> 1. a test-mode payment turns the account into Pro within a minute;
> 2. sending the same payment notification twice doesn't upgrade or charge twice;
> 3. a cancelled plan drops back to Free at the end of the billing period, not before.
>
> Not in this one: coupons, yearly plans, an invoices page.
> This touches money, so use scaled mode, and show me the design before anything is built. I'll be here.

**6. A batch of jobs while you sleep**

> Work through these roadmap items tonight, in this order: ITEM-12, ITEM-15, ITEM-18. I'm away until morning, so this
> is unattended. Take safe defaults and write them down. Anything about money, secrets or deleting data: park it and
> leave me a question. Narrow mode. Give me one summary card when you're done.

**7. A brand-new project**

> New project: a habit-tracker web app. People sign up, create habits, and tick them off each day; the app shows a
> streak for each habit.
> First milestone: sign-up and login only. Done when someone can create an account, log out, and log back in, and a
> wrong password is refused.
> Scaled mode. Have the architect design the data model and the login first, have the critic attack that design, and
> show me the result before any code is written.

## Tips

- **One milestone at a time.** If it sounds like more than a day of work, split it.
- **Say what "done" looks like in your own words.** The fleet turns that sentence into the test.
- **Say what's out of scope.** It's the easiest way to keep the work small.
- **Small one-off edits don't need the fleet.** "Rename this variable" or "explain this function": just ask normally.
- **The fleet costs more than a single chat.** It runs several strong models, and the reviewers re-check the work.
  Narrow mode keeps that down.
- **Check the done card's evidence.** The fleet's own rule is that a report is a claim until it's checked, and the
  same goes for you.
- **You can interrupt any time.** Press Esc and say what to change.
- **Pick up where it left off.** If a session ends mid-job, open a new one and say "pick up where the fleet left off".
  The project-manager reads the notes it left in your project and carries on.
- **A model your account can't use?** Change that agent's `model:` line. See [INSTALL.md](INSTALL.md).

## Words you'll see

| Word | What it means |
|---|---|
| **Milestone card** | The project-manager's summary of your request, sent before any work starts. Reply "go" or correct it. |
| **Drive** | One run of work: a milestone, or a batch of items. |
| **Done test** | Your "done when…" sentence, turned into a real test. It must fail before the work and pass after. |
| **Gate** | The review at the end. Independent reviewers check the whole change before it's called done. |
| **Lens / reviewer** | An agent that only checks work and never writes it, such as `code-reviewer`, `security-reviewer` or `critic`. |
| **Narrow / scaled** | How much process the fleet uses. Narrow is lean; scaled adds design and wider reviews. |
| **Parked** | A job set aside because it needs your decision. It shows up as a question in the next report. |
| **PM-RULED** | A choice the project-manager made for you while you were away, written down so you can overrule it. |
| **Roadmap inbox** | A folder where agents drop new findings. Only the `product-manager` turns them into roadmap items. |
| **Done card** | The final report: what was built, how it was checked, and anything left for you. |
