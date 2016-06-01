## How to Deploy eFolder-Express to AWS GovCloud
* Configure SSH agent forwarding with the proper environment key
* SSH to govcloud jumpbox using above key
* Change directory to this deployment's Ansible scripts in `/opt/appeals-deploys`
* Export your AWS access key and secret key to the environment (prefix with a space so that it isn't stored in `.bash_history`).

```bash
$  export AWS_ACCESS_KEY_ID="xxx"
$  export AWS_SECRET_ACCESS_KEY="yyy"
```

* Run the `efolder-express-aws.yml` playbook to automate the following steps.
 - Launch a new instance
 - Deploy eFolder-Express to the instance
 - Take a production-ready snapshot of the instance

```bash
 $ ansible-playbook -i localhost, efolder-express-aws.yml --ask-vault-pass
```

* Create an EC2 *Launch Configuration* using the newly created AMI
 - Duplicate an existing configuration, e.g., `dsva-appeals-efolder_express-20160526`
 - Configure to use the newly created AMI
 - Rename
* Modify the existing *Auto-Scaling Group* to use the newly created configuration.
* Begin terminating old instances running the previous image (optionally remove them from load balancer first).

> NOTE: The last 3 steps will be automated in the future..
