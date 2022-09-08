# tc-dev-integration-test
Testing github integration for dev deployment against [dev.alpha.taskcluster-dev.net](https://dev.alpha.taskclsuter-dev.net)

`.taskcluster.yml` contains configuration of what tasks will be started on `push` / `pull_request` events.

You can use few options for workers (`taskQueueId`):

* `built-in/succeed` - those tasks will always resolve successfully, no payload needed
* `built-in/fail` - those tasks will always fail, no payload needed
* `docker-compose/generic-worker` - this will run against generic worker, payload required. Success is optional :)

## Using generic-worker

At the moment dev.alpha deployment doesn't run generic worker, it only contains definition for this worker pool.

You can easily run generic worker from your local machine using `docker`:

```sh
# create worker configuration json file with proper credentials:
cat gen-work-dev.json
{
  "accessToken": "%access-token%",
  "clientId": "local-generic-worker-standalone",
  "ed25519SigningKeyLocation": "/etc/generic-worker/ed25519_key",
  "rootURL": "https://dev.alpha.taskcluster-dev.net",
  "workerId": "generic-worker",
  "provisionerId": "docker-compose",
  "workerType": "generic-worker",
  "livelogExecutable": "/usr/local/bin/livelog",
  "taskclusterProxyExecutable": "/usr/local/bin/taskcluster-proxy",
  "publicIP": "127.1.2.3"
}

# run worker that will connect to dev.alpha deployment and will start claiming tasks from docker-compose/generic-worker queue
docker run -it --rm \
  -e TASKCLUSTER_ROOT_URL=https://dev.alpha.taskcluster-dev.net \
  -e TASKCLUSTER_CLIENT_ID=local-generic-worker-standalone \
  -e TASKCLUSTER_ACCESS_TOKEN=%access-token% \
  -v $(pwd)/gen-work-dev.json:/etc/generic-worker/config.json \
  taskcluster/generic-worker:v44.20.4 \
  standalone
```

You will have to create client credentials for this worker and replace `TASKCLUSTER_CLIENT_ID`/`TASKCLUSTER_ACCESS_TOKENS` with correct values.

.1.2.1.
