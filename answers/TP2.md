# TP2 answers — Hafedh Khenissi

## Exercise 1

**Question 1. Run `migrate:fresh --seed` a second time. Are the ticket names the same? Why?**

**No.** First run: `Madaline, Shany, Kara, Marco, Wayne, Maya, Mabelle, German`. Second run: `Damion, Myra, Caitlyn, Sandy, Dillan, Caitlyn, Sonny, Ansley`.
`migrate:fresh` deletes every table and recreates them, then the seeder calls the factory again. The factory uses **Faker** (`fake()->firstName()`, `fake()->randomElement(...)`, `fake()->sentence()`), which picks **random** values each time. So every run produces new invented tickets (the same name can even appear twice, like *Caitlyn*).
