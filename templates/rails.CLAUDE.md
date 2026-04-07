# CLAUDE.md — Ruby on Rails Project

## Stack

Ruby on Rails 8, PostgreSQL, Devise (auth), Sidekiq (background jobs), Redis, deployed on Render.

## Project conventions

- Follow Rails defaults unless there's a strong reason not to.
- MVC: fat models, skinny controllers. Complex logic goes in service objects.
- Use `app/services/` for business logic that spans multiple models.
- Use `app/jobs/` for anything async — email, webhooks, data processing.

## Folder structure (additions to standard Rails)

```
app/
├── controllers/      ← standard Rails controllers
├── models/           ← ActiveRecord models with validations and scopes
├── services/         ← service objects (POROs) for business logic
│   └── [feature]/    ← group by domain (e.g., billing/, onboarding/)
├── jobs/             ← Sidekiq background jobs
├── views/            ← ERB templates (or ViewComponents if adopted)
└── policies/         ← authorization (Pundit if used)
```

## Naming conventions

- Models: singular (`User`, `TeamMembership`)
- Controllers: plural (`UsersController`, `InvoicesController`)
- Service objects: verb-noun (`CreateSubscription`, `ProcessRefund`)
- Jobs: verb-noun + `Job` suffix (`SendWelcomeEmailJob`)
- Database columns: `snake_case`, timestamps via `t.timestamps`

## Service object pattern

Service objects are POROs: `CreateSubscription.new(user:, plan:).call`. One public method (`call`), verb-noun naming, lives in `app/services/`.

## Auth

Devise handles authentication. Protect controllers with `before_action :authenticate_user!`. Authorization via Pundit policies or simple `before_action` checks.

## Background jobs

Sidekiq for all async work. Keep jobs idempotent — they may retry. Never do heavy work in controllers; enqueue a job instead.

## Testing

- Framework: **RSpec** + **FactoryBot** + **Shoulda Matchers**
- Structure: `spec/models/`, `spec/requests/`, `spec/services/`, `spec/jobs/`
- Run: `bundle exec rspec` (all) / `bundle exec rspec spec/models/` (subset)
- Minimum: test all models (validations, scopes), request specs for every endpoint, service object specs

## Commands

```bash
bin/rails db:migrate        # run migrations
bin/rails server            # start dev server (port 3000)
bundle exec rspec           # run tests
bundle exec sidekiq         # start background worker
bin/rails console           # interactive console
```
