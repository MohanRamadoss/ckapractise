### Test infrastructure

## Exercises

### kubetest - Run

```bash
$ go get -u k8s.io/test-infra/kubetest
$ kubetest --extract=v1.11.3 # Version must match the kubernetes server version
$ cd kubernetes
$ export KUBE_MASTER_IP="192.168.26.10:6443"
$ export KUBE_MASTER=kube-master
$ kubetest --test --provider=skeleton > testout.txt
$ kubetest --test --provider=skeleton --test_args="--ginkgo.focus=Secrets" > testout.txt
$ kubetest --test --provider=skeleton --test_args="--ginkgo.focus=\[Conformance\]" > testout.txt
$ kubetest --test --provider=skeleton --test_args="--ginkgo.focus=\[Conformance\]" > testout.txt
```