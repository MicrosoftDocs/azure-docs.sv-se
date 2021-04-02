---
title: Distribuera Azure-AD-programproxy för Azure AD Domain Services | Microsoft Docs
description: Lär dig hur du ger säker åtkomst till interna program för fjärranslutna arbetare genom att distribuera och konfigurera Azure Active Directory-programproxy i en Azure Active Directory Domain Services hanterad domän
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 8305b2190cf3b157973f5844c3237ffe73adba66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619924"
---
# <a name="deploy-azure-ad-application-proxy-for-secure-access-to-internal-applications-in-an-azure-active-directory-domain-services-managed-domain"></a>Distribuera Azure AD-programproxy för säker åtkomst till interna program i en Azure Active Directory Domain Services hanterad domän

Med Azure AD Domain Services (Azure AD DS) kan du lyfta och byta äldre program som körs lokalt i Azure. Azure Active Directory (AD) Application Proxy hjälper dig att stödja fjärran vändare genom att publicera de interna programmen på ett säkert sätt i en Azure AD DS-hanterad domän så att de kan nås via Internet.

Om du är nybörjare på Azure-AD-programproxy och vill veta mer kan du läsa om [hur du ger säker fjärråtkomst till interna program](../active-directory/manage-apps/application-proxy.md).

Den här artikeln visar hur du skapar och konfigurerar en Azure AD-programproxy-anslutning för att ge säker åtkomst till program i en hanterad domän.

## <a name="before-you-begin"></a>Innan du börjar

För att slutföra den här artikeln behöver du följande resurser och behörigheter:

* En aktiv Azure-prenumeration.
    * [Skapa ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)om du inte har någon Azure-prenumeration.
* En Azure Active Directory klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog som endast är moln.
    * Om det behövs kan du [skapa en Azure Active Directory klient][create-azure-ad-tenant] eller [associera en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
    * En **Azure AD Premium-licens** krävs för att använda Azure-AD-programproxy.
* En Azure Active Directory Domain Services hanterad domän aktive rad och konfigurerad i Azure AD-klienten.
    * Om det behövs kan du [skapa och konfigurera en Azure Active Directory Domain Services hanterad domän][create-azure-ad-ds-instance].

## <a name="create-a-domain-joined-windows-vm"></a>Skapa en domänansluten virtuell Windows-dator

För att dirigera trafik till program som körs i din miljö installerar du Azure AD-programproxy Connector-komponenten. Den här Azure AD-programproxy-anslutningen måste installeras på en virtuell Windows Server-dator (VM) som är ansluten till den hanterade domänen. För vissa program kan du distribuera flera servrar där anslutningen är installerad. Det här distributions alternativet ger dig större tillgänglighet och hjälper till att hantera kraftigare autentiserings belastningar.

Den virtuella datorn som kör Azure AD-programproxy-anslutningen måste finnas på samma eller ett peer-kopplat virtuellt nätverk som din hanterade domän. De virtuella datorerna som sedan är värdar för de program som du publicerar med programproxyn måste också distribueras på samma virtuella Azure-nätverk.

Utför följande steg för att skapa en virtuell dator för Azure AD-programproxy-anslutningen:

1. [Skapa en anpassad Organisationsenhet](create-ou.md). Du kan delegera behörigheter för att hantera den här anpassade ORGANISATIONSENHETen till användare i den hanterade domänen. De virtuella datorerna för Azure AD-programproxy och som kör dina program måste vara en del av den anpassade ORGANISATIONSENHETen, inte som standard organisationsenhet för *AAD DC-datorer* .
1. [Domän – Anslut till de virtuella datorerna][create-join-windows-vm], både den som kör Azure AD-programproxy-anslutningen och de som kör dina program till den hanterade domänen. Skapa dessa dator konton i den anpassade ORGANISATIONSENHETen från föregående steg.

## <a name="download-the-azure-ad-application-proxy-connector"></a>Ladda ned Azure AD-programproxy-anslutningen

Utför följande steg för att ladda ned Azure AD-programproxy-anslutningen. Installations filen som du hämtar kopieras till din App proxy-VM i nästa avsnitt.

1. Logga in på [Azure Portal](https://portal.azure.com) med ett användar konto som har *företags administratörs* behörighet i Azure AD.
1. Sök efter och välj **Azure Active Directory** överst i portalen och välj sedan **företags program**.
1. Välj **Application Proxy** på menyn till vänster. Om du vill skapa din första anslutning och aktivera App proxy, väljer du länken för att **Ladda ned en koppling**.
1. Godkänn licens villkoren och sekretess avtalet på sidan hämtning och välj sedan **acceptera villkor & hämtning**.

    ![Ladda ned Azure AD App proxy Connector](./media/app-proxy/download-app-proxy-connector.png)

## <a name="install-and-register-the-azure-ad-application-proxy-connector"></a>Installera och registrera Azure AD-programproxy-anslutningen

När du har en virtuell dator som är redo att användas som Azure AD-programproxy-anslutning kopierar du och kör installations filen som hämtats från Azure Portal.

1. Kopiera installations filen för Azure AD-programproxy Connector till den virtuella datorn.
1. Kör installations filen, t. ex. *AADApplicationProxyConnectorInstaller.exe*. Godkänn licens villkoren för program varan.
1. Under installationen uppmanas du att registrera anslutnings programmet med programproxyn i Azure AD-katalogen.
   * Ange autentiseringsuppgifterna för en global administratör i Azure AD-katalogen. Autentiseringsuppgifterna för global administratör i Azure AD kan skilja sig från dina Azure-autentiseringsuppgifter på portalen

        > [!NOTE]
        > Det globala administratörs kontot som används för att registrera anslutningen måste tillhöra samma katalog där du aktiverar Application Proxy-tjänsten.
        >
        > Om till exempel Azure AD-domänen är *contoso.com* bör den globala administratören vara `admin@contoso.com` eller ett annat giltigt alias i domänen.

   * Om förbättrad säkerhets konfiguration i Internet Explorer är aktiverat för den virtuella datorn där du installerar anslutningen, kan registrerings skärmen blockeras. Om du vill tillåta åtkomst följer du anvisningarna i fel meddelandet eller stänger av förbättrad säkerhet i Internet Explorer under installationen.
   * Om anslutnings registreringen Miss lyckas, se [Felsöka programproxyn](../active-directory/manage-apps/application-proxy-troubleshoot.md).
1. I slutet av installationen visas en anteckning för miljöer med en utgående proxy. Om du vill konfigurera Azure AD-programproxy-anslutningen så att den fungerar via den utgående proxyn kör du det tillhandahållna skriptet, till exempel `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1` .
1. På sidan Application Proxy i Azure Portal visas den nya anslutningen med statusen *aktiv*, som visas i följande exempel:

    ![Den nya Azure AD-programproxy-anslutningen visas som aktiv i Azure Portal](./media/app-proxy/connected-app-proxy.png)

> [!NOTE]
> Om du vill ge hög tillgänglighet för program som autentiseras via Azure-AD-programproxy kan du installera anslutningar på flera virtuella datorer. Upprepa samma steg som i föregående avsnitt för att installera anslutningen på andra servrar som är anslutna till den hanterade domänen.

## <a name="enable-resource-based-kerberos-constrained-delegation"></a>Aktivera resurs baserad Kerberos-begränsad delegering

Om du vill använda enkel inloggning till dina program med hjälp av integrerad Windows-autentisering (IWA) ger du Azure AD-programproxy Connectors behörighet att personifiera användare och skicka och ta emot token för deras räkning. Om du vill bevilja dessa behörigheter konfigurerar du Kerberos-begränsad delegering (KCD) för anslutnings tjänsten för att få åtkomst till resurser på den hanterade domänen. Eftersom du inte har domän administratörs behörighet i en hanterad domän, kan inte traditionella KCD på konto nivå konfigureras på en hanterad domän. Använd i stället Resource based KCD.

Mer information finns i [Konfigurera Kerberos-begränsad delegering (KCD) i Azure Active Directory Domain Services](deploy-kcd.md).

> [!NOTE]
> Du måste vara inloggad på ett användar konto som är medlem i gruppen *Azure AD DC-administratörer* i din Azure AD-klient för att köra följande PowerShell-cmdletar.
>
> Dator kontona för din App proxy Connector-VM och virtuella program måste finnas i en anpassad ORGANISATIONSENHET där du har behörighet att konfigurera resursbaserade KCD. Du kan inte konfigurera resursbaserade KCD för ett dator konto i den inbyggda behållaren för *AAD DC-datorer* .

Använd [Get-ADComputer][Get-ADComputer] för att hämta inställningarna för den dator där Azure AD-programproxy Connector är installerat. Kör följande cmdlets från din domänanslutna hanterings-VM och inloggad som användar konto som är medlem i gruppen *Azure AD DC-administratörer* .

I följande exempel får du information om dator kontot som heter *appproxy.aaddscontoso.com*. Ange ett eget dator namn för den virtuella Azure AD-programproxy-datorn som har kon figurer ATS i föregående steg.

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity appproxy.aaddscontoso.com
```

För varje program server som kör apparna bakom Azure AD-programproxy använder du PowerShell-cmdleten [set-ADComputer][Set-ADComputer] för att konfigurera resursbaserade KCD. I följande exempel beviljas Azure AD-programproxy-anslutningen behörighet att använda den *appserver.aaddscontoso.com* datorn:

```powershell
Set-ADComputer appserver.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

Om du distribuerar flera Azure AD-programproxy-kopplingar måste du konfigurera resursbaserade KCD för varje anslutnings instans.

## <a name="next-steps"></a>Nästa steg

Med Azure AD-programproxy integrerat med Azure AD DS kan du publicera program som användare kan komma åt. Mer information finns i [Publicera program med hjälp av Azure AD-programproxy](../active-directory/manage-apps/application-proxy-add-on-premises-application.md).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[Get-ADComputer]: /powershell/module/addsadministration/get-adcomputer
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer