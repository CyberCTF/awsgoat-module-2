# AWSGoat: module 2

[AWSGoat](https://github.com/ine-labs/AWSGoat) by INE: a damn vulnerable AWS infrastructure. This
repository runs its module 2 with [Isoloom](https://www.isoloom.com) in your own AWS account:
[`isoloom.yml`](isoloom.yml) describes the cloud services, and AWSGoat sits unchanged in
[`app/`](app) (the Terraform root is `app/modules/module-2/`).

| Cloud services | What |
| --- | --- |
| ELB, ECS on EC2 | The payroll application (container `public.ecr.aws/p3q0v3y2/aws-goat-m2`) on a t2.micro container instance, behind an Application Load Balancer |
| RDS | A db.t3.micro MySQL database, its credentials in Secrets Manager |
| IAM, VPC | Instance and task roles with privilege escalation paths; the network |

Cost while it runs: about $0.07 an hour (load balancer, database, container instance and their
public IPv4 addresses).

## Run it

Use an AWS account with nothing else in it, signed in with the AWS CLI (`aws login`), and
Terraform installed. Upstream's deploy steps need bash 4.2 or later as `/bin/bash` and GNU
`sed -i`: run it on Linux (macOS ships bash 3.2).

```bash
isoloom run cloud-services     # about 6 minutes (the database), then stops on an error: see below
isoloom run cloud-services     # completes it
isoloom test cloud-services
isoloom down cloud-services
```

The first `run` stops with `Call to function "file" failed: function returned an inconsistent
result`: upstream's deploy step writes the database address into the task definition file after
Terraform has read it, which current Terraform refuses (upstream's own workflow pins Terraform
1.10.5). The second `run` reads the file with the address already in it and finishes (3 more
resources), then puts the placeholder back.

The container instance is a t2.micro: an account on the AWS Free plan refuses it (only
Free-Tier-eligible types such as t3.micro), so use an account on a paid plan.
Lab guide: [`app/attack-manuals/module-2/`](app/attack-manuals/module-2).

Upstream version and commit: [UPSTREAM.md](UPSTREAM.md).

## Licence

MIT, as AWSGoat ([LICENSE](LICENSE)). This lab is deliberately vulnerable: deploy it only in an
account you use for nothing else, and destroy it when you are done.
