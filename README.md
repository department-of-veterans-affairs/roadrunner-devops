# Roadrunner DevOps
Starter project for automating deployment of a [roadrunner-rails](https://github.com/department-of-veterans-affairs/roadrunner-rails) (Rails + rbenv) project in two steps:

1. Automates installation of an _application_ to an _instance_ (local Vagrant, EC2, or any other system).
1. Automates deployment of _application instances_ to the AWS cloud environment by creating a production-ready _AMI image_.

## What You Get: Overview
* Launch a new instance
* Deploy your rails application to the instance as a puma server (automatically detects and installs proper ruby version).
* Configure application user and upstart script
* Configure CloudWatch logs agent
* Configure mechanism to pass environment variables to application via EC2 tags so the same image can be deployed to multiple environments without modification.
* Take a production-ready snapshot of the instance
* `Coming Soon` Duplicate an existing launch configuration
* `Coming Soon` Modify it to use the newly created AMI and save as new launch configuration
* `Coming Soon` Modify existing auto-scaling group to use the above launch configuration.
* `Coming Soon` Start terminating instances to automatically be replaced by the above auto-scaling group (optionally removing them from load-balancer first).

## Quickstart: AWS
Deploy a roadrunner application to Vagrant instance(s). Prerequisites:

* Vagrant
* Virtualbox

1. Start vargrant
1. SSH to "jumpbox" driving the ansible process
1. Run ansible from local files (via default Vagrant [`synced_folder`](synced_folder ))
```shell
> vagrant up
> vagrant ssh ansible_host
$ ansible-playbook -i /vagrant/hosts.vagrant /vagrant/roadrunner-empty.yml -e "skip_update=true"
```

To verify,

1. reboot an instance
1. SSH into the instance
1. Verify puma is running on desired port (or visit forwarded port on [localhost](http://localhost:8881))

```shell
$ curl localhost:3000
<!DOCTYPE html>
<html>
  <head>
    <title>Ruby on Rails: Welcome aboard</title>
  ...
  ...
```

## Quickstart: AWS
Create an AMI imae of a roadrunner application. Prerequisites:
* Ansible to drive the process;
* An AWS environment with
  - Subnet
  - Security group
  - Keypair


1. Edit the `roadrunnner-empty-aws.yml` file and modify the AWS-related variables.
1. Run ansible

```shell
$ ansible-playbook -i localhost, roadrunner-empty-aws.yml --private-key [PATH TO YOUR KEY]
```

## Customization
This project deploys a sample Rails application but the conventions can be used on any application:

1. Configure base OS: `centos` role
1. Install and configure dependencies: `ruby`
1. Assemble application artifacts: `git` and `rails-build`
1. Configure application: `process`

## What You Get: Details
##### Step 1: Install application on target instance
- Update OS
- Install required packages
- Configure OS for production use
  - Time zone, `ntpd` service
  - Appropriate `ulimit`, e.g., open files (sockets)
  - Create user accounts for sysadmins with keys
- Obtain latest application, either
  1. Download pre-assembled from a central repository like S3
  1. Assemble from source by checking out and compiling
- Copy environment-specific variables
- Add `upstart` script
- Install logging and monitoring agents (*production*)

##### Step 2: Deploy instance to cloud environment
- "Bake" AMI of newly packaged instance (from above)
- Previously provisioned `autoscaling group` is running and behind ELB
- The new `autoscaling group` and `launch configuration` are provisioned side-by-side, behind the same ELB.
- Wait for new instances to become available
- Terminate previous `autoscaling group` and associated instances.

## Resources
* [Ansible Directory Structure](http://docs.ansible.com/ansible/playbooks_best_practices.html#directory-layout)
* [Cloud Formation best practices](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/best-practices.html)
* [12-Factor App](http://12factor.net/)
* [Immutable Systems and Ansible](https://www.ansible.com/blog/immutable-systems)
* [EC2 Auto Scaling with Ansible](https://atplanet.co/blog/ec2-auto-scaling-with-ansible.html)
* [The Upstart Event System: What It Is And How To Use It](https://www.digitalocean.com/community/tutorials/the-upstart-event-system-what-it-is-and-how-to-use-it)
