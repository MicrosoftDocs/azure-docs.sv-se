---
title: Kerberos-begränsad delegering för Azure AD Domain Services | Microsoft Docs
description: Lär dig hur du aktiverar resurs baserad Kerberos-begränsad delegering (KCD) i en Azure Active Directory Domain Services hanterad domän.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 138b90a33ff1dbc4b014f17fa0098112e1da66e4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619784"
---
# <a name="configure-kerberos-constrained-delegation-kcd-in-azure-active-directory-domain-services"></a>Konfigurera Kerberos-begränsad delegering (KCD) i Azure Active Directory Domain Services

När du kör program kan dessa program behöva åtkomst till resurser i kontexten för en annan användare. Active Directory Domain Services (AD DS) stöder en mekanism som kallas *Kerberos-delegering* som möjliggör det här användnings fallet. Kerberos- *begränsad* delegering (KCD) bygger på den här mekanismen för att definiera specifika resurser som kan nås i användarens kontext.

Azure Active Directory Domain Services (Azure AD DS) hanterade domäner är säkrare att låsas än traditionella lokala AD DS-miljöer, så Använd en säkrare *resurs baserad* KCD.

Den här artikeln visar hur du konfigurerar resurs-baserad Kerberos-begränsad delegering i en Azure AD DS-hanterad domän.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här artikeln behöver du följande resurser:

* En aktiv Azure-prenumeration.
    * [Skapa ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)om du inte har någon Azure-prenumeration.
* En Azure Active Directory klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog som endast är moln.
    * Om det behövs kan du [skapa en Azure Active Directory klient][create-azure-ad-tenant] eller [associera en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En Azure Active Directory Domain Services hanterad domän aktive rad och konfigurerad i Azure AD-klienten.
    * Om det behövs kan du [skapa och konfigurera en Azure Active Directory Domain Services hanterad domän][create-azure-ad-ds-instance].
* En virtuell Windows Server Management-dator som är ansluten till den hanterade Azure AD DS-domänen.
    * Om det behövs kan du slutföra självstudien för att [skapa en virtuell Windows Server-dator och ansluta den till en hanterad domän och][create-join-windows-vm] sedan [Installera hanterings verktygen för AD DS][tutorial-create-management-vm].
* Ett användar konto som är medlem i *Administratörs gruppen för Azure AD DC* i din Azure AD-klient.

## <a name="kerberos-constrained-delegation-overview"></a>Översikt över Kerberos-begränsad delegering

Med Kerberos-delegering kan ett konto personifiera ett annat konto för åtkomst till resurser. Till exempel kan ett webb program som har åtkomst till en server dels webb komponent personifiera sig själv som ett annat användar konto när den gör backend-anslutningen. Kerberos-delegering är inte säkert eftersom det inte begränsar vilka resurser som kontot som personifierar har åtkomst till.

Kerberos- *begränsad* delegering (KCD) begränsar de tjänster eller resurser som en angiven server eller ett visst program kan ansluta vid personifiering av en annan identitet. Traditionella KCD kräver domän administratörs behörighet för att konfigurera ett domän konto för en tjänst och det begränsar kontot som ska köras på en enda domän.

Traditionella KCD har också några problem. I tidigare operativ system hade tjänst administratören till exempel inget användbart sätt att veta vilka front-end-tjänster som delegerats till de resurs tjänster som de äger. Alla frontend-tjänster som kan delegera till en resurs tjänst var en potentiell angrepps punkt. Om en server som är värd för en frontend-tjänst som kon figurer ATS för att delegera till resurs tjänster komprometterades, kunde resurs tjänsterna också komprometteras.

I en hanterad domän har du inte domän administratörs behörighet. Därför kan traditionella kontobaserade KCD inte konfigureras i en hanterad domän. Resursbaserade KCD kan i stället användas, vilket också är säkrare.

### <a name="resource-based-kcd"></a>Resursbaserade KCD

Med Windows Server 2012 och senare får tjänst administratörer möjlighet att konfigurera begränsad delegering för deras tjänster. Den här modellen kallas för resursbaserade KCD. Med den här metoden kan Server administratören för Server delen tillåta eller neka särskilda klient dels tjänster från att använda KCD.

Resource-baserad KCD har kon figurer ATS med PowerShell. Du använder cmdletarna [set-ADComputer][Set-ADComputer] eller [set-ADUser][Set-ADUser] , beroende på om det Personifierande kontot är ett dator konto eller ett användar konto/tjänst konto.

## <a name="configure-resource-based-kcd-for-a-computer-account"></a>Konfigurera resursbaserade KCD för ett dator konto

I det här scenariot antar vi att du har en webbapp som körs på datorn med namnet *contoso-webapp.aaddscontoso.com*.

Webb programmet måste ha åtkomst till ett webb-API som körs på datorn med namnet *contoso-API.aaddscontoso.com* i kontexten för domän användare.

Utför följande steg för att konfigurera det här scenariot:

1. [Skapa en anpassad Organisationsenhet](create-ou.md). Du kan delegera behörigheter för att hantera den här anpassade ORGANISATIONSENHETen till användare i den hanterade domänen.
1. [Domän – Anslut de virtuella datorerna][create-join-windows-vm], både den som kör webbappen och den som kör webb-API: t till den hanterade domänen. Skapa dessa dator konton i den anpassade ORGANISATIONSENHETen från föregående steg.

    > [!NOTE]
    > Dator kontona för webbappen och webb-API: et måste vara i en anpassad ORGANISATIONSENHET där du har behörighet att konfigurera resursbaserade KCD. Du kan inte konfigurera resursbaserade KCD för ett dator konto i den inbyggda behållaren för *AAD DC-datorer* .

1. Konfigurera därefter resursbaserade KCD med PowerShell-cmdleten [set-ADComputer][Set-ADComputer] .

    Kör följande cmdlets från din domänanslutna hanterings-VM och inloggad som användar konto som är medlem i gruppen *Azure AD DC-administratörer* . Ange dina egna dator namn efter behov:
    
    ```powershell
    $ImpersonatingAccount = Get-ADComputer -Identity contoso-webapp.aaddscontoso.com
    Set-ADComputer contoso-api.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="configure-resource-based-kcd-for-a-user-account"></a>Konfigurera resursbaserade KCD för ett användar konto

I det här scenariot antar vi att du har en webbapp som körs som ett tjänst konto med namnet *appsvc*. Webb programmet måste ha åtkomst till ett webb-API som körs som ett tjänst konto med namnet *backendsvc* i kontexten för domän användare. Utför följande steg för att konfigurera det här scenariot:

1. [Skapa en anpassad Organisationsenhet](create-ou.md). Du kan delegera behörigheter för att hantera den här anpassade ORGANISATIONSENHETen till användare i den hanterade domänen.
1. [Domän – Anslut de virtuella datorerna][create-join-windows-vm] som kör Server dels webb-API: n/resursen till den hanterade domänen. Skapa sitt dator konto inom den anpassade ORGANISATIONSENHETen.
1. Skapa tjänst kontot (till exempel *appsvc*) som används för att köra webbappen i den anpassade organisationsenheten.

    > [!NOTE]
    > På nytt måste dator kontot för den virtuella webb-API: n och tjänst kontot för webbappen vara i en anpassad ORGANISATIONSENHET där du har behörighet att konfigurera resursbaserade KCD. Du kan inte konfigurera resursbaserade KCD för konton på de inbyggda *AAD DC-datorerna* eller *AAD DC Users* -behållare. Det innebär också att du inte kan använda användar konton som synkroniseras från Azure AD för att konfigurera resursbaserade KCD. Du måste skapa och använda tjänst konton som skapats i Azure AD DS.

1. Konfigurera därefter resursbaserade KCD med PowerShell-cmdleten [set-ADUser][Set-ADUser] .

    Kör följande cmdlets från din domänanslutna hanterings-VM och inloggad som användar konto som är medlem i gruppen *Azure AD DC-administratörer* . Ange dina egna tjänst namn efter behov:

    ```powershell
    $ImpersonatingAccount = Get-ADUser -Identity appsvc
    Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="next-steps"></a>Nästa steg

Mer information om hur delegering fungerar i Active Directory Domain Services finns i [Översikt över Kerberos-begränsad delegering][kcd-technet].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
[Set-ADUser]: /powershell/module/addsadministration/set-aduser

<!-- EXTERNAL LINKS -->
[kcd-technet]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11)