# Practice Test - Commands and Arguments

## Practice files

* [ubuntu-sleeper-2.yaml](ubuntu-sleeper-2.yaml)
* [webapp-color-green.yaml](webapp-color-green.yaml)

## Exercises

**What is the command used to run the pod 'ubuntu-sleeper'?**

```bash
$ kubectl describe pod ubuntu-sleeper
```

**Create a pod with the ubuntu image to run a container to sleep for 5000 seconds. Modify the file ubuntu-sleeper-2.yaml.**

```bash
$ kubectl create -f ubuntu-sleeper-2.yaml
```

**Create a pod with the given specifications. By default it displays a 'blue' background. Set the given command line arguments to change it to 'green'**

```bash
$ kubectl create -f webapp-color-green.yaml
```
