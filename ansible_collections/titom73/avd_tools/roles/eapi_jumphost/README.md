# Expose eAPI via IPTABLES

__inetsix.avd_tools.eapi_jumphost__ is a role that build an iptables script to expose eAPI port of all Arista nodes on a Jumphost by using destination NAT.

It takes exposed ports from `ansible_port` configured in your inventory as it is ports you will use to push your changes.

## Requirements

No specific requirement to install other than AVD.

## Role variables

- `eos_group`: Name of the group to look for Arista EOS devices. (default: `eos_devices`)
- `incoming_interface`: Interface name from where requests will come. (default: `ens3`)
- `eos_oob_interface`: Interface connected to Arista Out Of Band management. (default: `ens4`)
- `eapi_port`: Port used by eAPI on Arista switch. (default: `443`)
- `iptables_script_name`: Name of the generated script. (default: `{{ inventory_dir }}/eapi.iptables.sh`)

## Example

### Inventory file

```yaml
---
eos_devices:
  children:
    tooling:

tooling:
  hosts:
    my_node:
      ansible_port: 8001
      ansible_host: 10.126.51.251
      type: unset
```

### Script output

```bash
#!/bin/bash

echo "Jumphost Remote access configuration"

_EAPI_PORT='443'
_SRC_IF='ens3'
_DST_IF='ens4'

echo '* Activate kernel routing'
sysctl -w net.ipv4.ip_forward=1

echo '* Flush Current IPTables settings'
iptables --flush
iptables --delete-chain
iptables --table nat --flush
iptables --table nat --delete-chain

echo '* Activate default forwarding'

iptables -P FORWARD ACCEPT
iptables -P INPUT ACCEPT
iptables -P OUTPUT ACCEPT

echo '* Activate masquerading'

iptables -t nat -A POSTROUTING -o ens3 -j MASQUERADE
iptables -t nat -A POSTROUTING -o ens4 -j MASQUERADE

echo '* Activate eAPI forwarding with base port 800x'

iptables -t nat -A PREROUTING -p tcp -i ens3 --dport  8001 -j DNAT --to-destination 10.126.51.251:443

iptables -A FORWARD -p tcp -d 10.73.254.0/24 --dport ${_EAPI_PORT} -m state --state NEW,ESTABLISHED,RELATED -j ACCEPT

echo "-> Configuration done"
```
