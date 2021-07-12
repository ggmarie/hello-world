
# Harbinger

Harbinger is a canary code that contacts a DMS to check satellite uptime and readiness for uploading and downloading data.

To run harbinger locally, you will need a test bucket and its corresponding access token.

How to run:

## Run etcd and DMS containers
```
docker run -it -e ALLOW_NONE_AUTHENTICATION=yes -p 2379:2379 -p 2380:2380 --platform linux/amd64 bitnami/etcd:latest
```

```
docker run -it -p 1323:1323 --entrypoint /app/dms kristaxox/dms --basic-auth-username="admin" --basic-auth-password="admin" --jwt-secret="supersecret" --endpoints="192.168.1.94:2379" --heartbeat-expiration 5m
```

## Register heartbeat with DMS

Running this curl command will give you a DMS token that you will use in the next step.
```
curl --user admin:admin -d "environment=canary-us1" localhost:1323/register | jq .
```

## Run Harbinger

Run the main.go file using the desired bucket, its access token, and the dms token given previously.
```
go run main.go --access <access> --bucket <bucket> --file-path harb.txt --token <dms-token> --dms-endpoint http://localhost:1323 --log-level debug
```


If the satellite is up and running, the program will return the debug message "dms checkin completed".