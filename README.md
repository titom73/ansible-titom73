# Personal toolset for Arista AVD

List of ansible roles & modules to integrate [__Arista AVD__](https://www.avd.sh/en/devel/) in various environments

## List of available roles

- [__EOS Designs to Containerlab__](ansible_collections/titom73/avd_tools/roles/avd_to_clab): Generate [containerlab](https://containerlab.srlinux.dev/) topology from Arista AVD eos_designs.
- [__iptables script to expose eAPI via a Jumphost__](ansible_collections/titom73/avd_tools/roles/eapi_jumphost): Role to generate an iptables script that expose Arista eAPI ports by using D-NAT.

## License

Project is published under [Apache 2.0 License](LICENSE)

> This repository is personal and not directly linked to any AVD effort.
