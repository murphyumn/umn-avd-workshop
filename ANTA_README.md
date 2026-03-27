# ANTA - Arista Network Testing Automation

## Install ANTA

```bash
pip install 'anta[cli]'
```

## Create ANTA inventory from Ansible Inventory

```bash
anta get from-ansible \
     --ansible-inventory sites/AOC/inventory.yml \
     --ansible-group AOC_FABRIC \
     --output sites/AOC/anta_inventory.yml
```

## Sample Test catalog - tests/all.yml

```bash
# tests/all.yml

anta.tests.system:
  - VerifyUptime:
      minimum: 86400
  - VerifyReloadCause:
  - VerifyNTP:

anta.tests.routing:
  generic:
    - VerifyRoutingTableEntry:
        vrf: default
        routes:
          - 0.0.0.0

anta.tests.services:
  - VerifyDNSLookup:
      domain_names:
        - www.cv-prod-us-central1-c.arista.io
```

## Run NRFU (Network Ready for Use) test

```bash
anta nrfu \
   -u arista -p <insert lab password> \
   -i sites/AOC/anta_inventory.yml \
   -c tests/all.yml \
   table

# target single device
anta nrfu -u arista -p <insert lab password> -i sites/AOC/anta_inventory.yml -c tests/all.yml  --device AOC-DB-1

# single test
anta nrfu -u arista -p <insert lab password> -i sites/AOC/anta_inventory.yml -c tests/all.yml  --test VerifyNTP
```