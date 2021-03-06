---
layout: doc
h3InToc: true
contributedBy: Martin Zugec, Mads Petersen, Arnaud Pain, James Kindon
specialThanksTo: Bjorn Paulson
description: Overview of ports that are used by Citrix components and must be considered as part of networking architecture.
---
# Communication Ports Used by Citrix Technologies

This article provides an overview of common ports that are used by Citrix components and must be considered as part of networking architecture, especially if communication traffic traverses network components such as firewalls or proxy servers where ports must be opened to ensure communication flow.

Not all ports need to be open, depending on your deployment and requirements.

## Citrix ADC

| Source            | Destination                                  | Type     | Port            | Details                                                                                                                                                               |
| ----------------- | -------------------------------------------- | -------- | --------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Citrix ADC NSIP   | Citrix ADC Appliances in cluster setup       | UDP      | 7000            | Cluster heart beat exchange                                                                                                                                           |
|                   | Citrix ADC Appliance (for High Availability) | UDP      | 3003            | Exchange of hello packets for communicating UP/DOWN status (heartbeat)                                                                                                |
|                   |                                              | TCP      | 3008            | Secure High Availability configuration synchronization                                                                                                                |
|                   |                                              | TCP      | 3009            | For secure MEP.                                                                                                                                                       |
|                   |                                              | TCP      | 3010            | Non-secure high availability configuration synchronization.                                                                                                           |
|                   |                                              | TCP      | 3011            | For non-secure MEP.                                                                                                                                                   |
|                   |                                              | UDP      | 162             | Traps from ADC to Citrix ADM Center                                                                                                                                   |
|                   |                                              | TCP      | 22              | Used by the rsync process during file synchronization in high availability setup                                                                                      |
|                   | DNS Server                                   | TCP, UDP | 53              | DNS name resolution                                                                                                                                                   |
|                   | Application Firewall signature URL           | TCP      | 443             | Hosted signature updates on AWS                                                                                                                                       |
|                   | Bot Management signature URL                 | TCP      | 443             | Hosted signature updates on AWS                                                                                                                                       |
|                   | ADC lights out management                    | TCP      | 4001, 5900, 623 | Daemon which offers complete and unified configuration management of all the routing protocols                                                                        |
|                   | Integrated Management Interface              | TCP, UDP | 389             | LDAP connection                                                                                                                                                       |
|                   | Thales HSM                                   | TCP      | 9004            | RFS and Thales HSM                                                                                                                                                    |
|                   | Citrix ADM                                   | UDP      | 4739            | For AppFlow communication                                                                                                                                             |
|                   |                                              | SNMP     | 161, 162        | To send SNMP events                                                                                                                                                   |
|                   |                                              | Syslog   | 514             | To receive syslog messages in Citrix ADM                                                                                                                              |
|                   |                                              | TCP      | 5557, 5558      | For logstream communication from Citrix ADC to Citrix ADM.                                                                                                            |
| Admin Workstation | Citrix ADC NSIP                              | TCP      | 80, 443         | HTTP or HTTPS - GUI Administration                                                                                                                                    |
|                   |                                              | TCP      | 8443            | If an HTML client is used, then only 8443 port needs to be open between client and Command Center server. Citrix recommends using an HTML client as much as possible. |
|                   |                                              | TCP      | 22              | SSH Access                                                                                                                                                            |

  >**Note:**
  >
  >Depending on the Citrix ADC configuration, network traffic can originate from SNIP, MIP, or NSIP interfaces.
  >
  >[Link to application firewall signatures](https://s3.amazonaws.com/NSAppFwSignatures/SignaturesMapping.xml)
  >
  >[Link to bot management signatures](https://nsbotsignatures.s3.amazonaws.com/BotSignatureMapping.json)

## Citrix ADM

| Source                    | Destination                               | Type   | Port             | Details                                                                                                                                                       |
| ------------------------- | ----------------------------------------- | ------ | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Citrix ADM                | Citrix ADC NSIP or Citrix SD-WAN instance | TCP    | 80, 443          | For NITRO communication                                                                                                                                       |
|                           |                                           | TCP    | 22               | For SSH communication                                                                                                                                         |
|                           |                                           | ICMP   | No reserved port | To detect network reachability between Citrix ADM and ADC instances, SD-WAN instances, or the secondary Citrix ADM server deployed in high availability mode. |
|                           | Citrix ADM                                | TCP    | 22               | For synchronization between Citrix ADM servers deployed in high availability mode.                                                                            |
|                           |                                           | TCP    | 5454             | Default port for communication, and database synchronization in between Citrix ADM nodes in high availability mode.                                           |
|                           | Users                                     | TCP    | 25               | To send SMTP notifications from Citrix ADM to users.                                                                                                          |
|                           | LDAP external authentication server       | TCP    | 389, 636         | Default port for authentication protocol. For communication between Citrix ADM and LDAP external authentication server.                                       |
|                           | NTP Server                                | UDP    | 123              | Default NTP server port for synchronizing with multiple time sources.                                                                                         |
|                           | RADIUS external authentication server     | RADIUS | 1812             | Default port for authentication protocol. For communication between Citrix ADM and RADIUS external authentication server.                                     |
|                           | TACACS external authentication server     | TACACS | 49               | Default port for authentication protocol. For communication between Citrix ADM and TACACS external authentication server.                                     |
| Citrix ADM license server | Citrix ADC CPX instance                   | TCP    | 27000            | License port for communication between Citrix ADM license server and CPX instance.                                                                            |
|                           |                                           | TCP    | 7279             | Citrix vendor daemon port.                                                                                                                                    |
| Citrix ADC                | Citrix ADM                                | TCP    | 5563             | To receive ADC metrics (counters), system events, and Audit Log messages from Citrix ADC instance to Citrix ADM                                               |
|                           |                                           | TCP    | 5557, 5558       | For logstream communication (for Security Insight, Web Insight, and HDX Insight) fron Citrix ADC                                                              |
|                           |                                           | UDP    | 162              | To receive SNMP events from Citrix ADC                                                                                                                        |
|                           |                                           | UDP    | 514              | To receive syslog messages from Citrix ADC or Citrix SD-WAN instance                                                                                          |
|                           |                                           | UDP    | 4739             | To receive ADC analytics log data using IPFIX protocol                                                                                                        |
| Citrix ADM                | Citrix ADM Agent                          | TCP    | 443, 8443, 7443  | Port for communication between Citrix ADC agent and Citrix ADM                                                                                                |

## Citrix Cloud

The only Citrix component needed to serve as a channel for communication between Citrix Cloud and your resource locations is Citrix Cloud Connector. All connections are established from the Cloud Connector to the cloud using the standard HTTPS port (443) and the TCP protocol. No incoming connections are accepted.

Host management and Machine Creation Management capabilities of Citrix Cloud also require TCP 9350–9354 opened for communications to the Citrix-managed control plane.

Cloud Connectors must be able to connect to Digicert for certificate revocation checks.

| Source           | Destination                                                              | Type  | Port | Details                                     |
| ---------------- | ------------------------------------------------------------------------ | ----- | ---- | ------------------------------------------- |
| Cloud Connectors | `http://*.digicert.com`                                                  | HTTP  | 80   | Periodic Certificate Revocation List checks |
|                  | `https://*.digicert.com`                                                 | HTTPS | 443  |                                             |
|                  | `https://dl.cacerts.digicert.com/DigiCertAssuredIDRootCA.crt`            | HTTPS | 443  |                                             |
|                  | `https://dl.cacerts.digicert.com/DigiCertSHA2AssuredIDCodeSigningCA.crt` | HTTPS | 443  |                                             |

To find the list of addresses that are common to most Citrix Cloud services and their function, refer to [product documentation](/en-us/citrix-cloud/overview/requirements/internet-connectivity-requirements.html#service-connectivity-requirements).

### Citrix Cloud - Virtual Apps and Desktops Service

| Source                | Destination      | Type  | Port | Details                                                  |
| --------------------- | ---------------- | ----- | ---- | -------------------------------------------------------- |
| VDA                   | Gateway Service  | TCP   | 443  | Rendezvous Protocol                                      |
| Provisioning Servers  | Cloud Connectors | HTTPS | 443  | Provisioning Server integration with Citrix Cloud Studio |
| Citrix License Server | Citrix Cloud     | HTTPS | 443  | Citrix License Server integration with Citrix Cloud      |

 Read more about Citrix License Server integration [here](/en-us/citrix-cloud/citrix-cloud-management/citrix-cloud-on-premises-registration.html#connectivity-requirements).
 Read more about Citrix Provisioning Server integration [here](/en-us/provisioning/current-release/configure/cloud-connector.html#firewall-considerations).

### Citrix Cloud - Gateway Service

By default, the Gateway Service will proxy HDX connections via the Citrix Cloud Connectors, however Rendezvous Protocol changes the flow of HDX connections in an attempt to directly connect the VDA to the Gateway Service bypassing the Citrix Cloud Connectors

#### Rendezvous Protocol and HDX Enlightened Data Transport Protocol (EDT)

| Source | Destination     | Type | Port | Details                             |
| ------ | --------------- | ---- | ---- | ----------------------------------- |
| VDA    | Gateway Service | UDP  | 443  | EDT UDP over 443 to Gateway Service |

The VDAs must have access to `https://*.nssvc.net`, including all subdomains. Or `https://*.c.nssvc.net` and `https://*.g.nssvc.net`.

  >**Note:**
  >
  >If using EDT in Microsoft Azure, UDP must be defined on the Citrix Gateway protecting the VDA

Read more about Rendezvous Protocol and HDX Enlightened Data Transport Protocol (EDT) requirements [here](/en-us/citrix-gateway-service/hdx-edt-support-for-gateway-service.html).

### Citrix Cloud - Workspace Environment Management Service

| Source    | Destination     | Type  | Port | Details                                                             |
| --------- | --------------- | ----- | ---- | ------------------------------------------------------------------- |
| WEM Agent | WEM Service     | HTTPS | 443  | HTTPS based communication between the WEM Agent and the WEM Service |
| WEM Agent | Cloud Connector | TCP   | 443  | Registration Traffic for WEM Agents                                 |

Read more about Citrix Workspace Environment Management Service requirements [here](/en-us/workspace-environment-management/service.html).

## Citrix Endpoint Management

Refer to the following link for Citrix Endpoint Management (XenMobile) Ports – [Port Requirements](/en-us/citrix-endpoint-management/system-requirements.html#port-requirements).

## Citrix Gateway

| Source                 | Destination                           | Type     | Port             | Details                                                                                                                                                               |
| ---------------------- | ------------------------------------- | -------- | ---------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Citrix Gateway SNIP    | LDAP Server                           | TCP      | 636              | LDAPS SSL connection                                                                                                                                                  |
|                        |                                       | TCP      | 3268             | LDAP connection to Global Catalog                                                                                                                                     |
|                        |                                       | TCP      | 3269             | LDAP connection to Global Catalog over SSL                                                                                                                            |
|                        |                                       | TCP      | 389              | LDAP plaintext or TLS                                                                                                                                                 |
|                        | RADIUS Server                         | UDP      | 1813             | RADIUS accounting                                                                                                                                                     |
|                        |                                       | UDP      | 1645, 1812       | RADIUS connection                                                                                                                                                     |
|                        | Secure Ticketing Authority (STA)      | TCP      | 80, 8080, 443    | Secure Ticketing Authority (embedded into XML Service)                                                                                                                |
|                        | Citrix Virtual Apps and Desktops VDAs | TCP, UDP | 1494             | Access to applications and virtual desktops by ICA/HDX. EDT protocol requires 1494 to be open for UDP.                                                                |
|                        |                                       | TCP, UDP | 2598             | Access to applications and virtual desktops by ICA/HDX with Session Reliability. EDT protocol requires 2598 to be open for UDP.                                       |
|                        |                                       | TCP, UDP | 443              | Access to applications and virtual desktops by ICA/HDX over TLS/DTLS.                                                                                                 |
|                        |                                       | TCP      | 8008             | Access to applications and virtual desktops by ICA/HDX from Citrix Workspace app for HTML5                                                                            |
|                        |                                       | UDP      | 16500..16509     | ICA/HDX audio over UDP Real-time Transport                                                                                                                            |
|                        | StoreFront                            | TCP      | 80, 443          | Citrix Gateway communication with StoreFront                                                                                                                          |
| Citrix Gateway Plug-in | VPN/CVAD                              | UDP      | 3108, 3168, 3188 | For VPN tunnel with secure ICA connections                                                                                                                            |
|                        |                                       | TCP, UDP | 3148             | For VPN tunnel with secure ICA connections                                                                                                                            |
| Admin Workstation      | Citrix Gateway                        | TCP      | 80, 443          | HTTPS - GUI Administration                                                                                                                                            |
|                        |                                       | TCP      | 8443             | If an HTML client is used, then only 8443 port needs to be open between client and Command Center server. Citrix recommends using an HTML client as much as possible. |
|                        |                                       | TCP      | 22               | SSH Access                                                                                                                                                            |
| Citrix Gateway         | DNS                                   | TCP, UDP | 53               | Communication with the DNS server                                                                                                                                     |

For more information about required ports for Citrix Gateway in DMZ setup, refer to [CTX113250](https://support.citrix.com/article/CTX113250).

  >**Note:**
  >
  >All the above ports are not mandatory, depending on your own configuration.

## Citrix Hypervisor

| Source                        | Destination                   | Type         | Port        | Details                                                                  |
| ----------------------------- | ----------------------------- | ------------ | ----------- | ------------------------------------------------------------------------ |
| Citrix Hypervisor             | Citrix Hypervisor             | TCP          | 443         | Intra-host communication between members of a Resource Pool using XenAPI |
|                               | NTP Service                   | TCP, UDP     | 123         | Time Synchronization                                                     |
|                               | DNS Service Domain Controller | TCP, UDP TCP | 53, 389     | DNS User authentication when using Active Directory integration (LDAP)   |
|                               |                               | TCP          | 636         | LDAP over SSL (LDAPS)                                                    |
|                               | FileServer                    | TCP, UDP     | 139         | ISOStore:NetBIOSSessionService                                           |
|                               |                               | TCP, UDP     | 445         | ISOStore:Microsoft-DS                                                    |
|                               | SAN Controller                | TCP          | 3260        | iSCSI Storage                                                            |
|                               | NAS Head/ File Server         | TCP          | 2049        | NFS Storage                                                              |
|                               | Syslog                        | TCP          | 514         | Sends data to a central location for collation                           |
|                               | Clustering                    | TCP          | 8892, 21064 | Communication between all pool members in a clustered pool.              |
|                               |                               | UDP          | 5404, 5405  |                                                                          |
| Admin Workstation (XenCenter) | Citrix Hypervisor             | TCP          | 22          | SSH                                                                      |
|                               |                               | TCP          | 443         | Management using XenAPI                                                  |
|                               | Virtual Machine               | TCP          | 5900        | VNC for Linux Guests                                                     |
|                               |                               | TCP          | 3389        | RDP for WindowsGuests                                                    |

Read more about Citrix License Server requirements [here](#citrix-license-server).

  >**Note:**
  >
  >If FQDN is used instead of IP as resource, then make sure it is resolvable.

## Citrix License Server

| Source               | Destination           | Type | Port  | Details                                               |
| -------------------- | --------------------- | ---- | ----- | ----------------------------------------------------- |
| Any Citrix Component | Citrix License Server | TCP  | 27000 | Handles initial point of contact for license requests |
|                      |                       | TCP  | 7279  | Check-in/check-out of Citrix licenses                 |
| Admin Workstation    | Citrix License Server | TCP  | 8082  | Web-based administration console (Lmadmin.exe)        |
|                      |                       | TCP  | 8083  | Simple License Service port (required for CVAD)       |
|                      |                       | TCP  | 80    | Licensing Config PowerShell Snap-in Service           |

## Citrix SD-WAN

| Source                                          | Destination                                     | Type   | Port     | Details                                                                                                                                                                |
| ----------------------------------------------- | ----------------------------------------------- | ------ | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| SD-WAN Standard and Enterprise Edition          | SD-WAN Standard and Enterprise Edition          | UDP    | 4980     | Static Virtual Path and Dynamic Virtual Path tunnels between SD-WAN SE/EE devices.                                                                                     |
|                                                 | SD-WAN Center                                   | TCP    | 2156     | Reporting communication between SD-WAN Center and SD-WAN SE/EE devices.                                                                                                |
|                                                 | Citrix Cloud Zero Touch Deployment Service      | TCP    | 443      | Authentication communication between SD-WAN devices and Citrix Cloud Services.                                                                                         |
|                                                 | RADIUS                                          | TCP    | 1812     | Default port for authentication protocol. For communication between SD-WAN SE/EE and RADIUS external authentication server.                                            |
|                                                 | TACACS+                                         | TACACS | 49       | Default port for authentication protocol. For communication between SD-WAN SE/EE and TACACS external authentication server.                                            |
|                                                 | SNMP                                            | UDP    | 161, 162 | SNMP authentication and polling to SD-WAN SE/EE devices.                                                                                                               |
|                                                 | NetFlow                                         | UDP    | 2055     | NetFlow polling to SD-WAN SE/EE devices.                                                                                                                               |
|                                                 | AppFlow (Citrix ADM)                            | TCP    | 4739     | For AppFlow communication between Citrix ADM and SD-WAN SE/EE devices.                                                                                                 |
|                                                 | API                                             | TCP    | 80, 443  | For NITRO API communication to SD-WAN SE/EE devices.                                                                                                                   |
| SD-WAN Center                                   | Citrix Cloud Zero Touch Deployment Service      | TCP    | 443      | Authentication communication between SD-WAN devices and Citrix Cloud Services.                                                                                         |
| SD-WAN WANOP Edition                            | SD-WAN WANOP Edition                            | TCP    | N/A      | SD-WAN WO Edition transparently optimizes TCP traffic between two sites. The original source destination and port go unchanged throughout the segments of the network. |
|                                                 | API (Citrix ADM)                                | TCP    | 80, 443  | For NITRO API communication between Citrix ADM and SD-WAN WANOP devices.                                                                                               |
|                                                 | SSH (Citrix ADM)                                | TCP    | 22       | For SSH communication between Citrix ADM and SD-WAN WANOP devices.                                                                                                     |
|                                                 | AppFlow (Citrix ADM)                            | TCP    | 4739     | For AppFlow communication between Citrix ADM and SD-WAN WANOP devices.                                                                                                 |
|                                                 | Citrix ADM                                      | ICMP   | N/A      | For network reachability between Citrix ADM and SD-WAN WANOP devices.                                                                                                  |
|                                                 | RADIUS                                          | TCP    | 1812     | Default port for authentication protocol. For communication between SD-WAN WO and RADIUS external authentication server.                                               |
|                                                 | TACACS+                                         | TACACS | 49       | Default port for authentication protocol. For communication between SD-WAN WO and TACACS external authentication server.                                               |
|                                                 | SNMP                                            | UDP    | 161, 162 | SNMP authentication and polling to SD-WAN WO devices.                                                                                                                  |
| SD-WAN WANOP Edition (SSL Acceleration Enabled) | SD-WAN WANOP Edition (SSL Acceleration Enabled) | TCP    | 443      | SD-WAN WO Edition secure peering feature encrypts traffic between SD-WAN peers.                                                                                        |

## Citrix Virtual Apps and Desktops

| Source                                | Destination                            | Type     | Port                                           | Details                                                                                                                                                                                                                                                                                                                                                                                                          |
| ------------------------------------- | -------------------------------------- | -------- | ---------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| DDC Controller                        | Citrix Hypervisor Resource Pool Master | TCP      | 80, 443                                        | Communication with Citrix Hypervisor infrastructure                                                                                                                                                                                                                                                                                                                                                              |
|                                       | Microsoft SCVMM Server                 | TCP      | 8100                                           | Communication with Hyper-V infrastructure                                                                                                                                                                                                                                                                                                                                                                        |
|                                       | VMware vCenter Server                  | TCP      | 443                                            | Communication with vSphere infrastructure                                                                                                                                                                                                                                                                                                                                                                        |
|                                       | Microsoft SQL Server                   | TCP      | 1433                                           | Microsoft SQL Server                                                                                                                                                                                                                                                                                                                                                                                             |
|                                       |                                        | TCP      | 1434                                           | Microsoft SQL Server. Note: Named instance connection requires UDP 1434                                                                                                                                                                                                                                                                                                                                          |
|                                       | Virtual Delivery Agent                 | TCP      | 80 (Bidirectional)                             | DDC Controller initiates the connection when discovering local applications or for gathering information about local processes, performance data, and so on                                                                                                                                                                                                                                                      |
|                                       |                                        | TCP      | 89                                             | Local Host Cache (This use of port 89 might change in future releases.)                                                                                                                                                                                                                                                                                                                                          |
|                                       | DDC Controller                         | TCP      | 80                                             | Communication between Controllers.                                                                                                                                                                                                                                                                                                                                                                               |
|                                       |                                        | TCP      | 89                                             | Local Host Cache (This use of port 89 might change in future releases.)                                                                                                                                                                                                                                                                                                                                          |
|                                       |                                        | TCP      | 9095                                           | Orchestration service                                                                                                                                                                                                                                                                                                                                                                                            |
| Citrix Director and Admin Workstation | Virtual Delivery Agent                 | TCP      | 135, 3389                                      | Communication between Citrix Director and Virtual Delivery Agent for Remote Assistance                                                                                                                                                                                                                                                                                                                           |
|                                       |                                        | TCP      | 389                                            | LDAP Note: For the logon step, Citrix Director does not contact the AD but does a local logon using the native Windows API– LogonUser (which might internally be contacting the AD).                                                                                                                                                                                                                             |
| Citrix Workspace app                  | StoreFront                             | TCP      | 80, 443                                        | Communication with StoreFront                                                                                                                                                                                                                                                                                                                                                                                    |
|                                       | Virtual Delivery Agent                 | TCP, UDP | 2598                                           | Access to applications and virtual desktops by ICA/HDX with Session Reliability. EDT protocol requires 2598 to be open for UDP.                                                                                                                                                                                                                                                                                  |
|                                       |                                        | TCP, UDP | 1494                                           | Access to applications and virtual desktops by ICA/HDX. EDT protocol requires 1494 to be open for UDP.                                                                                                                                                                                                                                                                                                           |
|                                       |                                        | TCP      | 443                                            | Access to applications and virtual desktops by ICA/HDX over SSL                                                                                                                                                                                                                                                                                                                                                  |
|                                       |                                        | TCP      | 8008                                           | Access to applications and virtual desktops by ICA/HDX from HTML5 Receiver                                                                                                                                                                                                                                                                                                                                       |
|                                       |                                        | UDP      | 16500..16509                                   | Port range for UDP ICA/HDX audio                                                                                                                                                                                                                                                                                                                                                                                 |
| Virtual Delivery Agent                | DDC Controller                         | TCP      | 80 (Bidirectional)                             | Used by process `WorkstationAgent.exe` for communicating with DDC Controller                                                                                                                                                                                                                                                                                                                                     |
| Admin Workstation                     | Director Server                        | TCP      | 80, 443                                        | Access to Citrix Director website                                                                                                                                                                                                                                                                                                                                                                                |
|                                       | DDC Controller                         | TCP      | 80, 443                                        | When using a locally installed Citrix Studio console or the SDK to directly access DDC Controller                                                                                                                                                                                                                                                                                                                |
|                                       | Virtual Delivery Agent                 | TCP, UDP | Dynamically allocated high-port (49152..65535) | When initiating a Remote Assistance session from a Windows machine to a Virtual Delivery Agent                                                                                                                                                                                                                                                                                                                   |
| HdxVideo.js                           | Virtual Delivery Agent                 | TCP      | 9001                                           | HTML5 video redirection and [Browser Content Redirection](/en-us/citrix-virtual-apps-desktops/policies/reference/ica-policy-settings/browser-content-redirection-policy-settings.html) secure WebSocket service needed to redirect HTTPS websites. WebSocketService.exe - Runs on the local system and performs SSL termination and user session mapping. TLS Secure WebSocket listening on 127.0.0.1 port 9001. |

Read more about Citrix License Server requirements [here](#citrix-license-server).

### Citrix App Layering

Refer to the following link for Citrix App Layering ports – [Firewall Ports](/en-us/citrix-app-layering/4/manage/firewall-ports.html).

### Federated Authentication Service

| Source                          | Destination                     | Type     | Port | Details                                                         |
| ------------------------------- | ------------------------------- | -------- | ---- | --------------------------------------------------------------- |
| StoreFront                      | FAS Server                      | TCP      | 80   | To send identity assertion of the user.                         |
| FAS Server                      | Microsoft Certificate Authority | TCP      | 135  | Certificate Request.                                            |
|                                 | Domain Controller               | TCP, UDP | 389  | Validate the user account before creating a certificate request |
| Microsoft Certificate Authority | FAS Server                      | TCP      | 135  | Issue certificate to the certificate request from FAS Server.   |
| Virtual Delivery Agent          | FAS Server                      | TCP      | 80   | Fetch the user certificate from the FAS Server.                 |
|                                 | Domain Controller               | TCP, UDP | 389  | Authentication of user during application or desktop launch     |

  >**Note:**
  >
  >The Microsoft CA accepts communication using Kerberos authenticated DCOM, which can be configured to use a fixed TCP port. To learn more about it, see [Federated Authentication Service certificate authority configuration](/en-us/federated-authentication-service/config-manage/ca-configuration.html).

### Provisioning Services

| Source                                                                                                               | Destination           | Type | Port         | Details                                                                                                                                                                                                                       |
| -------------------------------------------------------------------------------------------------------------------- | --------------------- | ---- | ------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Provisioning Server                                                                                                  | Provisioning Server   | UDP  | 6890..6909   | Inter-server communication                                                                                                                                                                                                    |
|                                                                                                                      | Microsoft SQL Server  | TCP  | 1433         | Communication with Microsoft SQL Server                                                                                                                                                                                       |
|                                                                                                                      | Domain Controller     | TCP  | 389          | Communication with Active Directory                                                                                                                                                                                           |
| Target Device (PVS outbound communication on ports 6901, 6902 and 6905 for Target Devices starting with version 6.0) | Broadcast/DHCPServer  | UDP  | 67, 4011     | Optional: Obtaining network boot information in case DHCP options 66-TFTP Server Name (Bootstrap Protocol Server) and 67-Boot file name (Bootstrap Protocol Client) are not configured or boot from ISO/ local disk not used. |
|                                                                                                                      | Broadcast/ PXEService | UDP  | 69           | Trivial File Transfer (TFTP) for Bootstrap delivery                                                                                                                                                                           |
|                                                                                                                      | TFTP Server           | UDP  | 6910         | Target Device log on at Provisioning Services                                                                                                                                                                                 |
|                                                                                                                      | Provisioning Server   | UDP  | 6910..6930   | Virtual disk Streaming (Streaming Service) (configurable)                                                                                                                                                                     |
|                                                                                                                      |                       | UDP  | 6969, 2071   | Two Stage Boot (BDM). Used in boot from ISO or USB scenarios only.                                                                                                                                                            |
|                                                                                                                      |                       | TCP  | 54321..54323 | SOAP Service – Used by Imaging Wizards                                                                                                                                                                                        |
| Admin Workstation                                                                                                    | Provisioning Server   | TCP  | 54321..54323 | SOAP Service – Used by Console and APIs (MCLI, PowerShell, etc.)                                                                                                                                                              |

### Session Recording

| Source                           | Destination              | Type | Port    | Details                                                                                                                                                                                                                         |
| -------------------------------- | ------------------------ | ---- | ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Virtual Delivery Agent           | Session Recording Server | TCP  | 80, 443 | Communication between Session Recording Agent installed on Virtual Delivery Agent to connect to the Session Recording Server. Default installation uses HTTPS/SSL to secure communications. If SSL is not configured, use HTTP. |
| Session Recording Policy Console | Session Recording Server | TCP  | 80, 443 | Communication between server where the Session Recording Policy Console is installed and Session Recording Server                                                                                                               |
| Session Recording Player         | Session Recording Server | TCP  | 80, 443 | Communication between the workstation where the Session Recording Player is installed and Session Recording Server.                                                                                                             |

### StoreFront

| Source            | Destination               | Type     | Port                                                                                                                                                             | Details                                                                                                                                                                                                                                            |
| ----------------- | ------------------------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| User Device       | StoreFront Server         | TCP      | 80, 443                                                                                                                                                          | Connecting to the store hosted on StoreFront server                                                                                                                                                                                                |
| StoreFront Server | Domain Controller         | TCP, UDP | 389                                                                                                                                                              | LDAP connection to query user-friendly name and email addresses                                                                                                                                                                                    |
|                   |                           | TCP, UDP | 88                                                                                                                                                               | Kerberos                                                                                                                                                                                                                                           |
|                   |                           | TCP, UDP | 464                                                                                                                                                              | Native Windows authentication protocol to allow users to change expired passwords                                                                                                                                                                  |
|                   | StoreFront Server         | TCP      | Randomly selected unreserved port per service. Scroll down to the end of this table for configuration of firewalls when you place StoreFront in its own network. | Used for Peer-to-peer Services (Credential Wallet, Subscriptions Store (1 per Store). This service uses MS .Net NetPeerTcpBinding which negotiates a random port on each server between the peers. Only used for communication within the cluster. |
|                   |                           | TCP      | 808                                                                                                                                                              | Used for Subscription Replication Services. Not installed by default. Used to replicate subscriptions between associated clusters                                                                                                                  |
|                   | DDC Controller, XenMobile | TCP      | 80, 443                                                                                                                                                          | For application and desktop requests.                                                                                                                                                                                                              |
|                   | Citrix ADC                | TCP      | 8000                                                                                                                                                             | For Monitoring Service used by Citrix ADC load balancer.                                                                                                                                                                                           |
| StoreFront        | Citrix Gateway            | TCP      | 443                                                                                                                                                              | Callback URL to reach Citrix Gateway from StoreFront                                                                                                                                                                                               |

Use the following information for configuration of firewalls when you place StoreFront in its own network:

1.  Locate the config files:
    -  `C:\Program Files\Citrix\Receiver StoreFront\Services\SubscriptionsStoreService\Citrix.DeliveryServices.SubscriptionsStore.ServiceHost.exe.config`
    -  `C:\Program Files\Citrix\Receiver StoreFront\Services\CredentialWallet\Citrix.DeliveryServices.CredentialWallet.ServiceHost.exe.config`
1.  Edit both the config files changing the values for endpoint URIs.

    For example - `<endpoint uri="net.p2p://CitrixCredentialWalletReplication">` so any address that starts with `net.p2p://` includes the port. You should end up with `<endpoint uri="net.p2p://CitrixCredentialWalletReplication:93”>` and `<endpoint uri="net.p2p://Citrix-Subscriptions-1__Citrix_Store">` becomes `<endpoint uri="net.p2p://Citrix-Subscriptions-1__Citrix_Store:93">` and so on for all other net.p2p addresses.

1.  Restart the subscriptions store and credential wallet.
1.  The local firewall includes rules for allowing per application access, so it is not locked down by port.

### Workspace Environment Management

| Source                              | Destination            | Type | Port  | Details                                                                                                                                                                                                                                                                                                                                                     |
| ----------------------------------- | ---------------------- | ---- | ----- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Infrastructure service              | Agent host             | TCP  | 49752 | "Agent port". Listening port on the agent host which receives instructions from the infrastructure service.                                                                                                                                                                                                                                                 |
| Administration console              | Infrastructure service | TCP  | 8284  | "Administration port". Port on which the administration console connects to the infrastructure service.                                                                                                                                                                                                                                                     |
| Agent                               | Infrastructure service | TCP  | 8286  | "Agent service port". Port on which the agent connects to the infrastructure server.                                                                                                                                                                                                                                                                        |
| Agent cache synchronization process | Infrastructure service | TCP  | 8285  | "Cache synchronization port". Applicable to Workspace Environment Management 1909 and earlier; replaced by Cached data synchronization port in Workspace Environment Management 1912 and later. Port on which the agent cache synchronization process connects to the infrastructure service to synchronize the agent cache with the infrastructure server. |
|                                     |                        | TCP  | 8288  | "Cached data synchronization port". Applicable to Workspace Environment Management 1912 and later; replaces Cache synchronization port of Workspace Environment Management 1909 and earlier. Port on which the agent cache synchronization process connects to the infrastructure service to synchronize the agent cache with the infrastructure server.    |
| Monitoring service                  | Infrastructure service | TCP  | 8287  | "WEM monitoring port". Listening port on the infrastructure server used by the monitoring service. (Not yet implemented.)                                                                                                                                                                                                                                   |
| Infrastructure service              | Microsoft SQL Server   | TCP  | 1433  | To connect to WEM Database                                                                                                                                                                                                                                                                                                                                  |
|                                     | Citrix License Server  | TCP  | 27000 | "Citrix License Server port". The port on which the Citrix License Server is listening and to which the infrastructure service then connects to validate licensing.                                                                                                                                                                                         |
|                                     |                        | TCP  | 7279  | The port used by the dedicated Citrix component (daemon) in the Citrix License Server to validate licensing.                                                                                                                                                                                                                                                |

Read more about Citrix Workspace Environment Management requirements [here](/en-us/tech-zone/build/tech-papers/citrix-communication-ports.html).

Read more about Citrix License Server requirements [here](#citrix-license-server).
