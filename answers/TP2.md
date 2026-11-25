# TP2 answers — Hafedh Khenissi

## Exercise 1

**Question 1. Run `migrate:fresh --seed` a second time. Are the ticket names the same? Why?**

**No.** First run: `Madaline, Shany, Kara, Marco, Wayne, Maya, Mabelle, German`. Second run: `Damion, Myra, Caitlyn, Sandy, Dillan, Caitlyn, Sonny, Ansley`.
`migrate:fresh` deletes every table and recreates them, then the seeder calls the factory again. The factory uses **Faker** (`fake()->firstName()`, `fake()->randomElement(...)`, `fake()->sentence()`), which picks **random** values each time. So every run produces new invented tickets (the same name can even appear twice, like *Caitlyn*).

## Exercise 2

**Question 2. Which response do you get for `/tickets/999`? Which part of the code looked for the ticket?**

A **404 Not Found** page. Nobody wrote a query for it: the type `Ticket $ticket` in the signature of `show(Ticket $ticket)` (with the parameter `{ticket}` of the same name in the route) asks Laravel to do **route model binding**. Before calling `show()`, Laravel runs a query equivalent to `Ticket::findOrFail(999)` itself; no ticket has this id, so it answers 404 and `show()` is never called.

## Exercise 3

**Question 3. Which response do you get for `/tickets/create`? Explain.**

A **404 Not Found**. Laravel checks the routes from top to bottom and stops at the **first** one that matches. The route `/tickets/{ticket}` was above the `create` route, and `{ticket}` accepts any word, so `/tickets/create` matched it with `ticket = "create"`. Laravel then tried to find the ticket whose id is `create` (route model binding), found none, and answered 404. The `create` route was never reached.

After moving the `create` route **above** `{ticket}`, the address matches the right route. The page then shows the expected error `Route [tickets.store] not defined.`, because the form uses `route('tickets.store')`, which is only created in exercise 4.

## Exercise 4

The form works: after submitting it I am back on `/tickets`, the green message **"You are in the queue!"** appears once (it disappears when I reload) and my ticket is the last of the list (number 9).

**Question 4. Which error code did you get without `@csrf`? Which attack does this token protect against?**

The expected error is **419 Page Expired** (Laravel's `TokenMismatchException: CSRF token mismatch.`).

What I observed with Laravel 13: in Chrome the form *still worked* without `@csrf` (302 redirect to the queue). Laravel 13's middleware `PreventRequestForgery` first checks the `Sec-Fetch-Site` header that modern browsers send: if it says `same-origin` (the form comes from our own site), the request is accepted even without a token. Only otherwise does it compare the token. I tested the three cases with POST requests that have no token:

| Request without token | Result |
|---|---|
| no `Sec-Fetch-Site` header (old browser, `curl`, script) | **419** |
| `Sec-Fetch-Site: cross-site` (form placed on another website) | **419** |
| `Sec-Fetch-Site: same-origin` (our own page, modern browser) | 302, accepted |

So the protection is still there: a request coming **from another site** is refused with 419.

The token protects against **CSRF (Cross-Site Request Forgery)**: a malicious website makes the browser of a logged-in user send a request to our application (create, delete, change something) without the user knowing, using the user's cookies. That site cannot know the secret token stored in the user's session, and its request is marked `cross-site`, so Laravel refuses it.

## Exercise 5

| Try | Messages shown |
|---|---|
| 1. Form completely empty | `The name field is required.` and `The topic field is required.` |
| 2. Name filled in, no topic | `The topic field is required.` |
| 3. An `<option>` changed to `Cooking` with the developer tools | `The selected topic is invalid.` |

(No message for the description: it is `nullable`.)

**Question 5. In the second try, what happens to the name you had typed? Which part of the view is responsible?**

The name **stays in the field** ("Hafedh" is still there after the refused submission). When validation fails, Laravel redirects back to the form and keeps the submitted values in the session for one request. In the view, **`value="{{ old('name') }}"`** puts that previous value back into the input (and `@selected(old('topic') === $topic)` does the same for the select).

**Question 6. Why must validation always run on the server?**

Because everything on the client side can be changed by the user: the HTML of the form (developer tools), or the whole request (curl, Postman, a script) without using the form at all. The list of options in the `<select>` is only a suggestion; the browser does not enforce it. Only the server is under our control, so it is the only place where the rules can be trusted. Here the rule `Rule::in(Ticket::TOPICS)` refused `Cooking`, so no invalid data reaches the database. Client-side checks are only a comfort for the user.

## Exercise 6

Deleting ticket 2: the browser asks **"Remove this ticket?"**, then I am back on the queue with the message **"Ticket removed."**, and `/tickets/2` now answers 404.

**Question 7. Which hidden fields does the delete form contain, and what is each one for?**

```html
<input type="hidden" name="_token" value="4ViuUnG69lPmgrPxUn1hllq2CXM0PWbu8OXSWp1R" autocomplete="off">
<input type="hidden" name="_method" value="DELETE">
```

- **`_token`**, produced by `@csrf`: the secret CSRF token of my session. Laravel compares it with the one stored in the session to check the request really comes from our own page (protection against CSRF).
- **`_method`** with the value `DELETE`, produced by `@method('DELETE')`: HTML forms can only send GET or POST. The form is sent as POST, and Laravel reads `_method` to treat it as a **DELETE** request, so it matches the route `Route::delete('/tickets/{ticket}', ...)` (method spoofing).
