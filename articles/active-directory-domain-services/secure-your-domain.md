---
title: Skydda Azure AD Domain Services | Microsoft Docs
description: Lär dig hur du inaktiverar svaga chiffer, gamla protokoll och NTLM Password hash-synkronisering för en Azure Active Directory Domain Services hanterad domän.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.author: justinha
ms.openlocfilehash: 5fa19e23767af0e121d07872970199a2a1705ea8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104951948"
---
# <a name="disable-weak-ciphers-and-password-hash-synchronization-to-secure-an-azure-active-directory-domain-services-managed-domain"></a>Inaktivera svaga chiffer och Lösenordssynkronisering för att skydda en Azure Active Directory Domain Services hanterad domän

Azure Active Directory Domain Services (Azure AD DS) möjliggör som standard användningen av chiffer som NTLM v1 och TLS v1. Dessa chiffer kan krävas för vissa äldre program, men betraktas som svaga och kan inaktive ras om du inte behöver dem. Om du har en lokal hybrid anslutning som använder Azure AD Connect kan du också inaktivera synkroniseringen av NTLM-lösenords-hashvärden.

Den här artikeln visar hur du inaktiverar NTLM v1-och TLS v1-chiffer och inaktiverar hash-synkronisering av NTLM-lösenord.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här artikeln behöver du följande resurser:

* En aktiv Azure-prenumeration.
    * [Skapa ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)om du inte har någon Azure-prenumeration.
* En Azure Active Directory klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog som endast är moln.
    * Om det behövs kan du [skapa en Azure Active Directory klient][create-azure-ad-tenant] eller [associera en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En Azure Active Directory Domain Services hanterad domän aktive rad och konfigurerad i Azure AD-klienten.
    * Om det behövs kan du [skapa och konfigurera en Azure Active Directory Domain Services hanterad domän][create-azure-ad-ds-instance].

## <a name="use-security-settings-to-disable-weak-ciphers-and-ntlm-password-hash-sync"></a>Använd säkerhets inställningar för att inaktivera svaga chiffer och NTLM-lösenord för hash-synkronisering

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter och välj **Azure AD Domain Services**.
1. Välj din hanterade domän, till exempel *aaddscontoso.com*.
1. På den vänstra sidan väljer du **säkerhets inställningar**.
1. Klicka på **inaktivera** för följande inställningar:
   - **Endast TLS 1,2-läge**
   - **NTLM-autentisering**
   - **NTLM-Lösenordssynkronisering från lokal plats**

   ![Skärm bild av säkerhets inställningar för att inaktivera svaga chiffer och NTLM Password hash Sync](media/secure-your-domain/security-settings.png)

## <a name="use-powershell-to-disable-weak-ciphers-and-ntlm-password-hash-sync"></a>Använd PowerShell för att inaktivera svaga chiffer och NTLM Password hash Sync

Om det behövs [installerar och konfigurerar du Azure PowerShell](/powershell/azure/install-az-ps). Kontrol lera att du loggar in på Azure-prenumerationen med hjälp av cmdleten [Connect-AzAccount][Connect-AzAccount] . 

Du kan också [Installera och konfigurera Azure AD PowerShell](/powershell/azure/active-directory/install-adv2)vid behov. Kontrol lera att du loggar in på Azure AD-klienten med hjälp av cmdleten [Connect-AzureAD][Connect-AzureAD] .

Om du vill inaktivera svaga chiffersviter och hash-autentisering för NTLM-autentiseringsuppgifter loggar du in på ditt Azure-konto och hämtar sedan Azure AD DS-resursen med hjälp av cmdleten [Get-AzResource][Get-AzResource] :

> [!TIP]
> Om du får ett fel meddelande med kommandot [Get-AzResource][Get-AzResource] att resursen *Microsoft. AAD/DomainServices* inte finns kan du [öka din åtkomst för att hantera alla Azure-prenumerationer och hanterings grupper][global-admin].

```powershell
Login-AzAccount

$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"
```

Definiera sedan *DomainSecuritySettings* för att konfigurera följande säkerhets alternativ:

1. Inaktivera stöd för NTLM v1.
2. Inaktivera synkroniseringen av NTLM-lösenords-hashvärden från din lokala AD.
3. Inaktivera TLS v1.

> [!IMPORTANT]
> Användare och tjänst konton kan inte utföra enkla LDAP-bindningar om du inaktiverar hash-synkronisering av NTLM-lösenord i den hanterade domänen i Azure AD DS. Om du behöver utföra enkla LDAP-bindningar ska du inte ange alternativet *"SyncNtlmPasswords" = "inaktiverat".* säkerhets konfiguration i följande kommando.

```powershell
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}
```

Använd slutligen de definierade säkerhets inställningarna för den hanterade domänen med cmdleten [set-AzResource][Set-AzResource] . Ange Azure AD DS-resursen från det första steget och säkerhets inställningarna från föregående steg.

```powershell
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

Det tar en stund innan säkerhets inställningarna tillämpas på den hanterade domänen.

> [!IMPORTANT]
> När du har inaktiverat NTLM utför du en fullständig hash-synkronisering av lösen ord i Azure AD Connect att ta bort alla lösen ords-hashar från den hanterade domänen. Om du inaktiverar NTLM men inte tvingar fram en hash-synkronisering av lösen ord, tas NTLM-lösenords-hashvärden för ett användar konto bara bort vid nästa lösen ords ändring. Detta kan göra det möjligt för en användare att fortsätta logga in om de har cachelagrade autentiseringsuppgifter på ett system där NTLM används som autentiseringsmetod.
>
> När NTLM-lösenordets hash skiljer sig från Kerberos-lösenordets hash fungerar inte återställningen till NTLM. Cachelagrade autentiseringsuppgifter fungerar inte längre om den virtuella datorn har anslutning till den hanterade domänkontrollanten.  

## <a name="next-steps"></a>Nästa steg

Läs mer om synkroniseringsprocessen i [så här synkroniseras objekt och autentiseringsuppgifter i en hanterad domän][synchronization].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[global-admin]: ../role-based-access-control/elevate-access-global-admin.md
[synchronization]: synchronization.md

<!-- EXTERNAL LINKS -->
[Get-AzResource]: /powershell/module/az.resources/Get-AzResource
[Set-AzResource]: /powershell/module/Az.Resources/Set-AzResource
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
