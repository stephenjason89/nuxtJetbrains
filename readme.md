
<p align="center">
  <a href="https://laravel.com"><img src="https://onecentlin.gallerycdn.vsassets.io/extensions/onecentlin/laravel-extension-pack/0.4.0/1534522609664/Microsoft.VisualStudio.Services.Icons.Default"  width="128" height="128"/></a>
  <img src="https://raw.githubusercontent.com/acidjazz/aeonian/master/media/plus.png"/>
  <a href="https://nuxtjs.org/"><img src="https://images.opencollective.com/proxy/images?src=https%3A%2F%2Fopencollective-production.s3-us-west-1.amazonaws.com%2F63047830-23b9-11e9-8073-c73f9d8c047d.png&height=480"  width="128" height="128"/></a>
</p>

<p align="center">
  <a href="https://vuejs.org"><img src="https://vuejs.org/images/logo.png" width="92" height="92" /></a>
  <a href="https://tailwindcss.com"><img src="https://pbs.twimg.com/profile_images/895274026783866881/E1G1nNb0_400x400.jpg" width="92" height="92" /></a>
  <a href="https://stylus-lang.com"><img src="https://avatars0.githubusercontent.com/u/10009463?s=400&v=4" width="92" height="92" /></a>
  <a href="https://github.com/acidjazz/metapi"><img src="https://github.com/acidjazz/metapi/raw/master/logo.png" width="92" height="92" /></a>
  <a href="https://materialdesignicons.com"><img src="https://lh3.googleusercontent.com/kellzw4-4Q258D_HdHvcclbu2HEheO1TxauO4lmI5T6tCDnk8pvUfh0W0WpvKiB54g=s96-rw" width="92" height="92" /></a>
  <a href="https://pugjs.org/"><img src="https://camo.githubusercontent.com/a43de8ca816e78b1c2666f7696f449b2eeddbeca/68747470733a2f2f63646e2e7261776769742e636f6d2f7075676a732f7075672d6c6f676f2f656563343336636565386664396431373236643738333963626539396431663639343639326330632f5356472f7075672d66696e616c2d6c6f676f2d5f2d636f6c6f75722d3132382e737667" width="92" height="92" /></a>
</p>

## Laravel + Nuxt.js Boilerplate

### What is included

* [NUXT](https://nuxtjs.org) for our SPA vue.js powered front-end
  * [@nuxtjs/axios](https://github.com/nuxt-community/axios-module) to communicate with our API
  * [@nuxtjs/proxy](https://github.com/nuxt-community/proxy-module) sending `/api` to Laravel
  * [@nuxtjs/tailwindcss](https://github.com/nuxt-community/nuxt-tailwindcss) a [utility-first](https://tailwindcss.com) framework
  * [pug](https://pugjs.org) -  high-performance template engine
  * [mdi](https://materialdesignicons.com) - material design icons with a ton of contributed ones as well

* [Laravel](https://laravel.com) - for our API
  * [metapi](https://github.com/acidjazz/metapi) - API helpers and utilities
  * [debugbar](https://github.com/barryvdh/laravel-debugbar) - awesome debugbar for our API

### Installation

* clone from github
* run `yarn` and `composer install` to install all of your deps
* copy `.env.example` to `.env` and configure it to your likings
  * i do this to speed up reactivity and compilation time
* running `yarn mdi` will copy all the fonts and css to `resources/static/`
* run `yarn logs` to create laravels needed storage logs folders
* TL;DR `git clone git@github.com:acidjazz/laranuxt.git; cd laranuxt; yarn; composer install; yarn mdi; cp .env.example .env; yarn logs;`

### Local environment
* run `yarn api` (alias for `./artisan serve`) in another terminal for our laravel API
* run `yarn dev` in one terminal for our nuxt dev setup
* I've also included a simple [byobu](http://byobu.co/) script that starts everything up, just change `PROJECT` to your project folder name 

### AWS configuration (WIP)
This boilerplate also includes scripts to help you use it as an [Auto Scaling Group](https://docs.aws.amazon.com/autoscaling/ec2/userguide/AutoScalingGroup.html) with a [Launch Configuration](https://docs.aws.amazon.com/autoscaling/ec2/userguide/LaunchConfiguration.html)
* You can find `cloudinit-ami.sh` in `config/` which when configured and used as user-data will fire up an instance w/ the latest and all its dependencies to make an AMI
* You can find `cloudinit-delploy.sh` in `config/` which when configured can be used with a Launch Configuration to spin up more instances when needed
* The following are stipulations for these scripts to wrok functionality
  * You use the Amazon Linux 2 AMI
  * You have an s3 bucket holding your deploy keys and `.env` files, default to `${PROJECT}-vault`
    * key would be in `s3://project-vault/keys/` as `id_rsa`
    * envs would be in `s3://project-vault/envs/` as `env-${branch}` (env-staging, env-master, etc)
* Using these configs nuxt.js will run on port `3000` and then Laravel will run on port `8000`, you can make a [Target Group](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html) for each of these sending `80` and/or `443` to `3000`

### CircleCI Testing and Deployment 
This boilerplate also includes a sample starter script to run tests and then deploy to your group of instances
* This configuration works assuming your setup matches the above AWS config
* Your EC2 group must have a tag key of `ssm` and value of your branch/environment, either `staging` or `master`
* You can find, tweak, and use `config/circleci-config.yml` and move it to `.circleci/config.yml` when ready
* Upon successful tests, this will use the [AWS SSM Agent](https://docs.aws.amazon.com/systems-manager/latest/userguide/ssm-agent.html) to run `yarn ${branch}` on each instance which will:
  * Grab the latest code
  * Re-download your `.env` from the s3 bucket
  * Refresh the icons file
  * Re-generate the nuxt.js static files
  * Restart the pm2 server with the newer static files
  * Via composer, run `artisn migrate`, `artisan config:clear`, `artisan cache:clear` and `composer install`
