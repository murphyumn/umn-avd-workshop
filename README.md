# University of Minnesota AVD Workshop

## ATD Dual Datacenter Topology - MLAG Multihoming

The ATD lab is used to create the L3LS EVPN/VXLAN Dual Data Center topology below.

![Topologies](images/UMN-ATD-Topology.png)

## Lap Prep - Customized for UoMN Workshop

**STEP #1** - Update Lab to AVD 5.7.3

```bash
pip install "pyavd[ansible]==5.7.3"
ansible-galaxy collection install arista.avd:==5.7.3

# Verify AVD version
ansible-galaxy collection list
```

**STEP #2** - Reset nodes to undefined container in CVP - ***Verify CVP is running before executing this command***

```bash
make reset_to_undefined
```

**STEP #3** - Apply base configs to Lab nodes

```bash
make preplab
```

**STEP #4** - (*Optional*) - Export CVP Service Account token to env variable CV_TOKEN

If you wish to push configuration to nodes with CVP, you will need to create a Service Account Token and insert it below.

Follow the instructions on the [CloudVison documentation website](https://www.arista.io/help/2024.3/articles/c2V0dGluZ3MuQWxsLmFjY2Vzc0NvbnRyb2wuc2VydmljZUFjY291bnRz#service-accounts) to create a Service Account and generate a Service Account Token. The token is used to connect to the CloudVision APIs when using the arista.avd.cv_deploy role.

```bash
export CV_TOKEN=<insert token>
```

## Build and Deploy AOC Fabric Configs

```bash
# Build AOC Fabric Configs
make AOC-build

# Deploy AOC Fabric Configs via CVP
make AOC-CVP-deploy

# Deploy AOC Fabric Configs via eAPI (direct to switches)
make AOC-deploy

# Validate AOC fabric
make AOC-validate
```

## Build and Deploy TelecomB Fabric Configs

```bash
# Build TelecomB Fabric Configs
make TelecomB-build

# Deploy TelecomB Fabric Configs via CVP
make TelecomB-CVP-deploy

# Deploy TelecomB Fabric Configs via eAPI (direct to switches)
make TelecomB-deploy

# Validate TelecomB fabric
make TelecomB-validate
```


## Follow the Lab Guide Instructions

The instructions to build and deploy this L3LS Multi-site topology are located in the Lab Guide **[here](https://labguides.testdrive.arista.com/2025.3/automation/ci_avd_l3ls/overview/)**.

***Note:***

While the documentation references Site1 and Site2, our custom lab environment will use the following mappings:

- Site1 ---> AOC
- Site2 ---> TelecomB


## Add P2P Links for DCI connectivity for Site 1 and 2

Enable the l3_edge dictionary (shown below) by uncommenting and modifiying lines (77-97) from the **global_vars/global_dc_vars.yml**:

```bash
# L3 Edge port definitions. This can be any port in the entire Fabric, where IP interfaces are defined.
l3_edge:
  # Define a new IP pool that will be used to assign IP addresses to L3 Edge interfaces.
  p2p_links_ip_pools:
    - name: AOC_to_TelecomB_IP_pool
      ipv4_pool: 172.16.255.0/24
  # Define a new link profile which will match the IP pool, the used ASNs and include the defined interface into underlay routing
  p2p_links_profiles:
    - name: AOC_to_TelecomB_profile
      ip_pool: AOC_to_TelecomB_IP_pool
      as: [ 65103, 65203 ]
      include_in_underlay_protocol: true
  # Define each P2P L3 link and link the nodes, the interfaces and the profile used.
  p2p_links:
    - id: 1
      nodes: [ AOC-DB-1, TelecomB-DB-1 ]
      interfaces: [ Ethernet4, Ethernet4 ]
      profile: AOC_to_TelecomB_profile
    - id: 2
      nodes: [ AOC-DB-2, TelecomB-DB-2 ]
      interfaces: [ Ethernet5, Ethernet5 ]
      profile: AOC_to_TelecomB_profile
```

## Hostname Mappings

| Original Hostname | Lab Hostname      | Management0   |
|-------------------|-------------------|---------------|
| s1-brdr1          | AOC-DB-1          | 192.168.0.100 |
| s1-brdr2          | AOC-DB-2          | 192.168.0.101 |
| s1-core1          | AOC-CORE-1        | 192.168.0.102 |
| s1-core2          | AOC-CORE-2        | 192.168.0.103 |
| s1-host1          | AOC-HOST-1        | 192.168.0.16  |
| s1-host2          | AOC-HOST-2        | 192.168.0.17  |
| s1-leaf1          | AOC-DV-1          | 192.168.0.12  |
| s1-leaf2          | AOC-DV-2          | 192.168.0.13  |
| s1-leaf3          | AOC-DL-1          | 192.168.0.14  |
| s1-leaf4          | AOC-DL-2          | 192.168.0.15  |
| s1-spine1         | AOC-DS-1          | 192.168.0.10  |
| s1-spine2         | AOC-DS-2          | 192.168.0.11  |
| s2-brdr1          | TelecomB-DB-1     | 192.168.0.200 |
| s2-brdr2          | TelecomB-DB-2     | 192.168.0.201 |
| s2-core1          | TelecomB-CORE-1   | 192.168.0.202 |
| s2-core2          | TelecomB-CORE-2   | 192.168.0.203 |
| s2-host1          | TelecomB-HOST-1   | 192.168.0.26  |
| s2-host2          | TelecomB-HOST-2   | 192.168.0.27  |
| s2-leaf1          | TelecomB-DV-1     | 192.168.0.22  |
| s2-leaf2          | TelecomB-DV-2     | 192.168.0.23  |
| s2-leaf3          | TelecomB-DL-1     | 192.168.0.24  |
| s2-leaf4          | TelecomB-DL-2     | 192.168.0.25  |
| s2-spine1         | TelecomB-DS-1     | 192.168.0.20  |
| s2-spine2         | TelecomB-DS-2     | 192.168.0.21  |