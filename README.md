# azurectfss

Make sure to create your own secrets.yaml with the following values:

```yaml
workstationpass: "InsertYourComplexPasswordForRDPHere"
remoteallowedip: "ip.ad.dr.ess"
```

## Known issues:
- Don't currently have a task to create the inbound NAT via loadbalancer.
- No obvious way to enable winRM so we can manage Windows Base with Ansible
  - Others have used CustomScriptExtension.. but that is failing to execute for me.
- Created playbook to customize workstation (FF/Burp/Zap/etc)
  - Still needs a task to setup proxy
- Haven't yet created playbook to include securityshepherd-ansible role 
  - Achieve by using dns-name, like workstation base image..
