# azurectfss

Make sure to create your own secrets.yaml with the following values:

```yaml
workstationpass: "InsertYourComplexPasswordForRDPHere"
remoteallowedip: "ip.ad.dr.ess"
```
## Basic Instructions
### Provision SS CTF

### Prereqs: 
- Install ansible via pip
- Install ansible[azure] via pip
- Setup Azure SPN or use an account with contributor rights to a new ResourceGroup named "CTFRG"
	Switch to securityshepherd-ansible, and run 

1. Fetch azurectfss from https://github.com/johnsoab/azurectfss
  1. Run ctf-infra.yml playbook with vault secret: ansible-playbook ./ctf-infra.yml --ask-vault-pass
  1. Once complete, rerun it again (some provisioning glitch with ctf-wrk-nic.. doesnt assign NSG properly sometimes), to ensure its in good shape.
  1. While Windows Instance is booting up (takes a while), lets provision SS App:
1. Fetch securityshepherd-ansible role from https://github.com/johnsoab/securityshepherd-ansible
1. Provision infrastructure: ansible-playbook ./ctfdeploy.yml  (You may need to start ssh-agent and load the ctfadmin key)
1. Should probably reboot ctfserver just for good measure, but then it should be up
1. Now that Windows is finished updating (hopefully), and is already Ansiblized, we can push software and config:
1. Update hosts (which likely says ctfwrklb.. replace with ctfwrk10101) 
1. Fetch the ctfworkstation role: https://github.com/johnsoab/ctfworkstation
1. Apply ctf-workstation roles to your hosts: ansible-playbook ./workstation.yml
1. Some manual steps to add BurpCA.. 
 1. RDP into newly provisioned host. 
 1. Start Burp, Click thru new project dialog. Disable Interception.
 1. Open Firefox
 1. Navigate to http://burp/cert and download cacert.pem
 1. Go to Tools->Options->Security->View Certificates. Import CA from Downloads, allow it to identify websites.
 1. Set default homepage to https://10.0.0.4 (or whatever ctfserver is).
 1. Verify all is working.
		
	
	
## Known issues:
- Update ansible loop to provision variable number of instances from image with unique passwords
- Need to massively clean up and include all the roles/playbooks in easy to consume format - this is sprawled and inconsistent
- Lacks an automated task to add burp CA into firefox certstore (I believe is possible, just need time)
- Need to setup dynamic inventory so we can provision infra via localhost, then iterate on vm guest tasks
- Maybe create a simple stop/start playbook for ease of shutting down environment (easier with dynamic inventory)
- DONT DO: 
  - Generalize image: C:\Windows\System32\Sysprep\Sysprep.exe /oobe /generalize /shutdown /quiet
  - While this would be the normal step.. it wipes our burp trust, as well as burp install. So.. Perhaps we don't bother generalizing and capturing. We just provision. The windows update step is ugly, but deal with it.

