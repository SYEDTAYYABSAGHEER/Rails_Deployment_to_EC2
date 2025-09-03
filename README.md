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
- rvm install 3.3.0
- rvm use 3.3.0 --default
- ruby -v
- gem install bundler #install bundler
```

## Insatll Redis Server
```
sudo add-apt-repository ppa:chris-lea/redis-server
```

## Insatll Node
```
curl -sL https://deb.nodesource.com/setup_16.x | sudo -E bash -
sudo apt-get install -y nodejs
```

## Install Yarn
```
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
```

## Other Dependenices
```
 sudo apt-get install git-core curl zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev software-properties-common libffi-dev dirmngr gnupg apt-transport-https ca-certificates redis-server redis-tools nodejs yarn

```

## Install PostgreSQL
```
# setup postgresql
- sudo sh -c "echo 'deb http://apt.postgresql.org/pub/repos/apt/ xenial-pgdg main' > /etc/apt/sources.list.d/pgdg.list"
- wget --quiet -O - http://apt.postgresql.org/pub/repos/apt/ACCC4CF8.asc | sudo apt-key add -
- sudo apt-get update
- sudo apt-get install postgresql-common
- sudo apt-get install postgresql-16 libpq-dev
```

## Create Positgresl user
```
- sudo -su postgres
- createuser --pwprompt deploy

```
### Create database in postgres
```
- createdb -O deploy name_production_db
```

## Create files required by capistrano
```
- mkdir <application name>
- mkdir -p <application name>/shared/config
- nano <application name>/shared/config/database.yml

#for rails 5.2 'master.key' file is used for secret key
- nano <application name>/shared/config/master.key
- add key from master.key of your application to this file
  eg: da54dd7f691dd9d8707c3f67ec9d076f
#for rails lower versions 'application.yml' or 'secrets.yml' is used for secret key
- nano <application name>/shared/config/application.yml
  OR
- nano <application name>/shared/config/secrets.yml
- add SECRET_KEY_BASE from your application to this file (or you can generate your own secret key base)
  SECRET_KEY_BASE: "8a2ff74119cb2b8f14a85dd6e213fa24d8540fc34dcaa7ef8a35c246ae452bfa8702767d19086461ac911e1435481c22663fbd65c97f21f6a91b3fce7687ce63"
```

### 🔑 How to generate credentials.yml.enc and master.key
```
EDITOR="nano" bin/rails credentials:edit
```

## Installing Nginx
```
- sudo apt-get install nginx
- sudo nano /etc/nginx/sites-available/default #to configure default site
```



### Rails application setup for capistrano

```
#configuring capistrano
#add this to gemfile
group :development do
  gem 'capistrano'
  gem 'capistrano3-puma'
  gem 'capistrano-rails', require: false
  gem 'capistrano-bundler', require: false
  gem 'capistrano-rvm'
end
- bundle install

#create configuration files for capistrano
- bundle exec cap install
  #this command will create
  config/deploy.rb
  config/deploy/production.rb
  config/deploy/staging.rb

#add these line to capfile
require 'capistrano/bundler'
require 'capistrano/rvm'
require 'capistrano/rails/assets' # for asset handling add
require 'capistrano/rails/migrations' # for running migrations
require 'capistrano/puma'
install_plugin Capistrano::Puma  # Default puma tasks

#upload puma config  
- cap production puma:config
```
