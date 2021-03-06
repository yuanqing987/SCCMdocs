---
title: Plan for application management
titleSuffix: Configuration Manager
description: Implement and configure the necessary dependencies for deploying applications in Configuration Manager.
ms.date: 05/01/2019
ms.prod: configuration-manager
ms.technology: configmgr-app
ms.topic: conceptual
ms.assetid: 2be84a1d-ebb9-47ae-8982-c66d5b92a52a
author: aczechowski
ms.author: aaroncz
manager: dougeby
ms.collection: M365-identity-device-management
---

# Plan for and configure application management in Configuration Manager

*Applies to: System Center Configuration Manager (current branch)*

Use the information in this article to help you implement the necessary dependencies to deploy applications in Configuration Manager.  



## Dependencies external to Configuration Manager  


### Internet Information Services (IIS)

IIS is required on the servers that run the following site system roles:

- Application Catalog website point  
- Application Catalog web service point  
- Management point  
- Distribution point  

For more information about this requirement, see [Site and site system prerequisites](/sccm/core/plan-design/configs/site-and-site-system-prerequisites).  


### Certificates on code-signed applications for mobile devices

When you code-sign applications to deploy them to mobile devices, don't use a certificate that was generated by using a Version 3 template (**Windows Server 2008, Enterprise Edition**). This certificate template creates a certificate that's incompatible with Configuration Manager applications for mobile devices.

If you use Active Directory Certificate Services to code-sign applications for mobile device applications, don't use a Version 3 certificate template.


### Audit sign-in events for user device affinity  

If you want to automatically create user device affinities, configure clients to audit sign-in events.

To determine automatic user device affinities, the Configuration Manager client reads sign-in events of type **Success** from the Windows security event log. Enable these events with the following two audit policies:

- **Audit account logon events**
- **Audit logon events**

To automatically create relationships between users and devices, make sure that these two settings are enabled on client computers. You can use Windows Group Policy to configure these settings.

For more information on user device affinity, see [Link users and devices with user device affinity](/sccm/apps/deploy-use/link-users-and-devices-with-user-device-affinity).  



## Configuration Manager dependencies


### Management point

Clients contact a management point to download client policy, to locate content, and to connect to the Application Catalog. If clients can't access a management point, they can't use the Application Catalog.

> [!Note]  
> Starting in version 1806, application catalog roles are no longer required to display user-available applications in Software Center. For more information, see [Configure Software Center](/sccm/apps/plan-design/plan-for-software-center#bkmk_userex).<!--1358309-->  
  

### Distribution point

Before you can deploy applications to clients, you need at least one distribution point in the hierarchy. By default, the site server has a distribution point site role enabled during a standard installation. The number and location of distribution points vary according to the specific requirements of your environment.

For more information about how to install distribution points and manage content, see [Manage content and content infrastructure](/sccm/core/servers/deploy/configure/manage-content-and-content-infrastructure).  


### Reporting services point

To use the reports in Configuration Manager for application management, first install and configure a reporting services point.

For more information, see [Reporting in Configuration Manager](/sccm/core/servers/manage/reporting).  


### Client settings

Many client settings control how the client installs applications and the user experience on the device. These client settings include the following groups:

- Computer agent  
- Computer restart  
- Software Center  
- Software deployment  
- User and device affinity  

For more information, see the following articles:

- [About client settings](/sccm/core/clients/deploy/about-client-settings)  
- [How to configure client settings](/sccm/core/clients/deploy/configure-client-settings)  


### Security permissions for application management

- The **Application Author** security role includes the required permissions to create, change, and retire applications.  

- The **Application Deployment Manager** security role includes required permissions to deploy applications.  

- The **Application Administrator** security role has all the permissions from both the **Application Author** and the **Application Deployment Manager** security roles.  

For more information, see [Configure role-based administration](/sccm/core/servers/deploy/configure/configure-role-based-administration).  


### App-V 4.6 SP1 or later client to run virtual applications

To create virtual applications in Configuration Manager, install App-V 4.6 SP1 or later on devices.

Before you deploy virtual applications, also update the App-V client with the hotfix described in the [Microsoft Support article 2645225](https://support.microsoft.com/help/2645225).  


### Discovered user accounts for Application Catalog

Configuration Manager must first discover user accounts before users can view and request applications from the Application Catalog. For more information, see [Run discovery](/sccm/core/servers/deploy/configure/run-discovery).  


### Application Catalog web service point

The Application Catalog web service point is a site system role that provides information about available software from your software library to the Application Catalog website that users access.

For more information about how to configure this site system role, see [Install and configure the Application Catalog](#bkmk_appcat).  

> [!Note]  
> Starting in version 1806, the application catalog web service point role is no longer *required*, but still *supported*.<!--1358309-->  
>
> The **Silverlight user experience** for the application catalog website point is no longer supported. For more information, see [Removed and deprecated features](/sccm/core/plan-design/changes/deprecated/removed-and-deprecated-cmfeatures).  


### Application Catalog website point

The Application Catalog website point is a site system role that provides users with a list of available software.

For more information about how to configure this site system role, see [Install and configure the Application Catalog](#bkmk_appcat).

> [!Note]  
> Starting in version 1806, the application catalog website point role is no longer *required*, but still *supported*.<!--1358309-->  
>
> The **Silverlight user experience** for the application catalog website point is no longer supported. For more information, see [Removed and deprecated features](/sccm/core/plan-design/changes/deprecated/removed-and-deprecated-cmfeatures).  



## <a name="bkmk_userex"></a> Configure Software Center  

For more information on configuring and branding Software Center, see [Plan for Software Center](/sccm/apps/plan-design/plan-for-software-center).


## <a name="bkmk_appcat"></a> Install and configure the Application Catalog  

> [!Note]  
> Starting in version 1806, the application catalog website point and web service point roles are no longer *required*, but still *supported*. For more information, see [Configure Software Center](/sccm/apps/plan-design/plan-for-software-center#bkmk_userex).  
>
> The **Silverlight user experience** for the application catalog *website point* is no longer supported. For more information, see [Removed and deprecated features](/sccm/core/plan-design/changes/deprecated/removed-and-deprecated-cmfeatures).  

> [!IMPORTANT]  
> Before you do these steps, make sure that all of the dependencies are in place. For more information, see the following sections in this article:
>
> - [Dependencies external to Configuration Manager](#dependencies-external-to-configuration-manager)  
> - [Configuration Manager dependencies](#configuration-manager-dependencies)


### Step 1: Web server certificate for HTTPS

If you use HTTPS connections, deploy a web server certificate to the site system servers for the Application Catalog website point and the Application Catalog web service point.

If you want clients to use the Application Catalog from the internet, deploy a web server certificate to at least one management point. Configure it for client connections from the internet.

For more information about certificate requirements, see [PKI certificate requirements](/sccm/core/plan-design/network/pki-certificate-requirements).  


### Step 2: Client authentication certificate for HTTPS

If you use a client PKI certificate for connections to management points, deploy a client authentication certificate to client computers. Although clients don't use a client PKI certificate to connect to the Application Catalog, they must connect to a management point before they can use the Application Catalog.

Deploy a client authentication certificate to client computers in the following scenarios:

- All management points on the intranet accept only HTTPS client connections.
- Clients connect to the Application Catalog from the internet.

For more information about certificate requirements, see [PKI certificate requirements](/sccm/core/plan-design/network/pki-certificate-requirements).  


### Step 3: Install and configure the Application Catalog roles

Install both the Application Catalog web service point and the Application Catalog website roles in the same site. You don't have to install them on the same server or in the same Active Directory forest. However, the Application Catalog web service point must be in the same forest as the site database.

For more information about server placement, see [Plan for site system servers and site system roles](/sccm/core/plan-design/hierarchy/plan-for-site-system-servers-and-site-system-roles).

> [!NOTE]  
> Install the Application Catalog at a primary site. You can't install it at a secondary site or the central administration site.  

Install the application catalog on a new site system server or an existing server in the site. For more information on the general procedure, see [Install site system roles](/sccm/core/servers/deploy/configure/install-site-system-roles). In the wizard to add a site system role or create a site system server, select the following roles from the list:

- **Application Catalog web service point**  
- **Application Catalog website point**  

> [!TIP]  
> If you want client computers to use the Application Catalog over the internet, specify the internet fully qualified domain name (FQDN).  

#### Verify the installation of these site system roles  

- Status messages: Use the components **SMS_PORTALWEB_CONTROL_MANAGER** and **SMS_AWEBSVC_CONTROL_MANAGER**.  

    For example, status ID **1015** for **SMS_PORTALWEB_CONTROL_MANAGER** confirms that Site Component Manager successfully installed the Application Catalog website point.  

- Log files: Search for **SMSAWEBSVCSetup.log** and **SMSPORTALWEBSetup.log**.  

    For more information, search for the **awebsvcMSI.log** and **portlwebMSI.log** log files.  


### Step 4: Configure client settings

If you want all users to have the same settings, configure the default client settings. Otherwise, configure custom client settings for specific collections.

For more information, see the following articles:

- [About client settings](/sccm/core/clients/deploy/about-client-settings)  
    - Computer agent  
    - Computer restart  
    - Software Center  
    - Software deployment  
    - User and device affinity  
- [How to configure client settings](/sccm/core/clients/deploy/configure-client-settings)  

The Configuration Manager client configures devices with these settings when it next downloads client policy. To trigger policy retrieval for a single client, see [How to manage clients](/sccm/core/clients/manage/manage-clients).


### Step 5: Verify that the Application Catalog is operational

Use the following procedures to verify that the Application Catalog is operational.

> [!NOTE]  
> The Application Catalog user experience requires Microsoft Silverlight. If you use the Application Catalog directly from a browser, first verify that Microsoft Silverlight is installed on the computer.  

> [!TIP]  
> Missing prerequisites are among the most typical reasons for the Application Catalog to operate incorrectly after installation. Confirm the role prerequisites for the Application Catalog site system roles. For more information, see [Site and site system prerequisites](/sccm/core/plan-design/configs/site-and-site-system-prerequisites).  

In a browser, enter the address of the Application Catalog website. Confirm that the web page shows the three tabs: **Application Catalog**, **My Application Requests**, and **My Devices**.  

Use the appropriate address for the Application Catalog from the following list, where &lt;server&gt; is the computer name, intranet FQDN, or internet FQDN:  

- HTTPS client connections and default site system role settings: **https://&lt;server&gt;/CMApplicationCatalog**  

- HTTP client connections and default site system role settings: **http://&lt;server&gt;/CMApplicationCatalog**  

- HTTPS client connections and custom site system role settings: **https://&lt;server&gt;:&lt;port&gt;/&lt;web application name&gt;**  

- HTTP client connections and custom site system role settings: **http://&lt;server&gt;:&lt;port&gt;/&lt;web application name&gt;**  

> [!NOTE]  
> If you signed in to the device with a Domain Administrator account, the Configuration Manager client doesn't display notification messages. For example, messages indicating that new software is available.  
