---
title: "Prepare a non-routable domain for directory synchronization"
ms.author: josephd
author: JoeDavies-MSFT
manager: laurawi
audience: Admin
ms.topic: article
f1_keywords:
- 'O365E_SetupDirSyncLocalDir'
ms.service: o365-administration
localization_priority: Normal
ms.custom: Adm_O365
ms.collection:
- Ent_O365
- M365-identity-device-management
search.appverid:
- MET150
- MOE150
- MED150
- BCS160
ms.assetid: e7968303-c234-46c4-b8b0-b5c93c6d57a7
description: "Learn what to do if you have a non-routale domain associated with your on-premises users before you synchronize with Office 365."
---

# Prepare a non-routable domain for directory synchronization
When you synchronize your on-premises directory with Office 365 you have to have a verified domain in Azure Active Directory. Only the User Principal Names (UPN) that are associated with the on-premises domain are synchronized. However, any UPN that contains an non-routable domain, for example .local (like billa@contoso.local), will be synchronized to an .onmicrosoft.com domain (like billa@contoso.onmicrosoft.com). 

If you currently use a .local domain for your user accounts in Active Directory it's recommended that you change them to use a verified domain (like billa@contoso.com) in order to properly sync with your Office 365 domain.
  
## What if I only have a .local on-premises domain?

The most recent tool you can use for synchronizing your Active Directory to Azure Active Directory is named Azure AD Connect. For more information, see [Integrating your on-premises identities with Azure Active Directory](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/azure-ad).
  
Azure AD Connect synchronizes your users' UPN and password so that users can sign in with the same credentials they use on-premises. However, Azure AD Connect only synchronizes users to domains that are verified by Office 365. This means that the domain also is verified by Azure Active Directory because Office 365 identities are managed by Azure Active Directory. In other words, the domain has to be a valid Internet domain (for example, .com, .org, .net, .us, etc.). If your internal Active Directory only uses a non-routable domain (for example, .local), this can't possibly match the verified domain you have on Office 365. You can fix this issue by either changing your primary domain in your on premises Active Directory, or by adding one or more UPN suffixes.
  
### **Change your primary domain**

Change your primary domain to a domain you have verified in Office 365, for example, contoso.com. Every user that has the domain contoso.local is then updated to contoso.com. For instructions, see [How Domain Rename Works](https://go.microsoft.com/fwlink/p/?LinkId=624174). This is a very involved process, however, and an easier solution is to [Add UPN suffixes and update your users to them](prepare-a-non-routable-domain-for-directory-synchronization.md#bk_register), as shown in the following section.
  
### **Add UPN suffixes and update your users to them**

You can solve the .local problem by registering new UPN suffix or suffixes in Active Directory to match the domain (or domains) you verified in Office 365. After you register the new suffix, you update the user UPNs to replace the .local with the new domain name for example so that a user account looks like billa@contoso.com.
  
After you have updated the UPNs to use the verified domain,you are ready to synchronize your on-premises Active Directory with Office 365.
  
 **Step 1: Add the new UPN suffix**
  
1. On the server that Active Directory Domain Services (AD DS) runs on, in the Server Manager choose **Tools** \> **Active Directory Domains and Trusts**.
    
    **Or, if you don't have Windows Server 2012**
    
    Press **Windows key + R** to open the **Run** dialog, and then type in Domain.msc, and then choose **OK**.
    
    ![Choose Active Directory Domains and Trusts.](media/46b6e007-9741-44af-8517-6f682e0ac974.png)
  
2. On the **Active Directory Domains and Trusts** window, right-click **Active Directory Domains and Trusts**, and then choose **Properties**.
    
    ![Right-click ActiveDirectory Domains and Trusts and choose Properties](media/39d20812-ffb5-4ba9-8d7b-477377ac360d.png)
  
3. On the **UPN Suffixes** tab, in the **Alternative UPN Suffixes** box, type your new UPN suffix or suffixes, and then choose **Add** \> **Apply**.
    
    ![Add an new UPN suffix](media/a4aaf919-7adf-469a-b93f-83ef284c0915.PNG)
  
    Choose **OK** when you're done adding suffixes. 
    
 **Step 2: Change the UPN suffix for existing users**
  
1. On the server that Active Directory Domain Services (AD DS) runs on, in the Server Manager choose **Tools** \> **Active Directory Active Directory Users and Computers**.
    
    **Or, if you don't have Windows Server 2012**
    
    Press **Windows key + R** to open the **Run** dialog, and then type in Dsa.msc, and then click **OK**
    
2. Select a user, right-click, and then choose **Properties**.
    
3. On the **Account** tab, in the UPN suffix drop-down list, choose the new UPN suffix, and then choose **OK**.
    
    ![Add new UPN suffix for a user](media/54876751-49f0-48cc-b864-2623c4835563.png)
  
4. Complete these steps for every user.
    
    Alternately you can bulk update the UPN suffixes [You can also use Windows PowerShell to change the UPN suffix for all users](prepare-a-non-routable-domain-for-directory-synchronization.md#BK_Posh).
    
### **You can also use Windows PowerShell to change the UPN suffix for all users**

If you have a lot of users to update, it is easier to use Windows PowerShell. The following example uses the cmdlets [Get-ADUser](https://go.microsoft.com/fwlink/p/?LinkId=624312) and [Set-ADUser](https://go.microsoft.com/fwlink/p/?LinkId=624313) to change all contoso.local suffixes to contoso.com. 

Run the following Windows PowerShell commands to update all contoso.local suffixes to contoso.com:
    
  ```
  $LocalUsers = Get-ADUser -Filter {UserPrincipalName -like '*contoso.local'} -Properties userPrincipalName -ResultSetSize $null
  ```

  ```
  $LocalUsers | foreach {$newUpn = $_.UserPrincipalName.Replace("contoso.local","contoso.com"); $_ | Set-ADUser -UserPrincipalName $newUpn}
  ```
See [Active Directory Windows PowerShell module](https://go.microsoft.com/fwlink/p/?LinkId=624314) to learn more about using Windows PowerShell in Active Directory. 

