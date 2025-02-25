---
title: Security recommendations for Blob storage
titleSuffix: Azure Storage
description: Learn about security recommendations for Blob storage. Implementing this guidance will help you fulfill your security obligations as described in our shared responsibility model.
services: storage
author: tamram

ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: tamram
ms.custom: security-recommendations
---

# Security recommendations for Blob storage

This article contains security recommendations for Blob storage. Implementing these recommendations will help you fulfill your security obligations as described in our shared responsibility model. For more information on how Microsoft fulfills service provider responsibilities, read [Shared responsibilities for cloud computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225366/1/Shared%20Responsibility%20for%20Cloud%20Computing-2019-10-25.pdf).

Some of the recommendations included in this article can be automatically monitored by Azure Security Center. Azure Security Center is the first line of defense in protecting your resources in Azure. For information on Azure Security Center, see the [What is Azure Security Center?](../../security-center/security-center-introduction.md)

Azure Security Center periodically analyzes the security state of your Azure resources to identify potential security vulnerabilities. It then provides you with recommendations on how to address them. For more information on Azure Security Center recommendations, see [Security recommendations in Azure Security Center](../../security-center/security-center-recommendations.md).

## Data protection

| Recommendation | Comments | Security Center |
|-|----|--|
| Use the Azure Resource Manager deployment model | Create new storage accounts using the Azure Resource Manager deployment model for important security enhancements, including superior Azure role-based access control (Azure RBAC) and auditing, Resource Manager-based deployment and governance, access to managed identities, access to Azure Key Vault for secrets, and Azure AD-based authentication and authorization for access to Azure Storage data and resources. If possible, migrate existing storage accounts that use the classic deployment model to use Azure Resource Manager. For more information about Azure Resource Manager, see [Azure Resource Manager overview](../../azure-resource-manager/management/overview.md). | - |
| Enable Azure Defender for all of your storage accounts | Azure Defender for Azure Storage provides an additional layer of security intelligence that detects unusual and potentially harmful attempts to access or exploit storage accounts. Security alerts are triggered in Azure Security Center when anomalies in activity occur and are also sent via email to subscription administrators, with details of suspicious activity and recommendations on how to investigate and remediate threats. For more information, see [Configure Azure Defender for Azure Storage](../common/azure-defender-storage-configure.md). | [Yes](../../security-center/security-center-sql-service-recommendations.md) |
| Turn on soft delete for blobs | Soft delete for blobs enables you to recover blob data after it has been deleted. For more information on soft delete for blobs, see [Soft delete for Azure Storage blobs](./soft-delete-blob-overview.md). | - |
| Turn on soft delete for containers | Soft delete for containers enables you to recover a container after it has been deleted. For more information on soft delete for containers, see [Soft delete for containers (preview)](./soft-delete-container-overview.md). | - |
| Lock storage account to prevent accidental account deletion | You can lock an Azure Resource Manager resources, such as a subscription, resource group, or storage account, to prevent other users in your organization from accidentally deleting or modifying it. Locking a storage account does not prevent data within that account from being deleted. It only prevents the account itself from being deleted. For more information, see [Lock resources to prevent unexpected changes](../../azure-resource-manager/management/lock-resources.md).
| Store business-critical data in immutable blobs | Configure legal holds and time-based retention policies to store blob data in a WORM (Write Once, Read Many) state. Blobs stored immutably can be read, but cannot be modified or deleted for the duration of the retention interval. For more information, see [Store business-critical blob data with immutable storage](storage-blob-immutable-storage.md). | - |
| Require secure transfer (HTTPS) to the storage account | ??? | - |
| Limit shared access signature (SAS) tokens to HTTPS connections only | Requiring HTTPS when a client uses a SAS token to access blob data helps to minimize the risk of eavesdropping. For more information, see [Grant limited access to Azure Storage resources using shared access signatures (SAS)](../common/storage-sas-overview.md). | - |

## Identity and access management

| Recommendation | Comments | Security Center |
|-|----|--|
| Use Azure Active Directory (Azure AD) to authorize access to blob data | Azure AD provides superior security and ease of use over Shared Key for authorizing requests to Blob storage. For more information, see [Authorize access to Azure blobs and queues using Azure Active Directory](../common/storage-auth-aad.md). | - |
| Keep in mind the principal of least privilege when assigning permissions to an Azure AD security principal via Azure RBAC | When assigning a role to a user, group, or application, grant that security principal only those permissions that are necessary for them to perform their tasks. Limiting access to resources helps prevent both unintentional and malicious misuse of your data. | - |
| Use a user delegation SAS to grant limited access to blob data to clients | A user delegation SAS is secured with Azure Active Directory (Azure AD) credentials and also by the permissions specified for the SAS. A user delegation SAS is analogous to a service SAS in terms of its scope and function, but offers security benefits over the service SAS. For more information, see [Grant limited access to Azure Storage resources using shared access signatures (SAS)](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). | - |
| Secure your account access keys with Azure Key Vault | Microsoft recommends using Azure AD to authorize requests to Azure Storage. However, if you must use Shared Key authorization, then secure your account keys with Azure Key Vault. You can retrieve the keys from the key vault at runtime, instead of saving them with your application. For more information about Azure Key Vault, see [Azure Key Vault overview](../../key-vault/general/overview.md). | - |
| Regenerate your account keys periodically | Rotating the account keys periodically reduces the risk of exposing your data to malicious actors. | - |
| Disallow Shared Key authorization | When you disallow Shared Key authorization for a storage account, Azure Storage rejects all subsequent requests to that account that are authorized with the account access keys. Only secured requests that are authorized with Azure AD will succeed. For more information, see [Prevent Shared Key authorization for an Azure Storage account](../common/shared-key-authorization-prevent.md). | - |
| Keep in mind the principal of least privilege when assigning permissions to a SAS | When creating a SAS, specify only those permissions that are required by the client to perform its function. Limiting access to resources helps prevent both unintentional and malicious misuse of your data. | - |
| Have a revocation plan in place for any SAS that you issue to clients | If a SAS is compromised, you will want to revoke that SAS as soon as possible. To revoke a user delegation SAS, revoke the user delegation key to quickly invalidate all signatures associated with that key. To revoke a service SAS that is associated with a stored access policy, you can delete the stored access policy, rename the policy, or change its expiry time to a time that is in the past. For more information, see [Grant limited access to Azure Storage resources using shared access signatures (SAS)](../common/storage-sas-overview.md).  | - |
| If a service SAS is not associated with a stored access policy, then set the expiry time to one hour or less | A service SAS that is not associated with a stored access policy cannot be revoked. For this reason, limiting the expiry time so that the SAS is valid for one hour or less is recommended. | - |
| Disable anonymous public read access to containers and blobs | Anonymous public read access to a container and its blobs grants read-only access to those resources to any client. Avoid enabling public read access unless your scenario requires it. To learn how to disable anonymous public access for a storage account, see [Configure anonymous public read access for containers and blobs](anonymous-read-access-configure.md).  | - |

## Networking

| Recommendation | Comments | Security Center |
|-|----|--|
| Configure the minimum required version of Transport Layer Security (TLS) for a storage account.  | Require that clients use a more secure version of TLS to make requests against an Azure Storage account by configuring the minimum version of TLS for that account. For more information, see [Configure minimum required version of Transport Layer Security (TLS) for a storage account](../common/transport-layer-security-configure-minimum-version.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)| - |
| Enable the **Secure transfer required** option on all of your storage accounts | When you enable the **Secure transfer required** option, all requests made against the storage account must take place over secure connections. Any requests made over HTTP will fail. For more information, see [Require secure transfer in Azure Storage](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). | [Yes](../../security-center/security-center-remediate-recommendations.md) |
| Enable firewall rules | Configure firewall rules to limit access to your storage account to requests that originate from specified IP addresses or ranges, or from a list of subnets in an Azure Virtual Network (VNet). For more information about configuring firewall rules, see [Configure Azure Storage firewalls and virtual networks](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). | - |
| Allow trusted Microsoft services to access the storage account | Turning on firewall rules for your storage account blocks incoming requests for data by default, unless the requests originate from a service operating within an Azure Virtual Network (VNet) or from allowed public IP addresses. Requests that are blocked include those from other Azure services, from the Azure portal, from logging and metrics services, and so on. You can permit requests from other Azure services by adding an exception to allow trusted Microsoft services to access the storage account. For more information about adding an exception for trusted Microsoft services, see [Configure Azure Storage firewalls and virtual networks](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).| - |
| Use private endpoints | A private endpoint assigns a private IP address from your Azure Virtual Network (VNet) to the storage account. It secures all traffic between your VNet and the storage account over a private link. For more information about private endpoints, see [Connect privately to a storage account using Azure Private Endpoint](../../private-link/tutorial-private-endpoint-storage-portal.md). | - |
| Use VNet service tags | A service tag represents a group of IP address prefixes from a given Azure service. Microsoft manages the address prefixes encompassed by the service tag and automatically updates the service tag as addresses change. For more information about service tags supported by Azure Storage, see [Azure service tags overview](../../virtual-network/service-tags-overview.md). For a tutorial that shows how to use service tags to create outbound network rules, see [Restrict access to PaaS resources](../../virtual-network/tutorial-restrict-network-access-to-resources.md). | - |
| Limit network access to specific networks | Limiting network access to networks hosting clients requiring access reduces the exposure of your resources to network attacks. | [Yes](../../security-center/security-center-sql-service-recommendations.md) |
| Configure network routing preference | You can configure network routing preference for your Azure storage account to specify how network traffic is routed to your account from clients over the Internet using the Microsoft global network or Internet routing. For more information, see [Configure network routing preference for Azure Storage](../common/network-routing-preference.md). | - |

## Logging/Monitoring

| Recommendation | Comments | Security Center |
|-|----|--|
| Track how requests are authorized | Enable Azure Storage logging to track how each request made against Azure Storage was authorized. The logs indicate whether a request was made anonymously, by using an OAuth 2.0 token, by using Shared Key, or by using a shared access signature (SAS). For more information, see [Monitoring Azure Blob storage with Azure Monitor](monitor-blob-storage.md) or [Azure Storage analytics logging with Classic Monitoring](../common/storage-analytics-logging.md). | - |
| Set up alerts in Azure Monitor | Configure log alerts to evaluate resources logs at a set frequency and fire an alert based on the results. For more information, see [Log alerts in Azure Monitor](../../azure-monitor/platform/alerts-unified-log.md). | - |

## Next steps

- [Azure security documentation](../../security/index.yml)
- [Secure development documentation](../../security/develop/index.yml).
