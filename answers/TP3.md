# TP3 answers — Hafedh Khenissi

## Exercise 1

On a waiting ticket: **Start helping** → message *Status updated.*, status `helping`, the button becomes **Mark as done** → status `done`, the button disappears (only *Remove* is left).

**Question 1. With the hidden `status` field changed to `sleeping`, which message do you get?**

The validation rule `Rule::in(Ticket::STATUSES)` refuses the value, so the status is **not changed** (the ticket stays `waiting`). The error message is:

> **The selected status is invalid.**

On the page itself, nothing is displayed: Laravel redirects back to the ticket page with the error in the session, but `show.blade.php` has no `@error('status')` block to display it (and no *Status updated.* message appears either). I read the message by sending the same request asking for JSON, which answered `422` with `{"message":"The selected status is invalid.","errors":{"status":["The selected status is invalid."]}}`.

## Exercise 2

After clicking *Start helping* on another waiting ticket (Marquise), the queue shows her under **Being helped**, the ticket finished in exercise 1 has left the list, the **Waiting (6)** section is numbered 1 to 6, and the page says **1 solved so far.**

**Question 2. Result of `Ticket::waiting()->toSql()`:**

```sql
select * from "tickets" where "status" = ? order by "id" asc
```

The scope produces a query that selects all the columns of `tickets`, keeps only the rows whose `status` equals a value, sorted by `id` from the smallest to the largest (the oldest ticket first). The `?` is a placeholder: the value `'waiting'` is sent separately to the database (a *prepared statement*), which protects against SQL injection. `toSql()` shows the query without running it.

## Exercise 3

**Question 3. How many people are ahead of you? Check it by counting the Waiting section.**

I created a ticket "Hafedh Khenissi" (Database) through the form. Its page (`/tickets/10`) says **6 people ahead of you.**
In the queue, the **Waiting (7)** section lists: 1. Anne, 2. Emelie, 3. Jasmin, 4. Jade, 5. Meagan, 6. Hafedh, **7. Hafedh Khenissi**. I am 7th, so there are indeed **6** people before me. Marquise (*Being helped*) and the ticket that is *done* are not counted, because the count uses the `waiting()` scope: `Ticket::waiting()->where('id', '<', $ticket->id)->count()`.

## Exercise 4

After `php artisan migrate:fresh --seed`, `Ticket::first()->topic->name` prints **Laravel** in tinker: the relationship works.

**Question 4. Open `/tickets`. What do the topic badges contain now? Explain why.**

The badges now contain **JSON**, the whole topic object, for example:

```
{"id":2,"name":"Laravel","created_at":"2026-11-25T09:46:05.000000Z","updated_at":"2026-11-25T09:46:05.000000Z"}
```

The `topic` column no longer exists (the migration dropped it and added `topic_id`). In the views, `$ticket->topic` now calls the new `topic()` **belongsTo relationship**, which returns a `Topic` model, not a text. When Blade prints an object with `{{ }}`, Eloquent converts the model to a string, and a model converted to a string becomes its JSON representation. To show only the name we must write `$ticket->topic->name` (exercise 5).

## Exercise 5

The badges show topic names again (*Laravel, Git, Blade, Database…*) and the form offers the 5 topics from the database, sorted by name: Blade, Database, Git, Laravel, Setup.

**Question 5. With an option's value changed to 99, which message do you get, and which rule produced it?**

> **The selected topic id is invalid.**

It comes from the rule **`'exists:topics,id'`** on `topic_id` in `store()`: Laravel runs a query to check that a row with `id = 99` exists in the `topics` table; there are only 5 topics (ids 1 to 5), so the value is refused and no ticket is created.
