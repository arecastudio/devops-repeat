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
