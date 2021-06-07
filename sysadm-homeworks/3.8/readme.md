1. сервис требует persistence, т.е. удерживание пользователя на одном из серверов в течении заданного промежутка времени
2. vagrantfile
   # -*- mode: ruby -*-
# vi: set ft=ruby :

boxes = {
  'user' => '11',
  'Pbalancer' => '21',
  'Sbalancer' => '22',
  'server1' => '31',
  'server2' => '32',
}

Vagrant.configure("2") do |config|
  config.vm.network "private_network", virtualbox__intnet: true, auto_config: false
  config.vm.box = "bento/ubuntu-20.04"

  boxes.each do |k, v|
    config.vm.define k do |node|
      node.vm.provision "shell" do |s|
        s.inline = "hostname $1;"\
          "ip addr add $2 dev eth1;"\
          "ip link set dev eth1 up;"\
          "sudo apt-get update;"\
          "apt-get -y install nginx;"
        s.args = [k, "172.28.128.#{v}/24"]
      end
    end
  end

end

   root@Pbalancer:/home/vagrant# apt-get install keepalived

   root@Pbalancer:/home/vagrant# mcedit /etc/keepalived/keepalived.conf
vrrp_instance balancer_ip1 {
    state MASTER
    interface eth1
    virtual_router_id 20
    priority 100
    virtual_ipaddress {
        172.28.128.20
    }
}

   root@Pbalancer:/home/vagrant# systemctl enable keepalived

   root@Pbalancer:/home/vagrant# systemctl start keepalived

   root@Pbalancer:/home/vagrant# journalctl -u keepalived

   root@Sbalancer:/home/vagrant# apt-get install keepalived

   root@Sbalancer:/home/vagrant# mcedit /etc/keepalived/keepalived.conf
vrrp_instance balancer_ip1 {
    state BACKUP
    interface eth1
    virtual_router_id 20
    priority 99
    virtual_ipaddress {
        172.28.128.20
    }
}

   root@Sbalancer:/home/vagrant# systemctl enable keepalived

   root@Sbalancer:/home/vagrant# systemctl start keepalived

   root@Sbalancer:/home/vagrant# journalctl -u keepalived

   root@Pbalancer:/home/vagrant# /etc/keepalived/keepalived.conf
vrrp_instance balancer_ip1 {
    state MASTER
    interface eth1
    virtual_router_id 20
    priority 100
    virtual_ipaddress {
        172.28.128.20
    }
}

virtual_server 172.28.128.20 53 {
    protocol UDP
    delay_loop 10
    lvs_sched rr
    lvs_method NAT

    real_server 172.28.128.31 53 {
        DNS_CHECK {
            type txt
            name health.load.balance
        }
    }

    real_server 172.28.128.32 53 {
        DNS_CHECK {
            type txt
            name health.load.balance
        }
    }
}

virtual_server 172.28.128.20 53 {
    protocol TCP
    delay_loop 10
    lvs_sched rr
    lvs_method NAT

    real_server 172.28.128.31 53 {
        TCP_CHECK {
            connect_timeout 3
        }
    }

    real_server 172.28.128.32 53 {
        TCP_CHECK {
            connect_timeout 3
        }
    }
}

root@Sbalancer:/home/vagrant# /etc/keepalived/keepalived.conf
vrrp_instance balancer_ip1 {
    state BACKUP
    interface eth1
    virtual_router_id 20
    priority 99
    virtual_ipaddress {
        172.28.128.20
    }
}

virtual_server 172.28.128.20 53 {
    protocol UDP
    delay_loop 10
    lvs_sched rr
    lvs_method NAT

    real_server 172.28.128.31 53 {
        DNS_CHECK {
            type txt
            name health.load.balance
        }
    }

    real_server 172.28.128.32 53 {
        DNS_CHECK {
            type txt
            name health.load.balance
        }
    }
}

virtual_server 172.28.128.20 53 {
    protocol TCP
    delay_loop 10
    lvs_sched rr
    lvs_method NAT

    real_server 172.28.128.31 53 {
        TCP_CHECK {
            connect_timeout 3
        }
    }

    real_server 172.28.128.32 53 {
        TCP_CHECK {
            connect_timeout 3
        }
    }
}

3. Я думаю будет достаточно 3 VIP