LAB GRE OPENVSWITCH

## I.Mục tiêu bài lab :

- Hiểu hoạt động của giao thức GRE.
- Máy vm trong Host1 có thế ping đến được máy vm trong Host2.

## II.Mô hình thực hiện :

<img src="">


## III. Kịch bản bài Lab :
 
  - Cài đặt Open vSwitch trên máy ảo VMWare Workstation.
  - Cài KVM trên cả hai máy và tạo mỗi máy 1 máy ảo.
  - Cấu hình GRE trên 2 máy để 2 VM có thế ping được đến nhau.

## IV. Quá trình thực hiện:

1. Trên  HOST1 :

- Cài  đặt gói KVM :
```
sudo apt-get install -y kvm libvirt-bin pm-utils
```
- Mặc định kvm sẽ tạo ra một card virbr0. Để tắt ta sử dụng:
```
virsh net-destroy default
virsh net-autostart --disable default

aptitude purge ebtables
```
- Cài đặt gói Openvswitch :
```
sudo apt-get install -y openvswitch-switch openvswitch-datapath-dkms
```
- Tạo brigre br0,br1
```
sudo ovs-vsctl add-br br-ex
sudo ovs-vsctl add-br br-ex

```
Thêm port eth0 vào br-ex
```
ovs-vsctl add-port br-ex eth0
```

- set ip cho br-ex :
```
iconfig eth0 0
ifconfig br0 172.16.69.40 netmask 255.255.255.0
route add default gw 172.16.69.1 dev br0

```
- set ip cho br1:
```
iconfig br1 1.1.1.1 netmask 255.255.255.0
```
- hoặc chỉnh file cấu hình /etc/network/interface 
```
auto br-ex
iface br-ex inet static
address 10.145.48.159
netmask 255.255.255.0
gateway 10.145.48.1
dns-nameservers 8.8.8.8
bridge_ports eth0
bridge_fd 9
bridge_hello 2
bridge_maxage 12
bridge_stp off
bridge_ports eth0

auto eth0
iface eth0 inet manual
#bridge_ports eth0
up ip link set dev $IFACE up
down ip link set dev $IFACE down
```

- Tạo gre0 và set gre0 vào br-int:
```
 ovs-vsctl add-port br-int gre0 -- set interface gre0 type=gre options:remote_ip=172.16.69.41
```
- Tạo script để cung cấp chuyển đổi card
```
#nano /etc/ovs-ifup
#!/bin/sh
switch='br-int'
/sbin/ifconfig $1 0.0.0.0 up
ovs-vsctl add-port ${switch} $1
```
- Cấp quyền cho file:
```
chmod +x /etc/ovs-ifup
```


- Tạo máy ảo bằng KVM :( nếu có thể lên tạo bang virtualbox)
```
kvm -m 512 -net nic,macaddr=12:42:52:CC:CC:15 -net tap,script=/etc/ovs-ifup cirros-0.3.0-i386-disk.img
```
- set ip cho vm1:
```
 ifconfig eth0 1.1.1.10 netmask 255.255.255.0
```

2. Trên HOST2:

- Cài  đặt gói KVM :
```
sudo apt-get install -y kvm libvirt-bin pm-utils
```

- Cài đặt gói Openvswitch :
```
sudo apt-get install -y openvswitch-switch openvswitch-datapath-dkms
```
- Tạo brigre br0,br1
```
sudo ovs-vsctl add-br br0
sudo ovs-vsctl add-br br0

```
Thêm port eth0 vào br0
```
ovs-vsctl add-port br0 eth0
```

- set ip cho br0 :
```
iconfig eth0 0
ifconfig br0 172.16.69.40 netmask 255.255.255.0
route add default gw 172.16.69.1 dev br0

```
- set ip cho br1:
```
iconfig br1 1.1.1.2 netmask 255.255.255.0
```

- Tạo gre0 và set gre0 vào br1:
```
 ovs-vsctl add-port br2 gre0 -- set interface gre0 type=gre options:remote_ip=172.16.69.40
```

- Tạo máy ảo bằng KVM :( nếu có thể lên tạo bang virtualbox)
```
kvm -m 512 -net nic,macaddr=12:42:52:CC:CC:15 -net tap,script=/etc/ovs-ifup cirros-0.3.0-i386-disk.img
```
- set ip cho vm1:
```
 ifconfig eth0 1.1.1.12 netmask 255.255.255.0
```

## V. Test kết quả:

- Từ VM1 trên HOST1 -> VM2 trên HOST2
```
ping 1.1.1.12
```
<img src="">
- Từ VM1 trên HOST2 -> VM2 trên HOST1
```
ping 1.1.1.11
```
<img src="">

 




























