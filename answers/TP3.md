# TP3 answers — Hafedh Khenissi

## Exercise 1

On a waiting ticket: **Start helping** → message *Status updated.*, status `helping`, the button becomes **Mark as done** → status `done`, the button disappears (only *Remove* is left).

**Question 1. With the hidden `status` field changed to `sleeping`, which message do you get?**

The validation rule `Rule::in(Ticket::STATUSES)` refuses the value, so the status is **not changed** (the ticket stays `waiting`). The error message is:

> **The selected status is invalid.**

On the page itself, nothing is displayed: Laravel redirects back to the ticket page with the error in the session, but `show.blade.php` has no `@error('status')` block to display it (and no *Status updated.* message appears either). I read the message by sending the same request asking for JSON, which answered `422` with `{"message":"The selected status is invalid.","errors":{"status":["The selected status is invalid."]}}`.
