# IP authentication route service

This repo contains a simple Nginx application which acts as a proxy for your web applications and provides an IP restriction layer.

## Customization

Edit the `manifest.yml` and change the `ALLOWED_IPS` as appropriate.

## Deployment

To deploy the app, run `cf push`.

If you have not overwritten the `((app-name))` variables then you will need to
run `cf push --var app-name=my-app`

### Use the app as a route service

From the command line, run:

```
cf create-user-provided-service SERVICE_INSTANCE -r ROUTE_SERVICE_URL
```

where `SERVICE_INSTANCE` is a unique, descriptive name for this route service instance, and `ROUTE_SERVICE_URL` is the url of the route service endpoint; for example:

```
cf create-user-provided-service my-route-service -r https://route-service.example.org
```

The service will be immediately ready to be used, and you can query its status by running:

```
cf service my-route-service
```

Bind this route service instance to the application route

```
cf bind-route-service DOMAIN SERVICE_INSTANCE --hostname HOSTNAME
```

where:

- `DOMAIN` is your app domain
- `SERVICE_INSTANCE` the name of the service that you have just created
- `HOSTNAME` the host or app name assigned to the app

For example, if your app is named `myapp`:

```
cf bind-route-service app.cloud.gov my-route-service --hostname myapp
```

You can list the routes of the current space to see the applications and route services bound to them:

```
cf routes
```

## Checking that it works

The route service exposes two paths for checking the status.

The path `/_route-service-health` is for information and health checking, and
has stats about the number of active connections which exist.

The path `/_route-service-check` is for checking if you may use the route
service. If you are, then you will receive `OK`, if you are not you will
received `Forbidden by ((app-name))`, where `((app-name))` is the value of the
`APP_NAME` environment variable.
