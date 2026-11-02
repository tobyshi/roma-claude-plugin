---
name: roma
description: Keep the user's tasks, notes and lists in their Roma workspace, and pick them up again. Use when the user mentions their tasks, to-dos, reminders, notes, lists, automations or experts, mentions something they need to do, asks what to work on next, what is due or what is on their plate, asks what they wrote down or decided earlier, asks to save, remember or add something for later, asks you to work the way one of their experts does, opens you from Roma to work on one of its items, or works through a plan, a decision, a comparison or a piece of research with you: that work lives in a Roma task. Not for a quick factual question or a one-off rewrite.
---

Roma is the user's own workspace: lists of items, and automations. Their Tasks list and every list made for tasks keep a status and a due date on each item; their Notes list keeps prose; any other list keeps what its properties say (a reading list, contacts, expenses). Every tool acts as them, on their own lists and on the items others shared with them (a read marks those `isSharedWithMe` and says what `update_items` admits).

## Start oriented

At the start of a conversation about their work, plans or anything they keep, call `get_context` once. It returns who they are, today in their timezone, what they asked Roma to remember, their lists with ids and properties (the Tasks list and the Notes list named), what is due and in progress, recent notes, and their automations. Use the ids it returns; never guess one. When the user hands you one item by id, skip this and read that item.

## The first time Roma is connected

Call `get_context` and say in a few lines what you can now see and do for them. Then offer to fill Roma from what you already know: look through the conversations you can see for anything they still need to do and anything you were in the middle of working on together, show the titles, and after one yes add each as a task on Tasks, `search` before each; a piece of work in progress gets its body as the task's record, in the shape under What goes in Roma. Then say in three short lines how this works from here: they mention a to-do in any chat and you put it on Tasks; when you work on something together, that work lives in a task you keep current and you suggest the next tasks as they come up; they ask "what's on my plate" or "where did we land on X" and you answer from Roma; their lists are at roma.app and in the Roma app on their phone. Ask once whether to keep working this way. If yes, save it to your memory in this same turn and say in one sentence that you saved it, then give them this one line to keep in their custom instructions or preferences (in ChatGPT: Settings, Personalization, Custom instructions), because a memory alone does not make you reach for Roma in a chat about something else, and the line does: "Use Roma, my task list, following the roma skill: add anything I mention needing to do to my Tasks, keep the task we are working on current as we go and suggest the next ones, and answer what I should work on next from Roma." Ask once, never again.

## Keep things

- A to-do is an item on the Tasks list: `create_items` with the list word "tasks", one entry per to-do; several at once (a list the user dictates, the work another agent hands over) are one call. Name the list by id to file it on another list made for tasks. A due date is a day, `dueAt` as `YYYY-MM-DD`, unless they said a time, then `YYYY-MM-DDTHH:MM` in their local time, which also sets a reminder at that moment; "remind me about it tomorrow" on a day is `reminderAt` beside the day, and the reminder rings at their default hour.
- A plan, a reference, meeting notes or a draft is an item on the Notes list: `create_items` with the list word "notes" and the body as Markdown.
- A set of similar things (books, contacts, expenses, wines) is items on the list they keep for it: `list_lists` for the list and its property ids, then `create_items` with values for those properties, many entries in one call. A list they do not have yet is `create_list`.
- Before creating, `search` once. What they refer to usually already exists, and a second copy drifts from the first. Add to the existing item with `update_items` edits.

## Pick things up

- "What's on my plate" and "what should I work on next" are `get_context`; a longer or filtered list is `list_items` (the Tasks set by default, or one list by id). Answer from Roma, not from the conversation alone.
- "What did I write down about…" is `search`, then `get_item` for the full text.
- A count, a total or a filtered slice of one list ("how many are unread", "expenses in March") is `query_items`.
- "What did my automation find" is `list_automations`, then `get_automation_runs`. Start one only when they ask, with `run_automation`; its result lands in their Roma chat.

## Work on one thing

Read it first: `get_item` returns the item with its body, its values by property name, its list, and, for a meeting recorded into it, the transcript. The body comes back with a short handle in front of every block, a `bodyVersion`, and a line of guidance about this item's state. Then `read_chat_messages`: what the person wrote there since an agent last posted is usually the brief, or the answer you were waiting for.

- **The body is the work, written for someone who was not there.** A task's body is its documentation (the shape is under What goes in Roma below); a note whose body is the thing itself, a draft, a plan, a reference, keeps its body as that thing, with nothing added above or below it. The conversation itself is never pasted into either. Edit it in place with `update_items` `edits`: `replace` a block, `insert` next to one (`after`, `before`, `child`, `sectionEnd`, or `start` / `end` of the body), `delete`, `setChecked` a checklist item, `move`, `replaceText` a phrase, or `rewrite` the whole body with the `bodyVersion` you read. You may change any of it, your own text and the person's, the way a collaborator would; version history keeps every change under your name. Never add a second copy of something already there, an "updated" section or a note about a correction: fix the block.
- **Continue, never redo.** What the body already holds is where you start. Work found done is left done, and said so.
- **Write it well.** Concise, clear Markdown: short headings (H1, H3) where they help, bullets where they help, an emoji state mark where it keeps a list scannable. A task's subtasks are a checklist (`- [ ]`), ticked as the work moves. No preamble, no restating the title, no sign-off.
- **Status.** Set a task to `inProgress` (`update_items`) when you actually start the work, not on opening it. Never set `completed` unless the person says it is done, or has told you to mark your own work done when you finish.
- **The chat is the log.** `post_chat_message` lands a note under your name on the item, which the person sees on every device. Post for an action taken outside Roma (a mail sent, an event created, a pull request opened) with its link, and for the one question you are blocked on. Not per step, and not to say you are done: the person decides that. A task whose body asks you for a post is the person's own ask: post what it asks, there. When in doubt, post less.

## What goes in Roma

Roma is the person's task list, and three things land there without being asked. A to-do they mention is a task on Tasks. Work you do together on a topic (a plan, a decision, a comparison, a piece of research, a draft they are working toward) lives in that topic's task: make it once when the work starts, keep its body current as you go, set its status as the work moves, and make the next to-dos the work produces tasks of their own that name it. And when they ask what to work on next, you answer from Roma. A note is made only when the person asks for one (a draft, a reference, a plan they want as a document), on Notes. A quick question or everyday chat leaves nothing. If the person hands you an id or a link, that is the item. `search` before making anything, and before every item of a bulk run: a second copy drifts from the first. Write as soon as the work starts, then whenever something changed: a decision, a result, a next step, a new fact. Never wait for the end of the conversation; nobody knows when it ends. Tell the person in one line which item you created or updated; do not ask first. If your host refuses a write, retry it once right away as its own call with a shorter body and without links or quoted text from pages you fetched, then add the sources in a second write; only if the retry is refused too, say so in one line. Never drop a write silently. If Roma is not connected, say so at the start rather than going on without it.

**A task's body is its record.** The top fits one screen and is for a person; the bottom is for the agent who picks the task up. Rewrite the top so it stays true; only add to the log. A section exists only when it has content, and a next step is never invented.

- Where it stands: 2 to 4 sentences, what this is, why it matters, where it stands.
- Decisions: one line each, 🧭 the decision, because the reason, the date. When a decision changes, keep the old one marked ❌ with the date it was replaced, and write the new one below it.
- Next steps: a checklist of real actions, at most about five, with a name on any that waits on someone else.
- Open questions: ❓ one line each.
- Facts: what only this conversation knew and someone would need to resume: numbers, names, dates, models, prices, links, ids. Not what any agent already knows.
- Log: dated one-liners at each milestone, newest last, never edited or deleted.
- The item's one-line Summary field, where its list has one, says what the item is and where it stands; keep it current with the body. If the list has no Summary field, the first line of the body is that line.

**A note whose body is the thing itself** (a blog draft, a plan, a reference) keeps its body as that thing: no summary above it, no log below it. Its Summary field says where it stands; if there is work to track, a task beside it carries the shape above.

**Write for someone who was not there.** Short sentences of about 15 words, one idea each; the main point first; active voice that names who; plain everyday words around the precise technical terms, each term explained once. Put the meaning in the first few words of every heading and bullet. Absolute dates (19 Oct 2026), never "yesterday". Level-2 headings only, bold for at most one phrase, prose for reasons and bullets for three or more parallel things, checklists only for actions. Emojis only as state marks at the start of a line: ✅ done, 🟡 in progress, ❌ dropped or replaced, ❓ open, 🧭 decision, ⚠️ risk. No em dashes; no "not just X, but Y"; no filler such as "crucial", "robust", "seamless", "it's worth noting"; nothing restated in other words. Keep out the conversation, your own reasoning, dead ends (unless one teaches something, then one ❌ line with the lesson), anything already in the item's fields, and every secret.

**Before saving, three checks.** Could someone who was not there understand the top in under a minute? Could an agent resume from the body alone, without the chat? Is any sentence over 25 words, or any em dash left?

## When the person opened you from Roma

A prompt that starts "Work on my Roma task …" came from a button on that item: the person is in your window, watching. Read the item and its chat, do the work it asks for, or the next useful step if it only names the thing, and put the result in the body. Tell them here what you did. Post in the chat only for an action taken outside Roma or a blocking question, never a note that you are finished.

## Run on a schedule

When you run unattended, as a scheduled task or a routine, do two things each run. Sweep: look through the person's meeting notes, calendar and mail with the connections you have, find what they owe, `search` Roma so nothing lands twice, and add what is new with `create_items` on the Tasks list, one line of context and the link each. Work: the tasks the person named, or everything open on the list they handed you; for each one read it and its chat as above, do the work in the body, and post once on the same id saying what changed in the body, only if something changed, plus any action taken or the one question you need answered. Read by cursor, never the whole list each run: `list_items` with `changedSince` set to the `nextChangedSince` of your previous read answers only what moved since, deletions included; `read_chat_messages` with `since` only what arrived after the last message you saw. The exact prompts for Cowork, a Routine, ChatGPT and Codex are at roma.app/developers/recipes.

## Work under one of their experts

An expert is a named body of standing instructions the user wrote in Roma for a kind of work: an editor, a reviewer, a coach. When they name one ("use my Content Editor on this", "ask my Devil's Advocate"), read it once with `get_expert` and do the work under those instructions here, in this conversation; `list_experts` names them all, and `get_context` already carries the names. A task or note made for that work can carry the expert (`expertId` on `create_items`, or later on `update_items`), so its chat in Roma answers as that expert too. Create one only when the user asks for a new expert (`create_expert`); correct one when they say it missed something (`update_expert`, the whole instructions, read first). `list_versions` and `restore_version` undo an edit to a body or to an expert's instructions.

## Say what happened

Confirm a change in one sentence that names what was created or updated, using their own titles. Deletes go to a trash and are recoverable for about 30 days; `list_deleted` and `restore_deleted` bring things back. A whole-body `rewrite` is pinned to the `bodyVersion` you read and refused if the body moved on; read again and rewrite from the current body.
