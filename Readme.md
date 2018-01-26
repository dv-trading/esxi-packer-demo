# Pre-Setup instructions

## Esxi setup tasks

### Enable `ESXi Shell`

1. Log in to an esxi host system using the vSphere Client.
1. Select the host in the Inventory panel.
1. Click the Configuration tab and click Security Profile.
1. In the Services section, click Properties.
1. Select each of the following from this list:
    - `ESXi Shell`
    - `SSH`
    - `Direct Console UI`
1. Click `Options` and select `Start and stop with host`.
1. Click `Start` to enable the service.
1. Click `OK`

### Guest IP Hack

To enable detection of IP address by ARP packet inspection, the following command

``` sh
esxcli system settings advanced set -o /Net/GuestIPHack
```

### Open up vnc ports on host

The firewall blocks vnc ports by default, add a rule for packer

#### `/etc/vmware/firewall/service.xml`

``` xml
<service id="1000">
  <id>packer-vnc</id>
  <rule id="0000">
    <direction>inbound</direction>
    <protocol>tcp</protocol>
    <porttype>dst</porttype>
    <port>
      <begin>5900</begin>
      <end>6000</end>
    </port>
  </rule>
  <enabled>true</enabled>
  <required>true</required>
</service>
```

note: *you may need to change the permissions of the file to edit it*

## Configure `user.json`

Copy `default.user.json` to `user.json` and override the values as appropriate,
here's some documentation for the json properties

``` json
{
// The name of the vm and vmdks
    "name": "New VM",
// The network to connect to ethernet0
    "network": "network-local",
// Number of vcpus to allocate for the vm
    "numvcpus": "1",
// Amount of memory needed
    "memsize": "512",
// size of primary harddisk to create
    "disk_size": "40000",
// ssh username for logging into esxi
    "esxi_username": "user",
// ssh password for logging into esxi
    "esxi_password": "password",
// esxi host to log into
    "esxi_host": "<esxi hostname>",
// esxi datastore to use for cache and hard disk
    "esxi_datastore": "datastore"
}
```

## Execute `packer`

execute packer command, and watch output, when the install completes successfully,
the vm will be halted on esxi server and packer will exit successfully when the
installation completes.

``` sh
./provision
```