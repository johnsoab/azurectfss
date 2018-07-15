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
- Setup Azure SPN with contributor rights to a new ResourceGroup named "CTFRG"
- Run ctf-infra.yml playbook with vault secret: ansible-playbook ./ctf-infra.yml --ask-vault-pass
- Once complete, rerun it again (some provisioning glitch with ctf-wrk-nic.. doesnt assign NSG properly sometimes), to ensure its in good shape.
	While Windows Instance is booting up (takes a while), lets provision SS App:
	Switch to securityshepherd-ansible, and run 

1. Fetch azurectfss from https://github.com/johnsoab/azurectfss
2. Fetch securityshepherd-ansible role from https://github.com/johnsoab/securityshepherd-ansible
3. Provision infrastructure: ansible-playbook ./ctfdeploy.yml  (You may need to start ssh-agent and load the ctfadmin key)
4. Should probably reboot ctfserver just for good measure, but then it should be up
5. Now that Windows VM should be up, lets ansibilize it: ansible-playbook ansibilize-windows.yml
6. Now that Windows is Ansiblized, we can push software and config:
 - This step should be turned into a role just like SS
 - We should build a dynamic inventory of workstations from CTFRG
 - Then we should just apply role to all workstations.
 - Currently: update hosts (which likely says ctfwrklb.. replace with ctfwrk10101): ansible-playbook ./workstation.yml
7. Some manual steps to add BurpCA.. 
	a. RDP into newly provisioned host. 
	b. Start Burp, Click thru new project dialog. Disable Interception.
	c. Open Firefox
	d. Navigate to http://burp/cert and download cacert.pem
	e. Go to Tools->Options->Security->View Certificates. Import CA from Downloads, allow it to identify websites.
	f. Set default homepage to https://10.0.0.4 (or whatever ctfserver is).
	g. Verify all is working.
	h. DONT DO: 
		- Generalize image: C:\Windows\System32\Sysprep\Sysprep.exe /oobe /generalize /shutdown /quiet
		- While this would be the normal step.. it wipes our burp trust, as well as burp install. So.. Perhaps we don't bother generalizing and capturing. We just provision. The windows update step is ugly, but deal with it.
		
	
	
### Future states
	Build ctfwrkbase
	Generalize/Capture image
	Create ansible loop to provision variable number of instances from image with unique passwords
	

## Known issues:
- Need to massively clean up and include all the roles/playbooks in easy to consume format - this is sprawled
- Lacks an automated task to add burp CA into firefox certstore (I believe is possible, just need time)
- Need to setup dynamic inventory so we can provision infra via localhost, then iterate on vm guest tasks
- Maybe create a simple stop/start playbook for ease of shutting down environment (easier with dynamic inventory)

