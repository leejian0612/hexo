---
title: dea_ng learning
date: 2016-04-01 14:34:40
categories: cloudfoundry
tags:
---
### 安装测试环境
```   
git clone https://github.com/cloudfoundry/dea_ng.git  && cd dea_ng
```
启动虚拟机前一定要确认引用正确的cf-release目录
```
vim Vagrantfile
config.vm.synced_folder '~/Mywork/cf-release', '/var/cf-release'
```

启动并登陆到虚拟机
```
#start vagrant
vagrant up

#shell into the VM
vagrant ssh
```
```
#create rootfs
mkdir -p /tmp/warden/rootfs
sudo tar -xvf /var/cf-release/.blobs/`basename $(readlink /var/cf-release/blobs/rootfs/*)` -C /tmp/warden/rootfs > /dev/null
```
#### 启动[warden](https://github.com/cloudfoundry/warden)
```
#start warden
cd /var/cf-release/src/warden/warden
```
首先，bundle会根据当前目录下的Gemfile安装环境
```
sudo bundle install
```
然后，通过rake(ruby make)调用Rakefile中对应的脚本，启动warden
```
bundle exec rake setup:bin
sudo bundle exec rake warden:start[config/linux.yml] &> /tmp/warden.log &
```
 在`bundle exec rake warden:start[config/linux.yml]`里，warden指定Rakefile里的namespace, start指定task

```ruby
namespace :warden do
  desc "Run Warden server"
  task :start, :config_path do |t, args|
    lib = File.expand_path("../lib", __FILE__)
    $LOAD_PATH.unshift(lib) unless $LOAD_PATH.include?(lib)

    require "warden/server"

    if args[:config_path]
      config = YAML.load_file(args[:config_path])
    end

    Warden::Server.setup(config || {})
    Warden::Server.run!
  end
end

```
#### 启动dea
```
# start the DEA's dependencies
cd /var/cf-release/src/dea_next
export GOPATH=$PWD/go
go get github.com/nats-io/gnatsd
sudo bundle install
sudo bundle exec foreman start &> /tmp/foreman.log &
```

#### 运行单元测试和集成测试
```
#To run the tests (unit or integration - these must be run separately if run as suites):
bundle install
bundle exec rspec spec/unit
bundle exec rspec spec/integration
```