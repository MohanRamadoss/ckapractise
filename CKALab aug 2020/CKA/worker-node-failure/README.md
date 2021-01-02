# Practice Test - Worker Node Failure

## Troubleshooting

```bash
$ kubectl get nodes
$ kubectl describe node <node_name>
$ ssh <node>
$ top
$ df -h
$ service kubelet status
$ sudo journalctl -u kubelet
$ openssl x509 -in /var/lib/kubelet/worker-1.crt -text
```

## Exercises

**The cluster is broken. Investigate and fix the issue.**

```$
$ kubectl describe node node01
$ ssh node01
node01 $ service kubelet status
● kubelet.service - kubelet: The Kubernetes Node Agent
   Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
  Drop-In: /etc/systemd/system/kubelet.service.d
           └─10-kubeadm.conf
   Active: inactive (dead) since Wed 2020-07-22 11:58:47 UTC; 3min 41s ago
     Docs: https://kubernetes.io/docs/home/
  Process: 2669 ExecStart=/usr/bin/kubelet $KUBELET_KUBECONFIG_ARGS $KUBELET_CONFIG_ARGS $KUBELET_KUBEADM_ARGS $KUBELET_EXTRA_ARGS (code=exited, status=0/SUCCESS)
 Main PID: 2669 (code=exited, status=0/SUCCESS)
node01 $ service kubelet start
```

**The cluster is broken again. Investigate and fix the issue.**

```bash
$ service kubelet status
$ sudo journalctl –u kubelet -f
Jul 22 12:07:31 node01 kubelet[19803]: F0722 12:07:31.338526   19803 server.go:254] unable to load client CA file /etc/kubernetes/pki/WRONG-CA-FILE.crt: open /etc/kubernetes/pki/WRONG-CA-FILE.crt: no such file or directory
$ vi /var/lib/kubelet/config.yaml
# Change the path of cert file
```

**The cluster is broken again. Investigate and fix the issue.**

```bash
$ kubectl get nodes -o wide --no-headers
master   Ready      master   19m   v1.18.0   172.17.0.15   <none>   Ubuntu 18.04.4 LTS   4.15.0-101-generic   docker://19.3.6
node01   NotReady   <none>   18m   v1.18.0   172.17.0.17   <none>   Ubuntu 18.04.4 LTS   4.15.0-101-generic   docker://19.3.6

$ kubectl describe node node01
$ cat /etc/kubernetes/kubelet.cnf
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01EY3lNakV4TlRZMU5Wb1hEVE13TURjeU1ERXhOVFkxTlZvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTWUxCkYzUW9uNFpzQ0MxMm5yeW5WRGFPZ2IzM0w3eENRVDMzbnc0SkxZRC91THFSRTFHK0wyMThKZ0RFdTR3WmNVdloKQ0xCbkcwQkZtcmV6ZXJXL25xK2xFNjM4aVlQbWhxT3JLN3RnWUlZeGJoNFVNQkRZZWlOL0pvTmZHVi9XZlk5MQpOanZrbnJBczZCSk1vYTFHNi8zUzVkOFh2TnB0MEd2ZCtXSWNGR2NIV0tocUxvQlVMUE50d0dzdFJoemVNTFZ3Cm1KNFFMdGw5UUk1bmVoaXB2ZUxrakRhME5oV05mWEwvUGJjZ09qN1VKZEtpSlZpdjdlSTJPN08zbEgrOGNlMmgKbzhZQVJJWTBlWU1lNkc0cE1CSUhnSFZWZjcxdWRxMTYybFZkV1ZsMFVNYlVEWFRLb0NIbjZHL1ZRc0tHMkgrUwp5Szk1WndsOGZHTENqTWxaeFZjQ0F3RUFBYU1qTUNFd0RnWURWUjBQQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCCi93UUZNQU1CQWY4d0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFJZW9lck93NHN5Y1pBOTcxV3IzblhoOEtFSW4KaTB6WWlVWDBySTRtaDhxbG4xM0xJS3dVR25uL0lXTEZyZjNUelN1QnNtb2hYM1J2UTlqLzRWeVJyU3RHUnhSTwoxaEVUa1BkbFMwd3lnVi8rRXZHenp1ODQ1dGZPazNyV2hMVVRycUNNYTRQKzkxWmxEbVgvNllydU1INW9xTEw2CjVlcml5aXIyc2FMdnZNOXNQb09YUU92eCtEcFBZQTdYZVlOTVllMWJRWGthZFlZWUp2SEdjU0xzbWw4TzRPejcKbll0OHBTWmxqVCsvK1JtcVlFL2kySjduRlM3YkFBME1VR2tTVmErTEJuQ3JxUWVvK1RoRFg4ZjRvSVF1UHFoZQpsVE04ajhlalhlMmRtcVpEQnBXNk4wdFQxVHNycGlEanh4TmcyM3hWU0RPOXV2K29hV3BhVXMvTGlldz0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
    server: https://172.17.0.15:6553 => wrong port
```

