Playbook Name
=========

A collection of Ansible modules, created by Sirius, that automate configuration and operational tasks on Palo Alto Networks Next Generation Firewalls.

Requirements
------------

The PanOS Ansible collection is required for successful playbook execution.
You can install install the Palo Alto Networks Ansible Galaxy collection one of two ways:
1. ansible-galaxy collection install -r requirements.yml
or
2. ansible-galaxy collection install paloaltonetworks.panos

The following Ansible version,Python version and Python Modules are requirements on the host executing the playbooks.  You can use pip, which is part of Python, to install Ansible and all of the modules.

ansible >=2.9
python >= 3.7
boto
boto3
pandevice
pan-python
xmltodict
lxml

To execute all playbooks the following modules are required which are included in the paloaltonetworks.panos collection.

AWS and PANOS modules required:
panos_ike_crypto_profile
panos_ike_gateway
panos_ipsec_profile
panos_tunnel
panos_ipsec_tunnel
panos_bgp
panos_bgp_peer
panos_bgp_peer_group
panos_bgp_redistribute
panos_redistribution
panos_loadcfg
panos_import
panos_mgtconfig
panos_snmp_profile
panos_administrator
panos_snmp_v2c_server
panos_address_object
panos_security_rule
panos_object_facts
panos_security_rule_facts
panos_nat_rule_facts
panos_address_groups
panos_security_rules
panos_nat_rules
panos_object_facts
panos_zone_facts
panos_facts
panos_virtual_router_facts
ec2_vpc_vgw
ec2_customer_gateway
ec2_vpc_vpn
ec2_vpc_route_table
ec2_vpc_vpn_info
ec2_vpc_route_table_info


Generic Ansible modules used:
xml
set_fact
debug

The AWS Ansible modules use previously set environmental variables instead of stored variables. For command line usage these variables must be set.
export AWS_ACCESS_KEY_ID=ACCESSKEY
export AWS_SECRET_ACCESS_KEY=SECRETKEY


Role Variables
--------------

Variables files are located inside of roles in vars/main.yml

except for:
secrets.yml - Palo Alto Firewall admin access information. Should be secured for production use with ansible-vault or other encryption mechanism

Ensure that the path set for configuration backup is writeable by awx user otherwise backup and job will fail This is set by backup_path variable in backup_restore_vars.yml


Dependencies
------------

No dependencies other than requirements listed above.

Playbook Listing
----------------

add_rules_mass.yml -> Add list of security rules from file. Also adds address objects
  vars_files:
    - "vars/secrets.yml"
    - "vars/common_vars.yml"
  roles:
    - role: PaloAltoNetworks.paloaltonetworks
    - palo_remove_address_object
    
configure_bgp.yml -> Configures BGP for a Palo Alto firewall
    roles:
    - role: PaloAltoNetworks.paloaltonetworks
    - palo_configure_bgp
    
configure_bgp_advanced.yml -> Configures advanced BGP with multiple peers and/or advanced features and filtering.
  vars_files:
    - "vars/common_vars.yml"
    - "vars/secrets.yml"
    - "vars/advanced_bgp_vars.yml"
  roles:
    - role: PaloAltoNetworks.paloaltonetworks
    - palo_backup_restore_config
    
configure_cloud_vpn.yml - > Configures AWS and/or a Palo Alto firewall for VPN connectivity to AWS
  vars_files:
    - "vars/aws_vars.yml"
    - "vars/bgp_vars.yml"
    - "vars/common_vars.yml"
    - "vars/palo_vars.yml"
    - "vars/secrets.yml"
  roles:
    - role: PaloAltoNetworks.paloaltonetworks
    - aws_initial_checks
    - { role: aws_vpn_setup, when: aws_vpn_state | length == 0 }
    - aws_vpn_info
    - palo_site_site_vpn
    - { role: palo_configure_bgp, when: bgp == 'true' } 
    - palo_add_rules
    
Note: If Site-to-Site VPN is not previously built the playbook will pause for several minutes while the VPN goes from pending to active within AWS. If it times out wait for AWS to complete activating tunnel and rerun.

palo_add_logging -> Adds HTTP, SNMP, Syslog, Email, logging profile to all Palo Alto security rules or rules which contain address object.
  vars_files:
    - "vars/secrets.yml"
    - "vars/palo_logging_vars.yml"
  roles:
    - role: PaloAltoNetworks.paloaltonetworks
    - palo_add_logging

palo_facts.yml -> Collects and displays various facts from Palo Alto firewalls

  vars_files:
    - "vars/palo_vars.yml"
    - "vars/secrets.yml"
  roles:
    - role: PaloAltoNetworks.paloaltonetworks
    - palo_facts
    
palo_initial_setup.yml -> Completes initial configuration of Palo Alto firewall (physical or virtual)

  vars_files:
    - "vars/palo_initial_vars.yml"
    - "vars/secrets.yml"
  roles:
    - role: PaloAltoNetworks.paloaltonetworks
    - initial_palo_setup

palo_backup_restore_configuration.yml -> Backup and/or restore configuration 

   vars_files:
    - "vars/backup_restore_vars.yml"
    - "vars/secrets.yml"
  roles:
    - palo_backup_restore

License
-------

Content used by license from Sirius for internal Sirius and customer use.
BSD

Author Information
------------------

Written by Sirius Engineering

Rich Mallory
Sirius
Senior Solutions Architect
rich.mallory@siriuscom.com

Versions
--------
1.0 Initial Upload 6-16-2020
1.1 Added Logging Playbook (see playbook listing above) 6-24-2020
1.2 Added PaloAltoNetworks.paloaltonetworks role for Ansible Automation Platform 7-9-2020
1.3 Added Backup Restore Capability 7-10-2020 Note: setting backup_configuration to true in backup_restore_vars.yml will backup configuration using any playbook before changes
1.4 conversion ansible > version 2.9 use of collections FQCN in roles, seperation of backup/restore from other roles, movement of vars into roles to follow best practice of modular playbook design
