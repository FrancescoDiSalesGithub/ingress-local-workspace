# ingress-local-workspace
how to use kubernetes' ingress in a local environment

# Prerequisites

* a vm with kubernetes installed (suggested: microk8s on ubuntu)
* a vm with bind9 installed (suggested: ubuntu)
* a vm that is external to kubernetes environment
* the three vms are in bridged mode
* the vm with kubernetes has microk8s installed

# How to setup DNS server

Install bind9 by running the following command:
```
sudo apt install -y bind9
```

Now for easy configuration copy the files in this project under the DNS folder at the following paths:
* named.conf.local -> /etc/bind/
* named.conf.options -> /etc/bind/

for your dns record run firstly this command:
```
sudo mkdir /etc/bind/zones
```
move **your-dns-console.db** to **/etc/bind/zones/**. Edit now **your-dns-console.db** for your custom domain name. To do so please open the file and replace  **yournet.net.** to the name of your personal domain.
Finally edit **named.conf.local** and replace **yournet.net** tothe name of your personal domain. When done run **systemctl**:
```
systemctl start bind9
```
To use the dns server set the dns server of the vm who is external to kubernetes to the ip of the machine that has bind9 installed.

# How to setup kubernetes
Go to the kubernetes folder of this project and start to edit **ingress.yaml** replace the value of the **host** key with the name of your personal domain, then save and quit. In service.yaml edit the key **ip** with the ip of the vm that has microk8s installed. After this put these files in the vm who has microk8s installed and run these commands:

```
sudo microk8s kubectl apply -f service.yaml
sudo microk8s kubectl apply -f deployment.yaml
sudo microk8s kubectl apply -f ingress.yaml
```

Done this commands you now go to the vm who is external to the vm with kubernetes and try on your browser with:
```
http://your-dns-domain.com/
```

If everything went right you should have the nginx welcome page
