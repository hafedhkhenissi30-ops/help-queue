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
