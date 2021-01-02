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

**Create a pod using the file named 'ubuntu-sleeper-3.yaml'. There is something wrong with it. Try to fix it!**

```bash
# Look at seconds in definition file
$ kubectl create -f ubuntu-sleeper-3.yaml
```

**Inspect the file 'Dockerfile' given. What command is run at container startup?**

```bash
# look at the line ENTRYPOINT
```

**Inspect the file 'Dockerfile2' given. What command is run at container startup?**

```bash
# Look at the lines ENTRYPOINT and CMD combined
```

**Create a pod with the given specifications. By default it displays a 'blue' background. Set the given command line arguments to change it to 'green'**

> Pod Name: webapp-green, Image: kodekloud/webapp-color, Command line arguments: --color=green
 
```bash
$ kubectl run webapp-green --image=kodekloud/webapp-color -- --color=green
```
