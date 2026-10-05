---
name: roma
description: Keep tasks, notes, projects and lists in the user's Roma workspace and pick them up again. Use when the user mentions their tasks, to-dos, reminders, notes, lists, projects or automations, mentions something they need to do, asks what to work on next, what is due or what is on their plate, asks what they wrote down or decided earlier, or asks to save, remember or add something for later.
---

Roma is the user's own workspace: their tasks, notes, projects, lists (collections) and automations. Everything in it is theirs alone, and every tool acts as them.

## Start oriented

At the start of a conversation about their work, plans or anything they keep, call `get_context` once. It returns who they are, today in their timezone, what they asked Roma to remember, their projects with ids, what is due and in progress, recent notes, their lists with column ids, and their automations. Use the ids it returns; never guess one.

## The first time Roma is connected

Call `get_context` and say in a few lines what you can now see and do for them. Look through the recent conversation for anything they still need to do, show them the list, and only after they confirm add it in one call with `create_tasks`. Ask whether Roma should be their task list from now on; if yes, keep that in your memory or instructions: always use Roma for their tasks, add anything they mention needing to do to Roma, answer "what should I work on next" from Roma, and as you work together, check in now and then whether something you discussed should become a task.

## Keep things

- A to-do is `create_task`; several at once (a list the user dictates, the work another agent hands over) are `create_tasks`. Put a due time on it when they named one and the project id when they named a project; otherwise leave it unsorted.
- A plan, a reference, meeting notes or a draft is `create_note`, with the body as Markdown.
- A set of similar things (books, contacts, expenses, wines) is rows in a list: `list_collections` for the list and its column ids, then `add_collection_items`, many rows in one call.
- Before creating, `search` once. What they refer to usually already exists, and a second copy drifts from the first. Add to the existing item with `update_task` or `update_note` (append is the default).

## Pick things up

- "What's on my plate" and "what should I work on next" are `get_context`; a longer or filtered list is `list_tasks`. Answer from Roma, not from the conversation alone.
- "What did I write down about…" is `search`, then `get_note` for the full text.
- To work on one task, `get_task_context` for its body, subtasks and project; set it to in progress with `update_task` while you work, append what you found, and complete it when it is done.
- "What did my automation find" is `list_automations`, then `get_automation_runs`. Start one only when they ask, with `run_automation`; its result lands in their Roma chat.

## Say what happened

Confirm a change in one sentence that names what was created or updated, using their own titles. Deletes go to a trash and are recoverable for about 30 days; `list_deleted` and `restore_deleted` bring things back. A whole-body replacement needs `confirmReplace`; prefer appending.
