# Rails_Deployment_to_EC2

## Creating AWS EC2 Instance

```


- login to 'AWS Management Console' (https://aws.amazon.com/console/)
- from 'Services'(in navbar) choose 'EC2'
- from 'Create Instance' section, click on 'Launch Instance'
- then select 'AMI' (Amazon Machine Image), we will be using 'Ubuntu Server 16.04 LTS (HVM)' as example
- select 'Instance Type' as per your requirement
- then click 'Next:Configure Instance Details' to continue
  change 'Configure Instance Details' or used as default settings
- click 'Next: Add Storage'
  adjust size as per your need (default is 8GB)
- click 'Next: Tag Instance'
  create tag if you need any tagging for your instance
- click 'Next: Configure Security Group'
  click 'Add Rule' to add new rule(for port, ip address etc.)
  default you can set it to -
  SSH      TCP     22     Anywhere     0.0.0.0/0
  HTTP     TCP     80     Anywhere     0.0.0.0/0 
- then click 'Review and Launch'
  check all your setting, then click 'Launch'
- select existing or create new keypair to connect to your instance
  click 'Launch Instance', this will generate the key (in case of new keypair) & launches your instance
  find 'Public IP' from 'Description'

  ```

## Setup Elastic IP

```
- you can generate an Elastic IP from 'Network & Security' tab
- click 'Allocate new address'
- then click 'Allocate', this will generate an Elastic IP
- click 'Close', this will redirect you to Elastic IP Dashboard
- select you Elastic IP & on right click select 'Associate address'
- choose you instance (whom you want to associate) & click 'Associate'
  this will replace your Public IP with Elastic IP (& now this IP is used)
```

