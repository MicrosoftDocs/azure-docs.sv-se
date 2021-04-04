---
title: Gruppera hanterade tjänst konton för Azure AD Domain Services | Microsoft Docs
description: Lär dig hur du skapar ett grupphanterat tjänst konto (gMSA) för användning med Azure Active Directory Domain Services hanterade domäner
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: f975d3e0e605b7c24b9fd31dc8fc78f0f37bb6b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619992"
---
# <a name="create-a-group-managed-service-account-gmsa-in-azure-active-directory-domain-services"></a>Skapa ett grupphanterat tjänst konto (gMSA) i Azure Active Directory Domain Services

Program och tjänster behöver ofta en identitet för att autentisera sig med andra resurser. En webb tjänst kan till exempel behöva autentisera med en databas tjänst. Om ett program eller en tjänst har flera instanser, till exempel en webb Server grupp, får du tids krävande att skapa och konfigurera identiteterna för dessa resurser.

I stället kan ett grupphanterat tjänst konto (gMSA) skapas i den Azure Active Directory Domain Services (Azure AD DS)-hanterade domänen. Windows OS hanterar automatiskt autentiseringsuppgifterna för en gMSA, vilket fören klar hanteringen av stora grupper av resurser.

Den här artikeln visar hur du skapar en gMSA i en hanterad domän med hjälp av Azure PowerShell.

## <a name="before-you-begin"></a>Innan du börjar

För att slutföra den här artikeln behöver du följande resurser och behörigheter:

* En aktiv Azure-prenumeration.
    * [Skapa ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)om du inte har någon Azure-prenumeration.
* En Azure Active Directory klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog som endast är moln.
    * Om det behövs kan du [skapa en Azure Active Directory klient][create-azure-ad-tenant] eller [associera en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En Azure Active Directory Domain Services hanterad domän aktive rad och konfigurerad i Azure AD-klienten.
    * Om det behövs, slutför du själv studie kursen för att [skapa och konfigurera en Azure Active Directory Domain Services hanterad domän][create-azure-ad-ds-instance].
* En virtuell Windows Server Management-dator som är ansluten till den hanterade Azure AD DS-domänen.
    * Om det behövs kan du slutföra självstudien för att [skapa en virtuell hanterings dator][tutorial-create-management-vm].

## <a name="managed-service-accounts-overview"></a>Översikt över hanterade tjänst konton

Ett fristående hanterat tjänst konto (sMSA) är ett domän konto vars lösen ord hanteras automatiskt. Den här metoden fören klar hanteringen av tjänstens huvud namn (SPN) och möjliggör delegerad hantering till andra administratörer. Du behöver inte skapa och rotera autentiseringsuppgifter manuellt för kontot.

Ett grupphanterat tjänst konto (gMSA) har samma hanterings förenkling, men för flera servrar i domänen. Med en gMSA kan alla instanser av en tjänst som finns i en Server grupp använda samma tjänst huvud namn för ömsesidiga autentiseringsprotokoll som fungerar. När en gMSA används som tjänstens huvud namn, hanterar Windows-operativsystemet igen kontots lösen ord i stället för att förlita dig på administratören.

Mer information finns i [Översikt över grupphanterade tjänst konton (gMSA)][gmsa-overview].

## <a name="using-service-accounts-in-azure-ad-ds"></a>Använda tjänst konton i Azure AD DS

Eftersom hanterade domäner är låsta och hanteras av Microsoft, finns det några saker att tänka på när du använder tjänst konton:

* Skapa tjänst konton i anpassade organisationsenheter (OU) på den hanterade domänen.
    * Du kan inte skapa ett tjänst konto i de inbyggda *AADDC-användarna* eller *AADDC-datorernas* organisationsenheter.
    * Skapa i stället [en anpassad Organisationsenhet][create-custom-ou] i den hanterade domänen och skapa sedan tjänst konton i den anpassade organisationsenheten.
* Rot nyckeln för Key Distribution Services (KDS) har skapats i förväg.
    * Rot nyckeln KDS används för att generera och hämta lösen ord för gMSAs. I Azure AD DS skapas KDS-roten åt dig.
    * Du har inte behörighet att skapa en annan, eller så kan du Visa standard rot nyckeln KDS.

## <a name="create-a-gmsa"></a>Skapa en gMSA

Skapa först en anpassad ORGANISATIONSENHET med cmdleten [New-ADOrganizationalUnit][New-AdOrganizationalUnit] . Mer information om hur du skapar och hanterar anpassade organisationsenheter finns [i anpassade organisationsenheter i Azure AD DS][create-custom-ou].

> [!TIP]
> Om du vill slutföra de här stegen för att skapa en gMSA [använder du den virtuella hanterings datorn][tutorial-create-management-vm]. Den här virtuella hanterings datorn ska redan ha de AD PowerShell-cmdletar som krävs och anslutning till den hanterade domänen.

I följande exempel skapas en anpassad ORGANISATIONSENHET med namnet *myNewOU* i den hanterade domänen med namnet *aaddscontoso.com*. Använd din egen ORGANISATIONSENHET och ditt hanterade domän namn:

```powershell
New-ADOrganizationalUnit -Name "myNewOU" -Path "DC=aaddscontoso,DC=COM"
```

Skapa nu en gMSA med cmdleten [New-ADServiceAccount][New-ADServiceAccount] . Följande exempel parametrar definieras:

* **-Name** är inställt på *WebFarmSvc*
* Parametern **-Path** anger den anpassade organisationsenheten för den gMSA som skapades i föregående steg.
* DNS-poster och tjänst huvud namn har angetts för *WebFarmSvc.aaddscontoso.com*
* Huvud konton i *AADDSCONTOSO-Server $* får hämta lösen ordet och använda identiteten.

Ange egna namn och domän namn.

```powershell
New-ADServiceAccount -Name WebFarmSvc `
    -DNSHostName WebFarmSvc.aaddscontoso.com `
    -Path "OU=MYNEWOU,DC=aaddscontoso,DC=com" `
    -KerberosEncryptionType AES128, AES256 `
    -ManagedPasswordIntervalInDays 30 `
    -ServicePrincipalNames http/WebFarmSvc.aaddscontoso.com/aaddscontoso.com, `
        http/WebFarmSvc.aaddscontoso.com/aaddscontoso, `
        http/WebFarmSvc/aaddscontoso.com, `
        http/WebFarmSvc/aaddscontoso `
    -PrincipalsAllowedToRetrieveManagedPassword AADDSCONTOSO-SERVER$
```

Program och tjänster kan nu konfigureras för att använda gMSA vid behov.

## <a name="next-steps"></a>Nästa steg

Mer information om gMSAs finns i [komma igång med grupphanterade tjänst konton][gmsa-start].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[create-custom-ou]: create-ou.md

<!-- EXTERNAL LINKS -->
[New-ADOrganizationalUnit]: /powershell/module/addsadministration/New-AdOrganizationalUnit
[New-ADServiceAccount]: /powershell/module/addsadministration/New-AdServiceAccount
[gmsa-overview]: /windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview
[gmsa-start]: /windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts
