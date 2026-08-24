# devops-repeat

# Install AlmaLinux
Source:
- https://portal.cloud.hashicorp.com/vagrant/discover/almalinux/9

I have downloaded the file and extracted it to find its content
```
user@linux:~/Downloads$ find . -maxdepth 1 -type f -mtime -1
./1deadf3f-94b9-11f1-bdb9-0a8d6f3931c9
user@linux:~/Downloads$ mkdir extract
user@linux:~/Downloads$ find . -maxdepth 1 -type f -mtime -1|xargs -n1 -I{} cp {} ./extract/
xargs: warning: options --max-args and --replace/-I/-i are mutually exclusive, ignoring previous --max-args value
user@linux:~/Downloads$ cd extract/
user@linux:~/Downloads/extract$ tar -xvf *
Vagrantfile
box_0.img
metadata.json
user@linux:~/Downloads/extract$ file box_0.img 
box_0.img: QEMU QCOW Image (v3), 20971520000 bytes (v3), 20971520000 bytes
user@linux:~/Downloads/extract$ jq . metadata.json 
{
  "architecture": "amd64",
  "disks": [
    {
      "format": "qcow2",
      "path": "box_0.img"
    }
  ],
  "provider": "libvirt"
}
user@linux:~/Downloads/extract$ cat Vagrantfile 

# The contents below were provided by the Packer Vagrant post-processor

Vagrant.configure("2") do |config|
  config.vm.provider :libvirt do |libvirt|
    libvirt.driver = "kvm"
  end
end


# The contents below (if any) are custom contents provided by the
# Packer template during image build.
Vagrant.configure('2') do |config|
  config.vm.synced_folder '.', '/vagrant', type: 'rsync'
end
```

Set the root password befor install process
```
user@linux:~/Downloads/ext$ mv box_0.img alma9-disk.img 
user@linux:~/Downloads/ext$ mv alma9-disk.img ~/qcow2s/
user@linux:~/Downloads/ext$ virt-customize -a ~/qcow2s/alma9-disk.img --root-password password:toor
[   0.0] Examining the guest ...
[   9.4] Setting a random seed
[   9.4] Setting the machine ID in /etc/machine-id
[   9.4] Setting passwords
[  10.4] SELinux relabelling
[  11.9] Finishing off
```


# Not Needed anymore
After that, since we only need the `*.img` file, we need to convert it into qcow2 to be applied on `kvm` environment
```
user@linux:~/Downloads/extract$ qemu-img convert  -f raw -O qcow2  box_0.img alma9-disk.qcow2
user@linux:~/Downloads/extract$ ls *.qcow2
alma9-disk.qcow2
user@linux:~/Downloads/extract$ ls *.qcow2|xargs -n1 -I{} du -sh {} 2>/dev/null 
529M    alma9-disk.qcow2
```

### Install the VM

```
user@linux:~$ virt-install --name alma9-vm --memory 4048 --vcpus 2 --disk path=/home/user/qcow2s/alma9-disk.qcow2 --import --os-variant almalinux9 --network bridge=virbr0,model=virtio
```

### To remove the VM
```
user@linux:~$ virsh shutdown alma9-vm
user@linux:~$ virsh undefine alma9-vm --remove-all-storage

```

#### In case of NVRAM uses
```
user@linux:~$ virsh undefine ubuntu-noble-vm --remove-all-storage
error: Failed to undefine domain 'ubuntu-noble-vm'
error: Requested operation is not valid: cannot undefine domain with nvram

user@linux:~$ virsh undefine --nvram ubuntu-noble-vm
Domain 'ubuntu-noble-vm' has been undefined

```
