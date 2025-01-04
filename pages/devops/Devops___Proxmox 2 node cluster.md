## Attempt 1: 2 Proxmox clusters with 1 VM and Ceph as shared storage.  

1.  Follow the steps given here - #1 
1.  Created 2 proxmox cluster with Ceph cluster. 
1.  Created a VM template using the script given here - https://gitlab.iotiot.in/-/snippets/564
1.  Full Cloned the VM template to create a VM  on 1 node. 
1.  Installed nginx on the VM, and nginx default page is displayed at the IP of the VM. 
1.  On Migration (i.e pressing migrate in Proxmox) happens without any interruptions i.e there is no loss of data.
  1.  In this scene, there is no 
      1.  Fencing 
      2.  Ceph storage cluster is online (in Quorum)
1.  Switching off the proxmox node where the VM is running 
  1.  It takes 60 seconds approx to trigger fencing. 
  2.  It takes 60 more seconds to start migrating to other node. 
  3.  But the migration takes forever because the Ceph storage cluster is not online.
- ## Attempt 2: 2 Proxmox clusters with 1 VM and ZFS as shared storage.  
  
  1.  Following the steps given here - https://www.youtube.com/watch?v=UETNQJAz3Io
	- In this video, they have 3 proxmox nodes
	- But only using the ZFS part to create shared storage.
	- Also, this video it takes 5 mins to migrate - https://youtu.be/UETNQJAz3Io?t=937
	  
	  1.  Created ZFS shared storage for 2 proxmox nodes. 
	  1.  Full Cloned the VM template to create a VM  on 1 node with disk on the ZFS storage. 
	  1.  Switching off the proxmox node where the VM is running 
	  1.  It takes 60 seconds approx to trigger fencing. 
	  2.  It takes 60 more seconds to start migrating to other node. 
	  3.  Migration takes another 30 secs approx, so for 2mins 30 secs the service is offline.
- ### Key Learnings 
  1.  Bulk of the time is lost in fencing, better fencing methods can speed it up. 
  2.  Ceph storage is faster than ZFS shared storage. But Ceph requires atleast 3 nodes.
- ### Other Notes 
  1.  Most of the documentation is for 3 node clusters instead of 2 node clusters. Some people are using RPI as the 3rd node where it only acts as a QDevice (Quorum device) incase the other node goes down.
	- https://www.danatec.org/2021/05/21/two-node-cluster-in-proxmox-ve-with-raspberry-pi-as-qdevice/
	  
	  2.  With the addition of qdevice we can also use it to add it to the ceph cluster. Causing the ceph to also work if one node goes down, decreasing the migration time to whatever it takes to trigger fencing.