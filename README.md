⚠️ DO NOT FORK THIS REPOSITORY! ⚠️ \
⚠️ DO NOT OPEN A PULL REQUEST! ⚠️

# MCA DevOps test

We have a classic N-tier application:

- a [backend](./backend/): a Java/SpringBoot 3/Maven application exposing endpoints to list and add users
- a [frontend](./frontend/): an Angular application to interact with the backend
- a PostgreSQL database hosted externally at an address (e.g. `my-db-test.io`) and with a user "`myapplication`" and "`M3P@ssw0rd!`"

## Instructions

The exercise is to start this application entirely in Kubernetes, using tools like kubectl, Helm or other.

The database access information should be exposed through the environment variables `SPRING_POSTGRES_URL`, `SPRING_POSTGRES_USERNAME` and `SPRING_POSTGRES_PASSWORD`.

## To perform the exercise

Keep it simple and don't overcomplicate things.
For example, there's no need to try to hide the environment variables.

To facilitate the creation of a K8S cluster, consider using one of:

- [Play with Kubernetes](https://labs.play-with-k8s.com/)
- minikube
- kind
- ...

The solution for all of this will be done on GitHub.

**Bonus 1**: Exposing the application using a reverse proxy (like ingress nginx or others) would be a plus.

**Bonus 2**: It would be interesting to build the backend and/or frontend yourself by writing an adapted `Dockerfile`.

**Bonus 2.1**: Using a multistage build would be a plus.
