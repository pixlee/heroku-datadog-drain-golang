[![Build Status](https://travis-ci.org/apiaryio/heroku-datadog-drain-golang.svg?branch=master)](https://travis-ci.org/apiaryio/heroku-datadog-drain-golang)

# Heroku Datadog Drain

Golang version of [NodeJS](https://github.com/ozinc/heroku-datadog-drain)

Funnel metrics from multiple Heroku apps into DataDog using statsd.

Supported Heroku metrics:
- Heroku Router response times, status codes, etc.
- Application errors
- Heroku Dyno [runtime metrics](https://devcenter.heroku.com/articles/log-runtime-metrics)

## Get Started
```bash
# Clone the Github repo.
git clone git@github.com:apiaryio/heroku-datadog-drain-golang.git
cd heroku-datadog-drain-golang

# Setup Heroku, specify the app(s) you'll be monitoring and create a password for each.
heroku create
heroku config:set ALLOWED_APPS=<your-app-slug> <YOUR-APP-SLUG>_PASSWORD=<password>

# OPTIONAL: Setup Heroku build packs, including the Datadog DogStatsD client.
# If you already have a StatsD client running, see the STATSD_URL configuration option below.
heroku buildpacks:add heroku/go
heroku buildpacks:add --index 1 https://github.com/miketheman/heroku-buildpack-datadog.git
heroku config:set HEROKU_APP_NAME=$(heroku apps:info|grep ===|cut -d' ' -f2)
heroku config:add DATADOG_API_KEY=<your-Datadog-API-key>

# Deploy to Heroku.
git push heroku master
heroku ps:scale web=1

# Add the Heroku log drain using the app slug and password created above.
heroku drains:add https://<your-app-slug>:<password>@<this-log-drain-app-slug>.herokuapp.com/ --app <your-app-slug>
```

## Configuration
```bash
STATSD_URL=..             # Required. Set to: localhost:8125
DATADOG_API_KEY=...       # Required. Datadog API Key - https://app.datadoghq.com/account/settings#api
ALLOWED_APPS=my-app,..    # Required. Comma seperated list of app names
<APP-NAME>_PASSWORD=..    # Required. One per allowed app where <APP-NAME> corresponds to an app name from ALLOWED_APPS
<APP-NAME>_TAGS=mytag,..  # Optional. Comma seperated list of default tags for each app
<APP-NAME>_PREFIX=..      # Optional. String to be prepended to all metrics from a given app
DATADOG_DRAIN_DEBUG=..    # Optional. If DEBUG is set, a lot of stuff will be logged :)
```
Note that the capitalized `<APP-NAME>` and `<YOUR-APP-SLUG>` appearing above indicate that your application name and slug should also be in full caps. For example, to set the password for an application named `my-app`, you would need to specify `heroku config:set ALLOWED_APPS=my-app MY-APP_PASSWORD=example_password`
