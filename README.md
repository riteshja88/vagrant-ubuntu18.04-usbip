# vagrant-ubuntu18.04-usbip

## Run below command
```
vagrant up
vagrant ssh
vagrant destroy
```

On client
```
sshpass -p vagrant ssh vagrant@192.168.1.91 sudo ./restart.sh ;sudo rmmod vhci-hcd;sudo modprobe vhci-hcd; sudo usbip attach -r 192.168.1.91 -b 2-1
```
