---
title: Aktivera hash-synkronisering av lösen ord för Azure AD Domain Services | Microsoft Docs
description: I den här självstudien får du lära dig hur du aktiverar hash-synkronisering av lösen ord med Azure AD Connect till en Azure Active Directory Domain Services hanterad domän.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: e83d8941d1be7fd36f53a881a21716252ad01954
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618356"
---
# <a name="tutorial-enable-password-synchronization-in-azure-active-directory-domain-services-for-hybrid-environments"></a>Självstudie: Aktivera Lösenordssynkronisering i Azure Active Directory Domain Services för Hybrid miljöer

I hybrid miljöer kan en Azure Active Directory (Azure AD)-klient konfigureras för att synkroniseras med en lokal Active Directory Domain Services (AD DS)-miljö med hjälp av Azure AD Connect. Som standard synkroniserar Azure AD Connect inte äldre NTLM (NT LAN Manager) och Kerberos-hashvärden för lösen ord som behövs för Azure Active Directory Domain Services (Azure AD DS).

Om du vill använda Azure AD DS med konton som är synkroniserade från en lokal AD DS-miljö måste du konfigurera Azure AD Connect för att synkronisera de lösen ords-hashar som krävs för NTLM-och Kerberos-autentisering. När Azure AD Connect har kon figurer ATS synkroniseras även en tjänst för att skapa ett lokalt konto eller lösen ords ändring.

Du behöver inte utföra de här stegen om du använder moln konton utan någon lokal AD DS-miljö eller om du använder en *resurs skog*. För hanterade domäner som använder en resurs skog synkroniseras aldrig lokala lösenords-hashvärden. Autentisering för lokala konton använder skogs förtroenden (s) på dina egna AD DS-domänkontrollanter.

I den här självstudien får du lära dig:

> [!div class="checklist"]
> * Varför behövs inte hashar för äldre NTLM-och Kerberos-lösenord
> * Konfigurera äldre hash-synkronisering för lösen ord för Azure AD Connect

Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du följande resurser:

* En aktiv Azure-prenumeration.
    * [Skapa ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)om du inte har någon Azure-prenumeration.
* En Azure Active Directory klient som är associerad med din prenumeration som är synkroniserad med en lokal katalog som använder Azure AD Connect.
    * Om det behövs kan du [skapa en Azure Active Directory klient][create-azure-ad-tenant] eller [associera en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
    * Om det behövs [aktiverar Azure AD Connect för synkronisering av lösen ords-hash][enable-azure-ad-connect].
* En Azure Active Directory Domain Services hanterad domän aktive rad och konfigurerad i Azure AD-klienten.
    * Om det behövs kan du [skapa och konfigurera en Azure Active Directory Domain Services hanterad domän][create-azure-ad-ds-instance].

## <a name="password-hash-synchronization-using-azure-ad-connect"></a>Hash-synkronisering av lösen ord med Azure AD Connect

Azure AD Connect används för att synkronisera objekt som användar konton och grupper från en lokal AD DS-miljö till en Azure AD-klient. Som en del av processen aktiverar hash-synkronisering av lösen ord konton att använda samma lösen ord i AD DS-miljön lokal och Azure AD.

För att autentisera användare på den hanterade domänen behöver Azure AD DS lösen ords-hashar i ett format som är lämpligt för NTLM-och Kerberos-autentisering. Azure AD lagrar inte lösen ordets hash-värden i det format som krävs för NTLM eller Kerberos-autentisering förrän du aktiverar Azure AD DS för din klient. Av säkerhets skäl lagrar Azure AD inte heller lösen ords referenser i klartext-format. Därför kan inte Azure AD automatiskt generera dessa NTLM-eller Kerberos-hashvärden utifrån användarnas befintliga autentiseringsuppgifter.

Azure AD Connect kan konfigureras för att synkronisera de obligatoriska NTLM-eller Kerberos-hashvärden för Azure AD DS. Se till att du har slutfört stegen för att [aktivera Azure AD Connect för synkronisering av lösen ords-hash][enable-azure-ad-connect]. Om du har en befintlig instans av Azure AD Connect [hämtar och uppdaterar du till den senaste versionen][azure-ad-connect-download] för att se till att du kan synkronisera äldre hashvärden för lösen ord för NTLM och Kerberos. Den här funktionen är inte tillgänglig i tidiga versioner av Azure AD Connect eller med det äldre DirSync-verktyget. Azure AD Connect version *1.1.614.0* eller senare krävs.

> [!IMPORTANT]
> Azure AD Connect bör endast installeras och konfigureras för synkronisering med lokala AD DS-miljöer. Det finns inte stöd för att installera Azure AD Connect i en Azure AD DS-hanterad domän för att synkronisera objekt tillbaka till Azure AD.

## <a name="enable-synchronization-of-password-hashes"></a>Aktivera synkronisering av lösen ords-hashar

När Azure AD Connect installerats och kon figurer ATS för synkronisering med Azure AD konfigurerar du nu den äldre hash-synkroniseringen för lösen ord för NTLM och Kerberos. Ett PowerShell-skript används för att konfigurera de nödvändiga inställningarna och sedan starta en fullständig Lösenordssynkronisering till Azure AD. När den Azure AD Connect synkroniseringen av lösen ord för hash är klar kan användarna logga in på program via Azure AD DS som använder äldre NTLM-eller Kerberos-hashvärden.

1. På datorn med Azure AD Connect installerat går du till Start-menyn och öppnar **Azure AD Connect >-synkroniseringstjänsten**.
1. Välj fliken **anslutningar** . Anslutnings informationen som används för att upprätta synkroniseringen mellan den lokala AD DS-miljön och Azure AD visas.

    **Typen** anger antingen *Windows Azure Active Directory (Microsoft)* för Azure AD-anslutningen eller *Active Directory Domain Services* för den lokala AD DS-anslutningen. Anteckna de kopplings namn som ska användas i PowerShell-skriptet i nästa steg.

    ![Lista de anslutnings namn som är synkroniserade Service Manager](media/tutorial-configure-password-hash-sync/service-sync-manager.png)

    I det här exemplet på skärm bilden används följande kopplingar:

    * Azure AD-anslutningen heter *contoso.onmicrosoft.com-AAD*
    * Den lokala AD DS-anslutningen heter *OnPrem.contoso.com*

1. Kopiera och klistra in följande PowerShell-skript till datorn med Azure AD Connect installerat. Skriptet utlöser en fullständig lösen ords synkronisering som innehåller äldre hashvärden för lösen ord. Uppdatera `$azureadConnector` `$adConnector` variablerna och med kopplings namnen från föregående steg.

    Kör det här skriptet på varje AD-skog för att synkronisera lokala konton NTLM och Kerberos-hashvärden för lösen ord till Azure AD.

    ```powershell
    # Define the Azure AD Connect connector names and import the required PowerShell module
    $azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"
    $adConnector = "<CASE SENSITIVE AD DS CONNECTOR NAME>"
    
    Import-Module "C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync\ADSync.psd1"
    Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1"

    # Create a new ForceFullPasswordSync configuration parameter object then
    # update the existing connector with this new configuration
    $c = Get-ADSyncConnector -Name $adConnector
    $p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
    $p.Value = 1
    $c.GlobalParameters.Remove($p.Name)
    $c.GlobalParameters.Add($p)
    $c = Add-ADSyncConnector -Connector $c

    # Disable and re-enable Azure AD Connect to force a full password synchronization
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true
    ```

    Beroende på storleken på din katalog med avseende på antalet konton och grupper kan synkroniseringen av de äldre lösen ords hasharna till Azure AD ta en stund. Lösen orden synkroniseras sedan till den hanterade domänen när de har synkroniserats till Azure AD.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig:

> [!div class="checklist"]
> * Varför behövs inte hashar för äldre NTLM-och Kerberos-lösenord
> * Konfigurera äldre hash-synkronisering för lösen ord för Azure AD Connect

> [!div class="nextstepaction"]
> [Lär dig hur synkronisering fungerar i en Azure AD Domain Services hanterad domän](synchronization.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-azure-ad-connect]: ../active-directory/hybrid/how-to-connect-install-express.md

<!-- EXTERNAL LINKS -->
[azure-ad-connect-download]: https://www.microsoft.com/download/details.aspx?id=47594
