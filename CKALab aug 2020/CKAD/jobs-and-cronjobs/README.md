# Practice Test - Jobs & CronJobs

## Exercises

**A pod definition file named throw-dice-pod.yaml is given. The image 'throw-dice' randomly returns a value between 1 and 6. 6 is considered 'success' and all others are 'failure'. Try deploying the POD and view the POD logs for the generated number.**

`$ kubectl create -f throw-dice-pod.yaml`

**Create a Job using this POD definition. Look at how many attempts does it take to get a '6'.**

> Job Name: throw-dice-job, Image Name: kodekloud/throw-dice 

```bash
$ kubectl create job throw-dice-job --image=kodekloud/throw-dice --dry-run=client -o yaml > throw-dice-job.yaml
# Edit YAML file
```

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: throw-dice-job
spec:
  backoffLimit: 15 # This is so the job does not quit before it succeeds.
  template:
    spec:
      containers:
      - name: math-add
        image: kodekloud/throw-dice
      restartPolicy: Never
```

Monitor and wait for the job to succeed. Throughout this practice test remember to increase the 'BackOffLimit' to prevent the job from quitting before it succeeds.

**Update the job definition to run as many times as required to get 3 successful 6's**

> Job Name: throw-dice-job, Image Name: kodekloud/throw-dice, Completions: 3, Job Succeeded: True

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: throw-dice-job
spec:
  completions: 3
  backoffLimit: 25 # This is so the job does not quit before it succeeds.
  template:
    spec:
      containers:
      - name: math-add
        image: kodekloud/throw-dice
      restartPolicy: Never
```

**That took a while. Let us try to speed it up, by running upto 3 jobs in parallel.**

> Job Name: throw-dice-job, Image Name: kodekloud/throw-dice, Completions: 3, Parallelism: 3

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: throw-dice-job
spec:
  completions: 3
  parallelism: 3
  backoffLimit: 25 # This is so the job does not quit before it succeeds.
  template:
    spec:
      containers:
      - name: math-add
        image: kodekloud/throw-dice
      restartPolicy: Never
```

**Let us now schedule that job to run at 21:30 hours every day.**

> CronJob Name: throw-dice-cron-job, Image Name: kodekloud/throw-dice, Schedule: 30 21 * * *

```yaml
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: throw-dice-cron-job
spec:
  schedule: "30 21 * * *"
  jobTemplate:
    spec:
      completions: 3
      parallelism: 3
      backoffLimit: 25 # This is so the job does not quit before it succeeds.
      template:
        spec:
          containers:
          - name: math-add
            image: kodekloud/throw-dice
          restartPolicy: Never
```
