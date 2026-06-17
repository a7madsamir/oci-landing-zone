# Execution Flow - One-OE Hub B

## Recommended flow for this repo:

  1. oneoe_governance.json
  2. oneoe_iam.json
  3. oneoe_observability_cis1_pre.json
  4. oneoe_security_cis1_pre.json
  5. oneoe_network_hub_b_pre.json
  6. oneoe_network_hub_b.json
  7. oneoe_observability_cis1.json
  8. oneoe_security_cis1.json

## For Hub B, the important split is:

  - _pre files create baseline controls without depending heavily on network resources.
  - Final files add items that need created resources, such as VCN/subnet flow logs and final Security Zone targets.

## Why this order

  **governance** first because IAM and policies depend on tag namespaces and governance primitives.

  **iam** second because all later files reference compartments, groups, policies, and service permissions.

  **observability_cis1_pre** early because it creates topics, events, alarms, and service connectors. These do not need VCNs to exist.

  **security_cis1_pre** early because it enables baseline Cloud Guard, Vulnerability Scanning, and initial Security Zone recipes/targets.

  **network_hub_b_pre** next because Hub B needs the first pass to create the VCNs, DRG, firewall, LB, subnets, NSGs, and route
  structures before you can know firewall private IP OCIDs.

  **network_hub_b** after that, once you replace the placeholder firewall private IP OCIDs in the final route tables.

  **observability_cis1** after network because it includes **logging_configuration** for VCN/subnet flow logs. Those targets must already
  exist.

  **security_cis1** last because it adds the final Security Zone targets. This avoids Security Zone restrictions blocking initial
  resource creation while you are still building the landing zone.

  Best practice in OCI Resource Manager: apply incrementally and review the plan each time. Do not keep both
  **oneoe_network_hub_b_pre.json** and **oneoe_network_hub_b.json** active as separate competing network definitions unless the orchestrator
  expects that pattern. Treat final files as the replacement/complete version after the pre-stage has served its purpose.

  Also, if you remove a previously applied file from the stack input, Terraform may plan to destroy resources from that file if they
  are still tracked in state. So use file removal carefully and always inspect the plan before applying.