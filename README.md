# VLAN-LACP
configuration VLAN, LACP

### Задание:
в Office1 в тестовой подсети появляется сервера с доп интерфейсами и адресами
в internal сети testLAN: 
- testClient1 - 10.10.10.254
- testClient2 - 10.10.10.254
- testServer1- 10.10.10.1 
- testServer2- 10.10.10.1

Развести вланами:
testClient1 <-> testServer1
testClient2 <-> testServer2

Между centralRouter и inetRouter "пробросить" 2 линка (общая inernal сеть) и объединить их в бонд, проверить работу c отключением интерфейсов

### Решение:
#### Vagrantfile для 7 ВМ
mkdir /home/maria/homework/my_vagrant_VLAN

cd /home/maria/homework/my_vagrant_VLAN

vagrant up

vagrant status:

inetRouter                running (virtualbox)

centralRouter             running (virtualbox)

office1Router             running (virtualbox)

testClient1               running (virtualbox)

testServer1               running (virtualbox)

testClient2               running (virtualbox)

testServer2               running (virtualbox)


#### Настройка VLAN на хостах
#RedHatSystems

#testClient1:

создаем файл /etc/sysconfig/network-scripts/ifcfg-vlan1

systemctl restart NetworkManager             #root:vagrant

ip a                                         #проверим

#testServer1:

создаем файл /etc/sysconfig/network-scripts/ifcfg-vlan1

systemctl restart NetworkManager             #root:vagrant

ip a                                         #проверим

ping 10.10.10.254                            #проходит

#Debian

#testClient2:

создаем файл etc/netplan/50-cloud-init.yaml

sudo netplan generate                         #проверка синтаксиса

sudo netplan try

ip a

#testServer2:

создаем файл etc/netplan/50-cloud-init.yaml

sudo netplan generate                         #проверка синтаксиса

sudo netplan try

ip a

ping 10.10.10.254

sudo tcpdump -i enp0s8 -e -n vlan 2           #посмотреть пинг на testClient2 с vlan тегами

#### Настройка LACP между хостами inetRouter и centralRouter

проделать всё на обоих хостах inetRouter (192.168.255.1), centralRouter (192.168.255.2)

создадим файлы /etc/sysconfig/network-scripts/ifcfg-eth1; /etc/sysconfig/network-scripts/ifcfg-eth2; /etc/sysconfig/network-scripts/ifcfg-bond0

sudo reboot

ip a

ping 192.168.255.1                                     #c 192.168.255.2

ip link set down eth1                                  #на 192.168.255.1 

убеждаемся что пинг проходит успешно 

cat /proc/net/bonding/bond0   #полезная информация















