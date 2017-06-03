<h5>HYPERVISOR</h5>
Software on a single physical host computer that creates and manages multiple guest virtual machines. Type 1 (bare-metal) hypervisors run directly on hardware with no host operating system and type 2 hypervisors run on top of a host operating system, such as Linux. See also virtual machine.

<h5>NVF</h5>
Network Functions Virtualization. Standard IT virtualization technology that consolidates many network equipment types onto standard-architecture high-volume servers, switches, and storage. NFV involves designing, deploying, and managing network functions in software that can be moved to, or instantiated in, various locations in the network as required, without the need to install purpose-built hardware. Although NFV complements software-defined networking (SDN), NFV can be deployed without SDN and vice versa. See also SDN.

<h5>J-WEB</h5>
Graphical Web browser interface to Junos OS on routing platforms. With the J-Web interface, you can monitor, configure, diagnose, and manage the routing platform from a PC or laptop that has Hypertext Transfer Protocol (HTTP) or HTTP over Secure Sockets Layer (HTTPS) enabled.

<h5>Security Zone</h5>A security zone is a collection of interfaces that define a security boundary. Internal network interfaces may be assigned to a security zone named "trust," and external network interfaces may be assigned to a security zone named "untrust." Security policies are then used to control transit traffic between security zones.

<h5>Service Chains in Contrail</h5> 
The service chaining feature allows the operator to insert dynamic services to control the traffic between two virtual networks. The service chaining works on a basic rule of next-hop stitching.

<h5>Service Templates</h5>
Service templates map out the basic configuration that Contrail uses to instantiate a service instance, or VM. Within Contrail, you configure service templates in the scope of a domain, and you can use the templates on all projects within a domain. You can use a
template to launch multiple service instances of the same type in different projects within a domain. Within a service template, you select the service mode, a vSRX image name for the VM that will provide the service, and an ordered list of interfaces for the service.
vSRX service VMs require the management interface to be the first interface in that ordered list. You can use OpenStack Horizon or Glance to add the vSRX image. You also select the OpenStack flavor to associate with all service instances that use the service
template. An OpenStack flavor defines the number of vCPUs, storage, and memory you can assign to a VM. OpenStack includes default flavors, and you can create new flavors in the OpenStack dashboard.

<h5>Virtual Networks</h5>
Virtual networks provide the link between the service instance and the network traffic in the virtualized environment. You can create the virtual networks in Contrail or OpenStack and use those networks to direct traffic to or through the service instance.

<h5>Service Instances</h5>
A service instance is the instantiation of the selected service template to create one or more VMs that provide the service (for example, a firewall). When you create a service instance, you select a service template that defines the instance. You also associate the interfaces in the service template with the virtual networks needed to direct traffic into and out of the service instance. If you enable service scaling in the selected service template, you can instantiate more than one VM when you create the service instance.

<h5>Network Policies </h5>
By default, all traffic in a virtual network remains isolated. You configure a network policy to allow traffic between virtual networks and through the service instance. The network policy filters traffic to and from the service VM based on the rules you configure. You
select the service instance VM and the virtual networks for the right and left interfaces of that VM that the network policy applies to. As a final step. you associate the network policy with each virtual network the policy applies to.


<h5>Contrail network policy</h5> It provides security between networks by allow/deny certain traffic. Contrail network policy also provides connectivity between virtual networks.

<h5>OpenStack security groups </h5> allow access between workloads/instances for specified traffic types and rest denied.

<h5>Floating-ip and Simple Gateway</h5>

   <b>Simple gateway</b> let's the virtual machines access underlay network. The access is provided by selective leaking of routes between vrf for virtual network and underlay network. No NAT is involved here.

   <b>Floating-ip</b> allows a VM to borrow IP address from another network. 1:1 NAT is done by vrouter for communication between VM and the borrowed virtual network.
    The Contrail idea is that each Compute nodes directly connect Gateway router for scaling, efficiency instead of Neutron network node. So Contrail recommends to use Gateway router

<h5>Leaf switches</h5> Are popular devices used in data centers to aggregate traffic from server nodes and then connect to the core of the network, consisting of spine switches. In this model, there are only two tiers of switches between the servers and the core network. This popular data center design architecture is known as “leaf-spine” and was popularized as data centers grew in scale.
<a href="https://www.sdxcentral.com/sdn/definitions/what-are-leaf-switches/">https://www.sdxcentral.com/sdn/definitions/what-are-leaf-switches/</a>
