.. meta::
   :description: FQDN whitelists reference design
   :keywords: FQDN, whitelist, Aviatrix, Egress Control, AWS VPC


.. raw:: html

   <style>
    /* override table no-wrap */
   .wy-table-responsive table td, .wy-table-responsive table th {
       white-space: normal !important;
   }
   </style>

========================================
 Public Subnet Filtering Gateway FAQ
========================================


What does Public Subnet Filtering gateway do?
===============================================

Public Subnet Filtering gateway, PSF gateway, provides both Ingress and Egress security for AWS public subnets where
instances have public IP addresses. It includes two parts: Ingress filtering via GuardDuty enforcement and Egress FQDN. 

Public Subnet Filtering function is described in the diagram below. 

|public_subnet_filter|

Ingress protection via GuardDuty enforcement is a feature where Aviatrix Controller periodically polls 
the AWS GuardDuty findings and blocks the malicious source IP addresses from attacking the public subnet instances
by programming stateful firewall rules in the filtering gateway.  

Egress FQDN is the existing `FQDN feature <https://docs.aviatrix.com/HowTos/fqdn_faq.html>`_ applied to the public 
subnets. Previously this feature is only available to filter Egress traffic initiated from instances in the private subnets. 


How to deploy Public Subnet Filtering gateway?
-------------------------------------------------

Following the workflow below. 

1. Launch a Public Subnet Filtering gateway 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Go to Security -> Public Subnet -> Add New

===================       =================
Setting                   Value
===================       =================
Cloud Type                AWS
Gateway Name              Input a unique gateway name
Account Name              Select one Access Account
Region                    Select one AWS region
VPC ID                    Select one VPC in the region
Unused Subnet             Aviatrix Controller creates a public subnet and creates a route table associated with it to launch the filtering gateway
Gateway Size              Select an instance type
Route Table               Select a route table whose associated public subnets are protected.  
===================       =================

After the PSF gateway is launched, Ingress traffic from IGW is routed to the gateway in a pass through manner. 
Egress traffic from instances in the protected public subnets is routed to the gateway in a pass through manner. 

2. Enable GuardDuty Enforcement
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Go to Security -> AWS GuardDuty, select an Access Account and AWS Region. Click Enable.

Once GuardDuty is enabled, malicious source IP addresses attacking instances in the public subnets in the region 
will be polled by the Controller. The Controller then programs rules into the filtering gateway to drop these packets.

Note if you enable AWS GuarDuty without launching the PSP gateway, GuardDuty does not have enforcement function.  

3. Enable Egress FQDN 
^^^^^^^^^^^^^^^^^^^^^^^^

Once the PSP gateway is launched, you enable configure FQDN function. 

Go to Security -> Egress Control, follow the instructions in `FQDN workflow <https://docs.aviatrix.com/HowTos/FQDN_Whitelists_Ref_Design.html>`_.


.. |public_subnet_filter| image::  public_subnet_filtering_faq_media/public_subnet_filter.png
   :scale: 30%


.. add in the disqus tag

.. disqus::
