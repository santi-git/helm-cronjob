# helm-cronjobs
You can define an array of jobs in values.yaml helm will take care of creating all the CronJobs.

## Configuration
template:
```yaml
jobs:
  ### REQUIRED ###
  - name: <job_name>
    image:
      repository: <image_repo>
      tag: <image_tag>
      imagePullPolicy: <pull_policy>
    schedule: "<cron_schedule>"
    failedJobsHistoryLimit: <failed_history_limit>
    successfulJobsHistoryLimit: <successful_history_limit>
    concurrencyPolicy: <concurrency_policy>
    restartPolicy: <restart_policy>
  ### OPTIONAL ###
    env:
    - name: ENV_VAR
      value: ENV_VALUE
    envFrom:
    - secretRef:
      name: <secret_name>
    - configMapRef:
      name: <configmap_name>
    command: ["<command>"]
    args:
    - "<arg_1>"
    - "<arg_2>"
    resources:
      limits:
        cpu: <cpu_count>
        memory: <memory_count>
      requests:
        cpu: <cpu_count>
        memory: <memory_count>
    serviceAccount:
      name: <account_name>
      annotations:  # Optional
        my-annotation-1: <value>
        my-annotation-2: <value>
    nodeSelector:
      key: <value>
    tolerations:
    - effect: NoSchedule
      operator: Exists
    volumes:
      - name: config-mount
        configMap:
          name: configmap-name
          items:
            - key: configuration.yml
              path: configuration.yml
    volumeMounts:
      - name: config-mount
        mountPath: /etc/config
    affinity:
      nodeAffinity:
        requiredDuringSchedulingIgnoredDuringExecution:
          nodeSelectorTerms:
          - matchExpressions:
            - key: kubernetes.io/e2e-az-name
              operator: In
              values:
              - e2e-az1
              - e2e-az2
```

## Testing
```
$ helm install test-cron-job .
NAME:   cold-fly
LAST DEPLOYED: Fri Feb  1 15:29:21 2022
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1beta1/CronJob
NAME                    AGE
Aya-hello-world    1s
Aya-hello-ubuntu   1s
Aya-hello-env-var  1s
```
list cronjobs:
```
$ kubectl get cronjob
NAME                     SCHEDULE      SUSPEND   ACTIVE    LAST SCHEDULE   AGE
Aya-hello-env-var   * * * * *     False     0         23s             1m
Aya-hello-ubuntu    */5 * * * *   False     0         23s             1m
Aya-hello-world     * * * * *     False     0         23s             1m
```
list jobs:
```
$ kubectl get jobs
NAME                                DESIRED   SUCCESSFUL   AGE
Aya-hello-env-var-1549056600   1         1            45s
Aya-hello-ubuntu-1549056600    1         1            45s
Aya-hello-world-1549056600     1         1            45s
```
