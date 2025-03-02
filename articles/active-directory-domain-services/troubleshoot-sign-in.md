---
title: Troubleshoot sign in problems in Microsoft Entra Domain Services | Microsoft Docs
description: Learn how to troubleshoot common user sign-in problems and errors in Microsoft Entra Domain Services.
services: active-directory-ds
author: justinha
manager: amycolannino

ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/29/2023
ms.author: justinha

#Customer intent: As a directory administrator, I want to troubleshoot user account sign in problems in a Microsoft Entra Domain Services managed domain.
---

# Troubleshoot account sign-in problems with a Microsoft Entra Domain Services managed domain

The most common reasons for a user account that can't sign in to a Microsoft Entra Domain Services (Microsoft Entra DS) managed domain include the following scenarios:

* [The account isn't synchronized into Microsoft Entra DS yet.](#account-isnt-synchronized-into-azure-ad-ds-yet)
* [Microsoft Entra DS doesn't have the password hashes to let the account sign in.](#azure-ad-ds-doesnt-have-the-password-hashes)
* [The account is locked out.](#the-account-is-locked-out)

> [!TIP]
> Microsoft Entra DS can't synchronize in credentials for accounts that are external to the Microsoft Entra tenant. External users can't sign in to the Microsoft Entra DS managed domain.

<a name='account-isnt-synchronized-into-azure-ad-ds-yet'></a>

## Account isn't synchronized into Microsoft Entra DS yet

Depending on the size of your directory, it may take a while for user accounts and credential hashes to be available in a managed domain. For large directories, this initial one-way sync from Microsoft Entra ID can take few hours, and up to a day or two. Make sure that you wait long enough before retrying authentication.

For hybrid environments that user Microsoft Entra Connect to synchronize on-premises directory data into Microsoft Entra ID, make sure that you run the latest version of Microsoft Entra Connect and have [configured Microsoft Entra Connect to perform a full synchronization after enabling Microsoft Entra DS][azure-ad-connect-phs]. If you disable Microsoft Entra DS and then re-enable, you have to follow these steps again.

If you continue to have issues with accounts not synchronizing through Microsoft Entra Connect, restart the Azure AD Sync Service. From the computer with Microsoft Entra Connect installed, open a command prompt window, then run the following commands:

```console
net stop 'Microsoft Azure AD Sync'
net start 'Microsoft Azure AD Sync'
```

<a name='azure-ad-ds-doesnt-have-the-password-hashes'></a>

## Microsoft Entra DS doesn't have the password hashes

Microsoft Entra ID doesn't generate or store password hashes in the format that's required for NTLM or Kerberos authentication until you enable Microsoft Entra DS for your tenant. For security reasons, Microsoft Entra ID also doesn't store any password credentials in clear-text form. Therefore, Microsoft Entra ID can't automatically generate these NTLM or Kerberos password hashes based on users' existing credentials.

### Hybrid environments with on-premises synchronization

For hybrid environments using Microsoft Entra Connect to synchronize from an on-premises AD DS environment, you can locally generate and synchronize the required NTLM or Kerberos password hashes into Microsoft Entra ID. After you create your managed domain, [enable password hash synchronization to Microsoft Entra Domain Services][azure-ad-connect-phs]. Without completing this password hash synchronization step, you can't sign in to an account using the managed domain. If you disable Microsoft Entra DS and then re-enable, you have to follow those steps again.

For more information, see [How password hash synchronization works for Microsoft Entra DS][phs-process].

### Cloud-only environments with no on-premises synchronization

Managed domains with no on-premises synchronization, only accounts in Microsoft Entra ID, also need to generate the required NTLM or Kerberos password hashes. If a cloud-only account can't sign in, has a password change process successfully completed for the account after enabling Microsoft Entra DS?

* **No, the password has not been changed.**
    * [Change the password for the account][enable-user-accounts] to generate the required password hashes, then wait for 15 minutes before you try to sign in again.
    * If you disable Microsoft Entra DS and then re-enable, each account must follow the steps again to change their password and generate the required password hashes.
* **Yes, the password has been changed.**
    * Try to sign in using the *UPN* format, such as `driley@aaddscontoso.com`, instead of the *SAMAccountName* format like `AADDSCONTOSO\deeriley`.
    * The *SAMAccountName* may be automatically generated for users whose UPN prefix is overly long or is the same as another user on the managed domain. The *UPN* format is guaranteed to be unique within a Microsoft Entra tenant.

## The account is locked out

A user account in a managed domain is locked out when a defined threshold for unsuccessful sign-in attempts has been met. This account lockout behavior is designed to protect you from repeated brute-force sign-in attempts that may indicate an automated digital attack.

By default, if there are 5 bad password attempts in 2 minutes, the account is locked out for 30 minutes.

For more information and how to resolve account lockout issues, see [Troubleshoot account lockout problems in Microsoft Entra DS][troubleshoot-account-lockout].

## Next steps

If you still have problems joining your VM to the managed domain, [find help and open a support ticket for Microsoft Entra ID][azure-ad-support].

<!-- INTERNAL LINKS -->
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-ad-connect-phs]: ./tutorial-configure-password-hash-sync.md
[enable-user-accounts]:  tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[phs-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
