# standalone-kayenta

## Purpose

## Docker Compose

If you'd like to deploy Kayenta and kick the tires, this repository contains a fully self-contained deployment of Kayenta for demo and testing purposes. 

### Prerequisites

* [Docker](https://docs.docker.com/get-docker/)
* [Docker Compose](https://docs.docker.com/compose/install/)

The Docker Compose deployment will run Kayenta along with it's dependencies (Redis and Minio). 

### Installation

To install Kayenta using Docker Compose, move into the `docker/standalone` directory and bring the stack.

```
$ cd docker/standalone
$ docker-compose up -d
```

To ensure the stack is running, simply check using `docker ps`. Your output should look something like this.

```
CONTAINER ID   IMAGE                                                                        COMMAND                  CREATED          STATUS                    PORTS                               NAMES
74f26b3da839   minio/minio:RELEASE.2021-03-04T00-53-13Z                                     "/usr/bin/docker-ent…"   12 minutes ago   Up 12 minutes (healthy)   0.0.0.0:9000->9000/tcp              standalone-kayenta_minio_1
8d5d5836ac3d   us-docker.pkg.dev/spinnaker-community/docker/kayenta:0.19.0-20201207200017   "/opt/kayenta/bin/ka…"   16 minutes ago   Up 16 minutes             0.0.0.0:8090->8090/tcp              standalone-kayenta_kayenta_1
7336a01db5b0   redis:6.2.1                                                                  "docker-entrypoint.s…"   27 minutes ago   Up 26 minutes             0.0.0.0:6379->6379/tcp              standalone-kayenta_redis_1
```


### Custom configuration

Kayenta integrates with various monitoring and telemetry platforms like New Relic and Datadog. In order to do this, you must provide some additional configuration.

First, create a configuration file called `config/kayenta-user.yml`. For example, if you'd like to enable the New Relic integration, the contents of this file will look like this:

_Note: A New Relic Ingest/Query API key is required for this example._

```
# kayenta-user.yml
kayenta:
  newrelic:
    enabled: true
    accounts:
    - name: newrelic
      apiKey: {your-api-key-here}
      applicationKey: 2607034
      defaultScopeKey: deploymentName # Optional, if omitted every request must supply the _scope_key param in extended scope params
      defaultLocationKey: namespaceName # Optional, if omitted requests must supply the _location_key if it is needed.
      supportedTypes:
        - METRICS_STORE
      endpoint.baseUrl: https://insights-api.newrelic.com
```

Once you've written the configuration file, simply run `docker-compose restart kayenta` to restart Kayenta. If your configuration is correct, you should be able to query the API to get a list of your configured accounts.

```
$ curl localhost:8090/credentials
[
  {
    "name": "minio",
    "supportedTypes": [
      "OBJECT_STORE",
      "CONFIGURATION_STORE"
    ],
    "bucket": "kayenta",
    "rootFolder": "kayenta",
    "type": "aws",
    "locations": [],
    "recommendedLocations": []
  },
  {
    "name": "newrelic",
    "supportedTypes": [
      "METRICS_STORE"
    ],
    "endpoint": {
      "baseUrl": "https://insights-api.newrelic.com"
    },
    "type": "newrelic",
    "locations": [],
    "recommendedLocations": []
  }
]

```

## Kubernetes

_Coming Soon_

## TODO
- [ ] Include Nike's Referee UI
- [ ] Kubernetes deployment



