OpenShift 4.x Bare Metal (UPI) Installation on vSphere 8

This guide documents the full process of deploying a Red Hat OpenShift 4.x cluster using the UPI (User-Provisioned Infrastructure) method on a vSphere 8 environment. The domain used for this setup is bm.tayyabtahir.com.

🧰 Prerequisites

vCenter Server 8.x with ESXi hosts

DNS and DHCP servers

Load Balancer (HAProxy or equivalent)

RHEL/RHCOS ISO

OpenShift CLI (oc) and installer binaries

Pull secret from Red Hat

Internet access (or a properly mirrored registry for air-gapped)

Time-synced NTP server for all nodes

📁 Folder Structure in vCenter

VMs are organized under a custom folder:

Folder Name: 8.0-NetworkCluster

Hosts:

bastion.bm.tayyabtahir.com

lb.bm.tayyabtahir.com

bootstrap.bm.tayyabtahir.com

master{1,2,3}.bm.tayyabtahir.com

worker{1,2}.bm.tayyabtahir.com

▶️ GIF Reference: 11.gif, 12.gif, 13.gif

🌐 DNS & Load Balancer

DNS

Hosted internally

Resolves API, apps, wildcard, and node names

HAProxy

Acts as the load balancer for:

API: api.bm.tayyabtahir.com:6443

Apps: *.apps.bm.tayyabtahir.com

▶️ GIF Reference: 1.gif, 2.gif, 3.gif

🖥️ Bastion Setup

Host tools: openshift-install, oc, jq, httpd

Place install-config.yaml under ~/bm/config

openshift-install create manifests --dir config
vim config/manifests/cluster-scheduler-02-config.yml  # set mastersSchedulable=false
openshift-install create ignition-configs --dir config

▶️ GIF Reference: 4.gif, 5.gif, 6.gif, 7.gif

🌐 HTTP Server Setup (Bastion)

yum install httpd -y
mkdir -p /var/www/html/bm
cp config/*.ign /var/www/html/bm
chmod 777 /var/www/html/bm/*
systemctl enable httpd --now

▶️ GIF Reference: 8.gif

📦 VM Configuration

All nodes are based on RHCOS template

Ignition URLs mapped via static IP & MAC

▶️ GIF Reference: 14.gif, 15.gif, 16.gif

🚀 Bootstrap and Install

Start bootstrap node:

openshift-install wait-for bootstrap-complete --dir config --log-level=info

Wait until you see:

INFO Bootstrap complete. It is now safe to remove the bootstrap resources.

▶️ GIF Reference: 10.gif, 18.gif, 19.gif

Delete bootstrap VM after success:

# Delete bootstrap.bm.tayyabtahir.com from vCenter

✅ Node Status Check

oc get nodes

You should see all masters and workers in Ready state.

▶️ GIF Reference: 21.gif, 22.gif

📸 Screenshots (Optional)

17.png, 21.png are UI snapshots during install

📚 Credits

Built with ❤️ by Tayyab Tahir as part of lab automation and OpenShift studies.

📎 Notes

Compatible with RHCOS 4.18+ and vSphere 8.x

All nodes are deployed on VLAN-backed networks

Firewalls open for required OpenShift UPI ports (TCP 6443, 22623, 80, 443, etc.)

📌 Useful Links

Official OpenShift UPI Docs

OpenShift GitHub

If this repo helps, give it a ⭐ and share feedback!

