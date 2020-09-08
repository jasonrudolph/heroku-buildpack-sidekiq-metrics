# heroku-buildpack-sidekiq-metrics

This Heroku buildpack provides [sidekiq](https://github.com/mperham/sidekiq) metrics.

See also https://github.com/mperham/sidekiq/wiki/Monitoring

## How does it affect my slug?

This buildpack does two things:

1. Copies [.profile.d/ruby-sidekiq-metrics.sh](https://github.com/feedforce/heroku-buildpack-sidekiq-metrics/blob/master/.profile.d/ruby-sidekiq-metrics.sh) script into your slug
    * See also [Dynos and the Dyno Manager > Startup > The .profile file](https://devcenter.heroku.com/articles/dynos#the-profile-file)
1. Copies [sidekiq-metrics](https://github.com/feedforce/heroku-buildpack-sidekiq-metrics/blob/master/sidekiq-metrics) script into your slug

The `.profile.d/ruby-sidekiq-metrics.sh` script starts the `sidekiq-metrics` script on `web.1` Dyno boot.

The `sidekiq-metrics` script is called every `60` seconds and outputs the following log to stdout that is compatible with [Librato Custom log-based metrics](https://devcenter.heroku.com/articles/librato#custom-log-based-metrics):

```
source=default sample#sidekiq.queue.size=42 sample#sidekiq.queue.latency=1
source=mailer sample#sidekiq.queue.size=3 sample#sidekiq.queue.latency=0
```

## Requirements

* [bundler](https://github.com/rubygems/bundler)
* [heroku/ruby](https://github.com/heroku/heroku-buildpack-ruby) buildpack
* [sidekiq](https://github.com/mperham/sidekiq)

## Customize

* `SIDEKIQ_METRICS_DYNO` (Default: `web.1`)
    * The Dyno name which the `sidekiq-metrics` script boot as daemon
* `SIDEKIQ_METRICS_INTERVAL` (Default: `60`)
    * Polling interval (seconds) which the `sidekiq-metrics` script is called
* `SIDEKIQ_METRICS_TYPE` (Default: `sample`)
    * You can set `sample` or `measure`. Although you can set `count`, it would not make sense for sidekiq metrics
    * See also [Librato Custom log-based metrics](https://devcenter.heroku.com/articles/librato#custom-log-based-metrics)

## Configure from CLI

```
$ heroku buildpacks:add https://github.com/jasonrudolph/heroku-buildpack-sidekiq-metrics.git
```

## Configure from app manifest

```json
{
  "buildpacks": [
    {
      "url": "https://github.com/jasonrudolph/heroku-buildpack-sidekiq-metrics.git"
    }
  ]
}
```
