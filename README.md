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

## CHange the Permssion of Pem file
```
- chmod 400 file.pem
```

## Connect to the Server
```
- ssh -i "<Path of your pem file>" ubuntu@<Public IP Address>
```
### Authneticity of the Host
```
- Are you sure you want to continue connecting (yes/no/[fingerprint])?
-- enter yes
```
### Explanation

- SSH is asking: *"Do you trust this server?"*
- The fingerprint is a unique hash of the server’s public key.
- If you type `yes`, SSH saves this fingerprint into a file called `~/.ssh/known_hosts`.
- Next time you connect, SSH will check the fingerprint again to make sure you’re connecting to the same server and not an imposter (**prevents man-in-the-middle attacks**).




## Upgrade Existing Package of Server
```
- sudo apt-get update && sudo apt-get -y upgrade 
```

## Create Deploy User
```
sudo adduser deploy
```
### Set the Password

### Provide Sudo privilege
```
sudo adduser deploy sudo 
```

## Generating ssh-key

```
- su - deploy
- ssh-keygen #do not set a passphrase for the key
- cat .ssh/id_rsa.pub
- copy the ssh-key & set it as your deploy key on your repository(project on bitbucket or github)
- copy your system public ssh-key & paste it into the following file on your instance as deploy user
  nano .ssh/authorized_keys #save & exit
```

## Installing GIT
```
sudo apt-get install git
```

## Installing ruby
```
- su - deploy
- sudo apt-get install libgdbm-dev libncurses5-dev automake libtool bison libffi-dev
- gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
- curl -sSL https://get.rvm.io | bash -s stable
- source ~/.rvm/scripts/rvm
- rvm install 2.5.1
- rvm use 2.5.1 --default
- ruby -v
- gem install bundler #install bundler
```

## Installing Nginx
```
- sudo apt-get install nginx
- sudo nano /etc/nginx/sites-available/default #to configure default site
```
