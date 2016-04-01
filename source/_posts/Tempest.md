---
title: Tempest
comments: true
categories: openstack
toc: true
---
Tempest for OpenStack Integration Tests

### install and configure tempest:
`git clone https://github.com/openstack/tempest`     
`cd ~/tempest && python setup.py install`       
`cp ./etc/tempest.conf.sample /etc/tempest.conf`    

### modify the config file
`vim  /etc/tempest.conf`    

```
[DEFAULT]
use_stderr = False
log_file = /home/leejian/work/tempest.log
lock_path = /tmp
default_log_levels = tempest.stress=INFO,amqplib=WARN,sqlalchemy=WARN,boto=WARN,suds=INFO,keystone=INFO,eventlet.wsgi.server=WARN
...
[compute-admin]
username = admin
password = lee0612
tenant_name = admin
...

[identity]
username = tempest1
password = passw0rd
tenant_name = tempest1
alt_username = tempest2
alt_password = passw0rd
alt_tenant_name = tempest2
admin_username = admin
admin_password = lee0612
admin_tenant_name = admin
region = RegionOne
uri = http://localhost:5000/v2.0/
uri_v3 = http://127.0.0.1:5000/v3/
...

[network-feature-enabled]
api_extensions = ,service-type,ext-gw-mode,security-group,l3_agent_scheduler,fwaas,binding,metering,agent,quotas,dhcp_agent_scheduler,l3-ha,multi-provider,external-net,router,allowed-address-pairs,extraroute,extra_dhcp_opt,provider,dvr
...

[service_available]
neutron = true
```


### create tenant and  users for tests
`keystone tenant-create --name tempest1`   
`keystone user-create --name tempest1 --tenant-id $demoTenantID1 --pass passw0rd --enabled True`    
`keystone tenant-create --name tempest2`     
`keystone user-create --name tempest2 --tenant-id $demoTenantID2 --pass passw0rd --enabled True`    

### run tests for neutron ports
`cd tempest/api/network`	      
`nosetests -v test_ports.py`     
