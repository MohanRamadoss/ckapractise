# Practice Test - Certificates API

## Exercises

**Create a CertificateSigningRequest object with the name akshay with the contents of the akshay.csr file**

```bash
$ kubectl create -f akshay-csr.yaml
# request -> cat akshay.csr | base64 | tr -d '\n'
```

**What is the Condition of the newly created Certificate Signing Request object?**

```bash
$ kubectl get csr
```

**Approve the CSR Request**

```bash
$ kubectl certificate approve akshay
```

**Who requested the csr-* request?**

```bash
$ kubectl get csr
```

**Hmmm.. You are not aware of a request coming in. What groups is this CSR requesting access to?**

```bash
$ kubectl get csr agent-smith -o yaml
```

**That doesn't look very right. Reject that request.**

```bash
$ kubectl certificate deny agent-smith
```

**Let's get rid of it. Delete the new CSR object**

```bash
$ kubectl delete csr agent-smith
```
