---
copyright:
  years: 1994, 2017

lastupdated: "2017-11-02"
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Set up Citrix Netscaler VPX for High Availability (HA)

Load balancers are used to balance traffic over multiple application servers to improve performance and stability in a scalable application. Yet, a single loadbalancer is a single point of failure. Avoid this by configuring a High Availability (HA) Netscaler VPX pair. Configuring an HA pair requires two Netscaler VPX servers. The secondary server steps in to continue load balancing should the primary fail. 

The SNIP (SubNet IP) is the IP seen by the load balanced servers as the source IP of the requests (connections) made to the VIP (Virtual IP) of the Netscaler VPX. Normally, in an HA pair setup, the SNIP does not change, but it is possible for it to do so during a failover event. When the two Netscalers are in the same subnet, it is possible for the SNIP of the secondary Netscaler VPX to change to the SNIP originally used by the primary Netscaler VPX. This will not cause any confusion for the servers handling the request.

For an HA configuration, both NetScalers must be in the same VLAN and on the same subnet.

Before beginning the HA configuration, ensure that you take the following prerequisite actions:

1. If the pair will be configured active-active, any VIP subnets added to the instances must be of type "Routed to VLAN".
* If the pair will be configured active-passive, any VIP subnets added to the instances must either be "Routed to VLAN" or "Secondary routed to IP", and routed to the public IP address of the primary node.

After ordering the two Netscaler VPX servers in the needed VLAN, and ordering the VIP and SNIP subnets to meet the prerequisites for your HA pair configuration, you can proceed with the following:

1. Open two browser windows and log into the advanced interface on both NetScalers. On the secondary NetScaler, go to **System > Users** and set the root password to the same as the primary NetScaler. Then update the password on file in the {{site.data.keyword.BluSoftlayer_notm}} portal on the device details page to match the password set for the secondary.

2. On the VPX you want to be secondary, click on **System > High Availability**, then right click the first line and click **Open**. Select **Stay Secondary** on the HA status config drop-down box and click **OK**.

3. Select **Add**. Enter the system IP address of the other VPX (this is located in the High Availability tab on the primary), and enter the root login details at the bottom. Leave the **INC** box unchecked. Click **OK**. 
	
	If you receive an error claiming that the IPs are not in the same subnet, it is possible both VPX servers are not in the same VLAN. Otherwise, you should now be able to open the primary server and select the **Refresh** button to see both servers operating in High Availability. 

	The new NetScaler management IP for the secondary will be one IP address lower than previously. If you cannot get any access to the secondary VPX, remove the high availability from the command line using:

	`sh ha node`

	Then remove what would be the ha node:
	
	`rm ha node 1`

4. On the primary VPX you should see that the remote VPX is now syncing. Go to the secondary server and check the **Network > IPs** configuration. You should see the primary server's VIP and other IPs listed as passive.

6. Return to **System > High Availability** on the secondary server and click **Open**. Select **ENABLED** in the drop-down box and hit **OK**.

7. Force a failover to test. Refresh the screen and watch the IP addresses become active on the secondary. Failover again and watch them go passive. Ping the IPs to make sure they work.

8. The primary server should now be labeled primary and the secondary should report has a synchronization state of success.