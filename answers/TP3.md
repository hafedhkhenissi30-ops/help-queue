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
