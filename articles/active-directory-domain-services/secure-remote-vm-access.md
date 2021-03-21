---
title: Skydda VM-fjärråtkomst i Azure AD Domain Services | Microsoft Docs
description: Lär dig hur du skyddar fjärråtkomst till virtuella datorer med hjälp av nätverks princip Server (NPS) och Azure AD Multi-Factor Authentication med en Fjärrskrivbordstjänster distribution i en Azure Active Directory Domain Services hanterad domän.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: f0605cbd81d8131014a1f6a6bf30e3db0ce9aa90
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96618934"
---
# <a name="secure-remote-access-to-virtual-machines-in-azure-active-directory-domain-services"></a>Säker fjärråtkomst till virtuella datorer i Azure Active Directory Domain Services

Om du vill skydda fjärråtkomst till virtuella datorer (VM) som körs i en Azure Active Directory Domain Services (Azure AD DS)-hanterad domän, kan du använda Fjärrskrivbordstjänster (RDS) och nätverks princip servern (NPS). Azure AD DS autentiserar användare när de begär åtkomst via RDS-miljön. För ökad säkerhet kan du integrera Azure AD-Multi-Factor Authentication för att tillhandahålla en ytterligare autentiserings-prompt under inloggnings händelser. Azure AD Multi-Factor Authentication använder ett tillägg för NPS för att tillhandahålla den här funktionen.

> [!IMPORTANT]
> Det rekommenderade sättet att ansluta till dina virtuella datorer på ett säkert sätt i en Azure AD DS-hanterad domän är att använda Azure-skydds, en fullständigt plattforms hanterad PaaS-tjänst som du etablerar i det virtuella nätverket. En skydds-värd ger säker och smidig Remote Desktop Protocol (RDP)-anslutning till dina virtuella datorer direkt i Azure Portal via SSL. När du ansluter via en skydds-värd behöver inte de virtuella datorerna någon offentlig IP-adress, och du behöver inte använda nätverks säkerhets grupper för att exponera åtkomst till RDP på TCP-port 3389.
>
> Vi rekommenderar starkt att du använder Azure skydds i alla regioner där det stöds. I regioner utan Azure skydds-tillgänglighet följer du stegen som beskrivs i den här artikeln tills Azure skydds är tillgängligt. Ta hand om att tilldela offentliga IP-adresser till virtuella datorer som är anslutna till Azure AD DS där all inkommande RDP-trafik tillåts.
>
> Mer information finns i [Vad är Azure skydds?][bastion-overview].

Den här artikeln visar hur du konfigurerar RDS i Azure AD DS och kan använda Azure AD Multi-Factor Authentication NPS-tillägget.

![Översikt över Fjärrskrivbordstjänster (RDS)](./media/enable-network-policy-server/remote-desktop-services-overview.png)

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här artikeln behöver du följande resurser:

* En aktiv Azure-prenumeration.
    * [Skapa ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)om du inte har någon Azure-prenumeration.
* En Azure Active Directory klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog som endast är moln.
    * Om det behövs kan du [skapa en Azure Active Directory klient][create-azure-ad-tenant] eller [associera en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En Azure Active Directory Domain Services hanterad domän aktive rad och konfigurerad i Azure AD-klienten.
    * Om det behövs kan du [skapa och konfigurera en Azure Active Directory Domain Services hanterad domän][create-azure-ad-ds-instance].
* Ett *arbets belastnings* under nät som skapats i ditt Azure Active Directory Domain Services virtuella nätverk.
    * Om det behövs [konfigurerar du virtuella nätverk för en Azure Active Directory Domain Services hanterad domän][configure-azureadds-vnet].
* Ett användar konto som är medlem i *Administratörs gruppen för Azure AD DC* i din Azure AD-klient.

## <a name="deploy-and-configure-the-remote-desktop-environment"></a>Distribuera och konfigurera fjärr skrivbords miljön

Kom igång genom att skapa minst två virtuella Azure-datorer som kör Windows Server 2016 eller Windows Server 2019. För redundans och hög tillgänglighet för din fjärr skrivbords miljö kan du lägga till och belastningsutjämna ytterligare värdar senare.

En rekommenderad RDS-distribution innehåller följande två virtuella datorer:

* *RDGVM01* – kör Server för anslutnings utjämning för fjärr skrivbord, servern för webb åtkomst för fjärr skrivbord och RD Gateway-servern.
* *RDSHVM01* – kör värd servern för fjärrskrivbordssession.

Se till att de virtuella datorerna distribueras till ett *arbets belastnings* under nät för ditt virtuella Azure AD DS-nätverk och Anslut sedan de virtuella datorerna till den hanterade domänen. Mer information finns i så här [skapar och ansluter du en virtuell Windows Server-dator till en hanterad domän][tutorial-create-join-vm].

Distributionen av fjärr skrivbords miljön innehåller ett antal steg. Den befintliga distributions guiden för fjärr skrivbord kan användas utan några speciella ändringar som ska användas i en hanterad domän:

1. Logga in på virtuella datorer som har skapats för RD-miljön med ett konto som är en del av *Administratörs gruppen för Azure AD DC* , till exempel *contosoadmin*.
1. Om du vill skapa och konfigurera RDS använder du den befintliga [distributions guiden för fjärr skrivbords miljö][deploy-remote-desktop]. Distribuera Server komponenterna för fjärr skrivbord i dina virtuella Azure-datorer efter behov.
    * Specifika för Azure AD DS – när du konfigurerar fjärr skrivbords licensiering ska du ställa in det på **per enhets** läge, inte **per användare** som anges i distributions guiden.
1. Om du vill ge åtkomst med hjälp av en webbläsare [konfigurerar du webb klienten för fjärr skrivbord för dina användare][rd-web-client].

Med RD distribuerad till den hanterade domänen kan du hantera och använda tjänsten på samma sätt som med en lokal AD DS-domän.

## <a name="deploy-and-configure-nps-and-the-azure-ad-mfa-nps-extension"></a>Distribuera och konfigurera NPS och Azure AD MFA NPS-tillägget

Om du vill öka säkerheten för användar inloggningen kan du välja att integrera RD-miljön med Azure AD Multi-Factor Authentication. Med den här konfigurationen får användarna en extra fråga under inloggningen för att bekräfta sin identitet.

För att tillhandahålla den här funktionen installeras en ytterligare nätverks princip Server (NPS) i din miljö tillsammans med Azure AD Multi-Factor Authentication NPS-tillägget. Det här tillägget integreras med Azure AD för att begära och returnera statusen för Multi-Factor Authentication-prompter.

Användare måste vara [registrerade för att använda Azure ad Multi-Factor Authentication][user-mfa-registration], vilket kan kräva ytterligare Azure AD-licenser.

Om du vill integrera Azure AD Multi-Factor Authentication i din Azure AD DS-fjärr skrivbords miljö skapar du en NPS-server och installerar tillägget:

1. Skapa ytterligare en virtuell Windows Server 2016-eller 2019-dator, till exempel *NPSVM01*, som är ansluten till ett under nätverk för *arbets belastningar* i ditt virtuella Azure AD DS-nätverk. Anslut den virtuella datorn till den hanterade domänen.
1. Logga in på NPS VM som-konto som är en del av gruppen *Azure AD DC-administratörer* , till exempel *contosoadmin*.
1. Från **Serverhanteraren** väljer du **Lägg till roller och funktioner** och installerar sedan rollen *nätverks policy och åtkomst tjänster* .
1. Använd den befintliga instruktions artikeln för att [Installera och konfigurera Azure AD MFA NPS-tillägget][nps-extension].

När NPS-servern och Azure AD Multi-Factor Authentication NPS-tillägget är installerade, slutför du nästa avsnitt för att konfigurera det för användning med RD-miljön.

## <a name="integrate-remote-desktop-gateway-and-azure-ad-multi-factor-authentication"></a>Integrera Fjärrskrivbordsgateway och Azure AD Multi-Factor Authentication

Om du vill integrera Azure AD Multi-Factor Authentication NPS-tillägget använder du den befintliga instruktions artikeln för att [integrera din infrastruktur för fjärr skrivbords anslutning med hjälp av NPS-tillägget (Network Policy Server) och Azure AD][azure-mfa-nps-integration].

Följande ytterligare konfigurations alternativ krävs för att integrera med en hanterad domän:

1. [Registrera inte NPS-servern i Active Directory][register-nps-ad]. Det här steget fungerar inte i en hanterad domän.
1. I [steg 4 konfigurerar du nätverks principen][create-nps-policy], markerar kryss rutan för att **Ignorera egenskaper för användar kontots uppringning**.
1. Om du använder Windows Server 2019 för NPS-servern och Azure AD Multi-Factor Authentication NPS-tillägget kör du följande kommando för att uppdatera den säkra kanalen så att NPS-servern kan kommunicera korrekt:

    ```powershell
    sc sidtype IAS unrestricted
    ```

Användarna uppmanas nu att ange ytterligare en autentiseringsnivå när de loggar in, till exempel ett textmeddelande eller en fråga i Microsoft Authenticator-appen.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du kan förbättra återhämtningen av distributionen finns i [Fjärrskrivbordstjänster-hög tillgänglighet][rds-high-availability].

Mer information om hur du skyddar användar inloggning finns i så här [fungerar det: Azure AD Multi-Factor Authentication][concepts-mfa].

<!-- INTERNAL LINKS -->
[bastion-overview]: ../bastion/bastion-overview.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[configure-azureadds-vnet]: tutorial-configure-networking.md
[tutorial-create-join-vm]: join-windows-vm.md
[user-mfa-registration]: ../active-directory/authentication/howto-mfa-nps-extension.md#register-users-for-mfa
[nps-extension]: ../active-directory/authentication/howto-mfa-nps-extension.md
[azure-mfa-nps-integration]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md
[register-nps-ad]:../active-directory/authentication/howto-mfa-nps-extension-rdg.md#register-server-in-active-directory
[create-nps-policy]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md#configure-network-policy
[concepts-mfa]: ../active-directory/authentication/concept-mfa-howitworks.md

<!-- EXTERNAL LINKS -->
[deploy-remote-desktop]: /windows-server/remote/remote-desktop-services/rds-deploy-infrastructure
[rd-web-client]: /windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client-admin
[rds-high-availability]: /windows-server/remote/remote-desktop-services/rds-plan-high-availability