# Help Queue

A queue students use to ask for help during practical sessions, built with Laravel over TP1 to TP3 (Web Frameworks).
A student joins the queue with a name, a topic and a description; the teacher moves each ticket from *waiting* to *helping* to *done*.

Author: Hafedh Khenissi

## Requirements

- PHP 8.3 or newer, Composer and Node.js (for example with [Laravel Herd](https://herd.laravel.com))
- Git

## Installation

```bash
git clone https://github.com/hafedhkhenissi30-ops/help-queue.git help-queue
cd help-queue
composer install
npm install
cp .env.example .env
php artisan key:generate
php artisan migrate --seed
composer run dev
```

- On Windows PowerShell, `cp` also works; in the old `cmd` window use `copy .env.example .env`.
- The database file (`database/database.sqlite`) is not in Git: the `migrate` command offers to create it, answer **yes**.
- `composer run dev` starts the server; open the address it prints (for example http://localhost:8000).

## What you can do

| Address | Page |
|---|---|
| `/tickets` | the queue: *Being helped*, *Waiting* (numbered) and the number solved |
| `/tickets/create` | the form to join the queue |
| `/tickets/{id}` | a ticket: position in the queue, *Start helping* / *Mark as done*, *Remove* |
| `/topics/{id}` | the tickets of one topic |
| `/queue.json` | the waiting tickets as JSON |

The seeder creates 5 topics (Git, Laravel, Blade, Database, Setup) and 8 tickets. To start again from scratch: `php artisan migrate:fresh --seed`.
