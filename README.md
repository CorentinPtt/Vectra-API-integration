# Vectra-API-integration
API integration project between Vectra AI tool and Sonicwall type firewall


# **Sonicwall to Vectra API integration for augmented subnet isolation**

## _Objective_

The objective of the development of this script was to allow some additional options to automatically isolate various equipment that the Vectra tool would flag as suspicious. 
By adding a firewall to the tool package, we have developed a script allowing the firewall to give or remove access to subnets, to the internet, to the flagged subnets and / or equipment. 

---
## Stages : 
-	Input from Vectra tool indicating levels of threat certainty
-	Extraction of necessary data from the flagged hosts (ip, name)
-	Verification of existence of group containing the flagged hosts and of proper configuration : correct names and Ip’s of all the flagged hosts
-	If non-existent group, creation of rule group containing the flagged hosts 
-	Creation of rule to remove access to network and internet to the flagged hosts or subnet entered in rule group
-	Commitment of the changes to the firewall 

---
## Additional Information 
-	The script needs to have a set threat level which will provoke the rule making (need to define which limit for the threat level) 
-	For the script to work correctly, we need to make sure it managed to log into the firewall API in configuration mode (otherwise rule and group creation will not be possible)
-	Every change, deletion, creation of any sort must be committed by the commit function before exiting the script for it to be valid and saved into the firewall
-	The uri and token for the creation of the connection to the Vectra tool need to be adapted to the version on the company network 
-	When posting new ipv4 policy, uuid is generated automatically by firewall and is an incrementation of previous creations -> additional testing to find if limit will be problematic
-	Same to be said with creation of new ipv4 group

---

This declaration of variables fills in the data necessary for the login to the sonicwall api. It needs to be adapted to your personal network for it to function properly. 
**The ip address as well as the port need to be chosen accordingly**
```python
# Firewall IP/port for SonicOS API
#fw = input("Enter the target firewall URL and port (Example: https://192.168.0.3:450): ")
#admin_username = input("Enter the management username: ")
#admin_password = getpass.getpass("Enter your administrator password: ")

fw = "https://192.168.1.100:443"
admin_username = "admin"
admin_password = "password"
```

[Link to an image or github doc]('https://google.com')

--- 
## Step by Step process

#### Line 36
- uri and token for Vectra integration need to be adapted to your current tool version
```python
def main():

	# Create the session
	session = create_admin_session(fw, admin_username, admin_password)

	uri = "demo.vectra.io"
	token = "4fe6f3a9defadebe696725b074009f500680e2e0"
```

#### Line 309
- Values stored in _get_scored_hosts_ needs to be adapted to client need
```pyhton 
vectra = Vectra(uri, token)
hosts = vectra.get_scored_hosts(50,50)
for host in hosts:
	print("name: %s, ip: %s" % (hosts[host]["name"], hosts[host]["ip"])) 
```

#### Line 309
- Method will take in the hosts that are considered problematic
```python
vectra = Vectra(uri, token)
	hosts = vectra.get_scored_hosts(50,50)
	for host in hosts:
		print("name: %s, ip: %s" % (hosts[host]["name"], hosts[host]["ip"])) 
		
		print('\n\nCreating group for new threat\n\n')
		searchgroupname = (hosts[host]["name"])
		searchip = (hosts[host]["ip"])
		searchgrpname = (hosts[host]["name"])
		search_create_vectra_group(session, searchgroupname, searchip, searchgrpname)

		response = post_ipv4_policy(session, hosts[host]["name"])
		print ('\n\nNew Rule status : \n', response.json())

		exit()
```

- Will take you to new series of methods that will create new rules implementing the ip adresses of flagged hosts for the firewall to remove access to these flagged hosts.
