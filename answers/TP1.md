# TP1 answers — Hafedh Khenissi

## Exercise 1

**Question 1. Why are `.env` and `vendor/` excluded by `.gitignore`?**

- `.env` holds the configuration of *this* machine, including secrets: the `APP_KEY` used to encrypt sessions and cookies, and database passwords. Secrets must never be published on GitHub. Each developer creates their own `.env` from `.env.example`.
- `vendor/` contains the PHP packages downloaded by Composer (Laravel itself and its dependencies, thousands of files). It is not our code and it can be rebuilt at any time with `composer install`, using the exact versions saved in `composer.lock`. Putting it in Git would make the repository huge for nothing.

## Exercise 2

| File | Information |
|---|---|
| `routes/web.php` | `Route::get('/', function () { return view('welcome'); });` — line 6 is `return view('welcome');` |
| `.env` | `DB_CONNECTION=sqlite` |
| `database/migrations/` | 3 migrations (`create_users_table`, `create_cache_table`, `create_jobs_table`) |
| `app/Models/User.php` | `#[Fillable(['name', 'email', 'password'])]` → `name`, `email`, `password` |

## Exercise 3

**Question 2. Which error code do you get for `/helo`, and why?**

**404 Not Found.** Laravel compares the address with every route in `routes/web.php`. No route matches `/helo` (only `/hello` exists), so Laravel answers that the page does not exist.

## Exercise 4

**Question 3. Why does `/hello/a/b` not work?**

It answers **404**. The parameter `{name}` matches exactly **one segment** of the address, and a segment stops at the `/`. `/hello/a/b` has three segments (`hello`, `a`, `b`), while the route `/hello/{name}` only has two, so no route matches.
