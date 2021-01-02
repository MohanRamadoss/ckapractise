# Practice Test - Network Policies

## Exercises

**How many network policies do you see in the environment?**

```bash
$ kubectl get networkpolicy
```

**What is the impact of the rule configured on this Network Policy?**

> Traffic From Internal to Payroll POD is allowed
> Internal POD can access port 8080 on Payroll POD

**Create a network policy to allow traffic from the 'Internal' application only to the 'payroll-service' and 'db-service'Create a network policy to allow traffic from the 'Internal' application only to the 'payroll-service' and 'db-service'**

> Use the spec given. Policy Name: internal-policy, Policy Types: Egress, Egress Allow: payroll, Payroll Port: 8080, Egress Allow: mysql, MYSQL Port: 3306

```bash
$ kubectl create -f internal-policy.yaml
```
