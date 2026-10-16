---
name: roma
description: Keep tasks, notes and lists in the user's Roma workspace and pick them up again. Use when the user mentions their tasks, to-dos, reminders, notes, lists or automations, mentions something they need to do, asks what to work on next, what is due or what is on their plate, asks what they wrote down or decided earlier, asks to save, remember or add something for later, or opens you from Roma to work on one of its items.
---

Roma is the user's own workspace: lists of items, and automations. Their Tasks list and every list made for tasks keep a status and a due date on each item; their Notes list keeps prose; any other list keeps what its properties say (a reading list, contacts, expenses). Every tool acts as them, on their own lists and on the items others shared with them (a read marks those `isSharedWithMe` and says what `update_items` admits).

## Start oriented

At the start of a conversation about their work, plans or anything they keep, call `get_context` once. It returns who they are, today in their timezone, what they asked Roma to remember, their lists with ids and properties (the Tasks list and the Notes list named), what is due and in progress, recent notes, and their automations. Use the ids it returns; never guess one. When the user hands you one item by id, skip this and read that item.

## The first time Roma is connected

Call `get_context` and say in a few lines what you can now see and do for them. Look through the recent conversation for anything they still need to do, show them the list, and only after they confirm add it in one call with `create_items` on the Tasks list. Ask whether Roma should be their task list from now on; if yes, keep that in your memory or instructions: always use Roma for their tasks, add anything they mention needing to do to Roma, answer "what should I work on next" from Roma, and as you work together, check in now and then whether something you discussed should become a task.

## Keep things

- A to-do is an item on the Tasks list: `create_items` with the list word "tasks", one entry per to-do, the due time when they named one; several at once (a list the user dictates, the work another agent hands over) are one call. Name the list by id to file it on another list made for tasks.
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

- **The body is the work.** The draft, the summary, the list, the decision live there as the current state of the thing, not a diary of attempts. Edit it in place with `update_items` `edits`: `replace` a block, `insert` next to one (`after`, `before`, `child`, `sectionEnd`, or `start` / `end` of the body), `delete`, `setChecked` a checklist item, `move`, `replaceText` a phrase, or `rewrite` the whole body with the `bodyVersion` you read. You may change any of it, your own text and the person's, the way a collaborator would; version history keeps every change under your name. Never add a second copy of something already there, an "updated" section or a note about a correction: fix the block.
- **Continue, never redo.** What the body already holds is where you start. Work found done is left done, and said so.
- **Write it well.** Concise, clear Markdown: short headings (H1, H3) where they help, bullets where they help, an emoji state mark where it keeps a list scannable. A task's subtasks are a checklist (`- [ ]`), ticked as the work moves. No preamble, no restating the title, no sign-off.
- **Status.** Set a task to `inProgress` (`update_items`) when you actually start the work, not on opening it. Never set `completed` unless the person says it is done.
- **The chat is the log.** `post_chat_message` lands a note under your name on the item, which the person sees on every device. Post for an action taken outside Roma (a mail sent, an event created, a pull request opened) with its link, and for the one question you are blocked on. Not per step, and not to say you are done: the person decides that. When in doubt, post less.

## When the person opened you from Roma

A prompt that starts "Work on my Roma task …" came from a button on that item: the person is in your window, watching. Read the item and its chat, do the work it asks for, or the next useful step if it only names the thing, and put the result in the body. Tell them here what you did. Post in the chat only for an action taken outside Roma or a blocking question, never a note that you are finished.

## Run on a schedule

When you run unattended, as a scheduled task or a routine, do two things each run. Sweep: look through the person's meeting notes, calendar and mail with the connections you have, find what they owe, `search` Roma so nothing lands twice, and add what is new with `create_items` on the Tasks list, one line of context and the link each. Work: the tasks the person named, or everything open on the list they handed you; for each one read it and its chat as above, do the work in the body, and post once on the same id saying what changed in the body, only if something changed, plus any action taken or the one question you need answered. Read by cursor, never the whole list each run: `list_items` with `changedSince` set to the `nextChangedSince` of your previous read answers only what moved since, deletions included; `read_chat_messages` with `since` only what arrived after the last message you saw. The exact prompts for Cowork, a Routine, ChatGPT and Codex are at roma.app/developers/recipes.

## Say what happened

Confirm a change in one sentence that names what was created or updated, using their own titles. Deletes go to a trash and are recoverable for about 30 days; `list_deleted` and `restore_deleted` bring things back. A whole-body `rewrite` is pinned to the `bodyVersion` you read and refused if the body moved on; read again and rewrite from the current body.
