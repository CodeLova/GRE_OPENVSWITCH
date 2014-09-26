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
iconfig br1 1.1.1.1 netmask 255.255.255.0
```

- Tạo gre0 và set gre0 vào br1:
```
 ovs-vsctl add-port br2 gre0 -- set interface gre0 type=gre options:remote_ip=172.16.69.41
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
 




























