
# active_scheduler

active_scheduler is a gem to take a standard schedule one would use with [resque scheduler](https://github.com/resque/resque-scheduler) and wraps it to work with [ActiveJob](https://github.com/rails/rails/tree/master/activejob).

Currently only Resque is supported, but pull requests to add other queues (sidekiq, etc) would be welcomed!

## Requirements/Support

- Ruby 2.0+
- ActiveJob
  - 4.2 is tested and used in production
  - 5.0 is tested against CI, but I haven't tried it in production.
- Resque
- Resque Scheduler

## Setup

#### Installation

Add `active_scheduler` to your Gemfile.

## Usage

In your Resque initializer:

```ruby
require 'resque/server'
require 'resque/scheduler/server'
require 'active_scheduler'

# ... Set up your Resque ...
...

yaml_schedule    = YAML.load_file("#{Rails.root}/config/resque_schedule.yaml") || {}
wrapped_schedule = ActiveScheduler::ResqueWrapper.wrap yaml_schedule
Resque.schedule  = wrapped_schedule
```


## Example Format

```yaml
simple_job:
  every: "30s"
  queue: "simple"
  class: "SimpleJob"
  args:
    -
  description: "It's a simple job."

ThisIsTheClass:
  cron: "* * * *"
  queue: 'cronny'
  description: "Will call the ThisIsTheClass class"
```

Only classes that are descended from `ActiveJob::Base` will be wrapped

### Job Classes With Named Args

If you have a job class that uses named arguments you can specify that. `args`
should be a hash nested in the array and you need to add the named_args key.

```yaml
simple_job:
  every: "30s"
  queue: "simple"
  class: "SimpleJob"
  args:
    - foo: 1
      bar: 2
  description: "It's a simple job."
  named_args: true
```
