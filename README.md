# Tanzu Kubernetes Grid Integrated (TKGI) / PKS  persistent labeller and tainter

## What does this do?

TKGI/PKS destroys and creates new nodes whenever there is an OS or Kubernetes upgrade, or a node failure.  Node IDs thus will change at VM recreate, remediate or upgrade time. 

This script ensures any Taints or Labels added to that node are preserved between recreations.  

Note:  There is an up to 15 second delay before these are persisted.   Also, scaling down the cluster will permanently remove nodes from the deployment (and thus their persistent disk that remembers any labels or taints).

## How do I install it?

1. Open a shell prompt on a BOSH CLI with access to your TKGI bosh director, such as Ops Manager.
2. Export your BOSH credentials to the enviornment.  These can be accessed via the Ops Manager GUI -> BOSH Director Tile -> Credentials Tab -> Bosh Commandline Credentials.    

e.g.
```
export BOSH_CLIENT=ops_manager BOSH_CLIENT_SECRET=fakesecret BOSH_CA_CERT=/var/tempest/workspaces/default/root_ca_certificate  BOSH_ENVIRONMENT=10.0.0.10
```
3. Copy or clone this repository onto this BOSH CLI workstation and create+upload the BOSH release to the director

```
git clone https://github.com/svrc-pivotal/tkgi-persistent-taints-labels && cd tkgi-persistent-taints-labels
bosh create-release
bosh upload-release ./dev_releases/tkgi-persistent-taints-labels/tkgi-persistent-taints-labels-0+dev.1.yml 

```
4. Configure the addon from this repo
```
bosh -n update-config --name=tkgi-persistent-taints-labels --type=runtime ./pks-watch-tl-manifest.yml
```
5. Update your TKGI clusters via the PKS CLI and/or Ops Manager "Apply Pending Changes" button with the TKGI upgrade errand enabled.  This addon will automatically be installed on all worker nodes with the default manifest `pks-watch-tl-manifest.yml`



