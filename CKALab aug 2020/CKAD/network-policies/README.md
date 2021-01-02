# Practice Test - Network Policies

## Exercises

**How many network policies do you see in the environment?**

`$ kubectl get networkpolicy`

**What type of traffic is this Network Policy configured to handle?**

> Ingress

`$ kubectl describe networkpolicy payroll-policy`

**What is the impact of the rule configured on this Network Policy?**

> Traffic From Internal to Payroll POD is allowed
> Internal POD can access port 8080 on Payroll POD

**Create a network policy to allow traffic from the 'Internal' application only to the 'payroll-service' and 'db-service'Create a network policy to allow traffic from the 'Internal' application only to the 'payroll-service' and 'db-service'**

> Use the spec given. Policy Name: internal-policy, Policy Types: Egress, Egress Allow: payroll, Payroll Port: 8080, Egress Allow: mysql, MYSQL Port: 3306

```bash
# Create a YAML file for network policy
$ kubectl create -f internal-policy.yaml
```

**Working with Kubernetes NetworkPolicies**

Your company has a set of services, one called inventory-svc and another called customer-data-svc. In the interest of security, both of these services and their corresponding pods have NetworkPolicies designed to restrict network communication to and from them. A new pod has just been deployed to the cluster called web-gateway, and this pod need to be able to access both inventory-svc and customer-data-svc.

Unfortunately, whoever designed the services and their corresponding NetworkPolicies was a little lax in creating documentation. In top of that, they are not currently available to help you understand how to provide access to the services for the new pod.

Examine the existing NetworkPolicies and determine how to alter the web-gateway pod so that it can access the pods associated with both services.

> You will not need to add, delete, or edit any NetworkPolicies in order to do this. Simply use the existing ones and modify the web-gateway pod to provide access. All work can be done in the default namespace.

```bash
$ kubectl get networkpolicies
$ kubectl describe networkpolicy inventory-policy
```

```yaml
From:
  PodSelector: inventory-access=true
```

```bash
# Add label inventory-access=true to web-access POD
$ kubectl label pods web-gateway inventory-access=true
# Access inventory-svc via web-gateway
$ kubectl exec web-gateway -- curl -m 3 inventory-svc
# Do the same for customer-data-svc
$ kubectl label pods web-gateway customer-data-access=true
$ kubectl exec web-gateway -- curl -m 3 customer-data-svc
```
