# Tekton Pipeline example with Gradle 

This project is example how to migrate legacy groovy jenkins pipelines to cloud native tekton 

## Requirements 

- Docker Desktop

## Creating k8s cluster and 

Gradle default task `all-tasks` will do 

- setup local cluster
- deploy tekton
- deploy tasks and pipelines
- execute pipeline using tekton pipeline-run

```./gradlew```

## Open Dashboard k9s 

```docker exec -it dev-tools k9s```

