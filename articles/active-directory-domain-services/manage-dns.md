---
title: Hantera DNS för Azure AD Domain Services | Microsoft Docs
description: Lär dig hur du installerar DNS-serverns verktyg för att hantera DNS och skapa villkorliga vidarebefordrare för en Azure Active Directory Domain Services hanterad domän.
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: afa6920a36a5a7218571239b36815004d8f2d450
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619359"
---
# <a name="administer-dns-and-create-conditional-forwarders-in-an-azure-active-directory-domain-services-managed-domain"></a>Administrera DNS och skapa villkorliga vidarebefordrare i en Azure Active Directory Domain Services hanterad domän

I Azure Active Directory Domain Services (Azure AD DS) är en nyckel komponent DNS (domän namns matchning). Azure AD DS innehåller en DNS-server som tillhandahåller namn matchning för den hanterade domänen. Den här DNS-servern innehåller inbyggda DNS-poster och uppdateringar för de viktiga komponenter som gör att tjänsten kan köras.

När du kör dina egna program och tjänster kan du behöva skapa DNS-poster för datorer som inte är anslutna till domänen, konfigurera virtuella IP-adresser för belastningsutjämnare eller konfigurera externa DNS-vidarebefordrare. Användare som tillhör gruppen *AAD DC-administratörer* beviljas behörighet för DNS-administration på den hanterade domänen i Azure AD DS och kan skapa och redigera anpassade DNS-poster.

I en hybrid miljö synkroniseras inte DNS-zoner och-poster som kon figurer ATS i andra DNS-namnområden, till exempel en lokal AD DS-miljö, till den hanterade domänen. Om du vill matcha namngivna resurser i andra DNS-namnområden skapar du och använder villkorliga vidarebefordrare som pekar på befintliga DNS-servrar i din miljö.

Den här artikeln visar hur du installerar verktyg för DNS-server och sedan använder DNS-konsolen för att hantera poster och skapa villkorliga vidarebefordrare i Azure AD DS.

## <a name="before-you-begin"></a>Innan du börjar

För att slutföra den här artikeln behöver du följande resurser och behörigheter:

* En aktiv Azure-prenumeration.
    * [Skapa ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)om du inte har någon Azure-prenumeration.
* En Azure Active Directory klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog som endast är moln.
    * Om det behövs kan du [skapa en Azure Active Directory klient][create-azure-ad-tenant] eller [associera en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En Azure Active Directory Domain Services hanterad domän aktive rad och konfigurerad i Azure AD-klienten.
    * Om det behövs, slutför du själv studie kursen för att [skapa och konfigurera en Azure Active Directory Domain Services hanterad domän][create-azure-ad-ds-instance].
* Anslutning från det virtuella Azure AD DS-nätverket till den plats där andra DNS-namnområden finns.
    * Den här anslutningen kan tillhandahållas med en [Azure-ExpressRoute][expressroute] eller [Azure VPN gateway][vpn-gateway] -anslutning.
* En virtuell Windows Server Management-dator som är ansluten till den hanterade domänen.
    * Om det behövs, slutför du själv studie kursen för att [skapa en virtuell Windows Server-dator och koppla den till en hanterad domän][create-join-windows-vm].
* Ett användar konto som är medlem i *Administratörs gruppen för Azure AD DC* i din Azure AD-klient.

## <a name="install-dns-server-tools"></a>Installera verktyg för DNS-Server

Om du vill skapa och ändra DNS-poster i en hanterad domän måste du installera verktyg för DNS-server. Dessa verktyg kan installeras som en funktion i Windows Server. Mer information om hur du installerar administrations verktyg på en Windows-klient finns i installera [verktyg för fjärrserveradministration (RSAT)][install-rsat].

1. Logga in på den virtuella hanterings datorn. Anvisningar om hur du ansluter med hjälp av Azure Portal finns i [ansluta till en virtuell Windows Server-dator][connect-windows-server-vm].
1. Om **Serverhanteraren** inte öppnas som standard när du loggar in på den virtuella datorn väljer du **Start** -menyn och väljer **Serverhanteraren**.
1. I fönstret *instrument panel* i fönstret **Serverhanteraren** väljer du **Lägg till roller och funktioner**.
1. På sidan **innan du börjar** i *guiden Lägg till roller och funktioner* väljer du **Nästa**.
1. För *installations typen* låter du alternativet för **rollbaserad eller funktions baserad installation** vara markerat och väljer **Nästa**.
1. På sidan **Server val** väljer du den aktuella virtuella datorn från serverpoolen, till exempel *myvm.aaddscontoso.com*, och väljer sedan **Nästa**.
1. På sidan **Server roller** klickar du på **Nästa**.
1. På sidan **funktioner** expanderar du noden **verktyg för fjärrserveradministration** och expandera sedan noden **roll administrations verktyg** . Välj funktionen **verktyg för DNS-Server** från listan över roll administrations verktyg.

    ![Välj att installera verktyg för DNS-server från listan över tillgängliga roll administrations verktyg](./media/manage-dns/install-dns-tools.png)

1. På sidan **bekräftelse** väljer du **Installera**. Det kan ta en minut eller två att installera verktyg för DNS-server.
1. När funktions installationen är klar väljer du **Stäng** för att avsluta guiden **Lägg till roller och funktioner** .

## <a name="open-the-dns-management-console-to-administer-dns"></a>Öppna DNS-hanteringskonsolen för att administrera DNS

Med verktyg för DNS-server installerat kan du administrera DNS-poster på den hanterade domänen.

> [!NOTE]
> Om du vill administrera DNS i en hanterad domän måste du vara inloggad på ett användar konto som är medlem i *Administratörs gruppen för AAD-domänkontrollanten* .

1. Välj **administrations verktyg** på Start skärmen. En lista över tillgängliga hanterings verktyg visas, inklusive **DNS** installerat i föregående avsnitt. Välj **DNS** för att starta konsolen DNS-hantering.
1. I dialog rutan **Anslut till DNS-Server** väljer **du följande dator** och anger sedan DNS-domännamnet för den hanterade domänen, till exempel *aaddscontoso.com*:

    ![Ansluta till den hanterade domänen i DNS-konsolen](./media/manage-dns/connect-dns-server.png)

1. DNS-konsolen ansluter till den angivna hanterade domänen. Expandera zoner för **vanlig sökning** eller **zoner för omvänd sökning** för att skapa DNS-poster som krävs eller redigera befintliga poster efter behov.

    ![DNS-konsol – administrera domän](./media/manage-dns/dns-manager.png)

> [!WARNING]
> När du hanterar poster med hjälp av verktyg för DNS-server, se till att du inte tar bort eller ändrar de inbyggda DNS-posterna som används av Azure AD DS. Inbyggda DNS-poster inkluderar domän-DNS-poster, namn server poster och andra poster som används för lokalisering av domänkontrollant. Om du ändrar dessa poster avbryts domän tjänster på det virtuella nätverket.

## <a name="create-conditional-forwarders"></a>Skapa villkorliga vidarebefordrare

En DNS-zon i Azure AD DS bör bara innehålla zonen och posterna för den hanterade domänen. Skapa inte fler zoner i den hanterade domänen för att lösa namngivna resurser i andra DNS-namnområden. Använd i stället villkorliga vidarebefordrare i den hanterade domänen för att ange den DNS-server som ska användas för att matcha adresser för dessa resurser.

En villkorlig vidarebefordrare är ett konfigurations alternativ i en DNS-server som gör att du kan definiera en DNS-domän, till exempel *contoso.com*, för att vidarebefordra frågor till. I stället för den lokala DNS-servern som försöker matcha frågor för poster i domänen vidarebefordras DNS-frågor till den domän som har kon figurer ATS för DNS. Den här konfigurationen säkerställer att rätt DNS-poster returneras, eftersom du inte skapar en lokal DNS-zon med dubbla poster i den hanterade domänen för att avspegla dessa resurser.

Utför följande steg för att skapa en villkorlig vidarebefordrare i den hanterade domänen:

1. Välj din DNS-zon, till exempel *aaddscontoso.com*.
1. Välj **villkorliga vidarebefordrare**, högerklicka och välj **ny villkorlig vidarebefordrare...**
1. Ange din andra **DNS-domän**, t. ex. *contoso.com*, och ange IP-adresserna för DNS-servrarna för det namn området, som visas i följande exempel:

    ![Lägga till och konfigurera en villkorlig vidarebefordrare för DNS-servern](./media/manage-dns/create-conditional-forwarder.png)

1. Markera kryss rutan för att **lagra den här villkorliga vidarebefordraren i Active Directory och replikera den enligt** följande. Välj sedan alternativet för *alla DNS-servrar i den här domänen*, som visas i följande exempel:

    ![DNS-konsol – Välj alla DNS-servrar i den här domänen](./media/manage-dns/store-in-domain.png)

    > [!IMPORTANT]
    > Om den villkorliga vidarebefordraren lagras i *skogen* i stället för *domänen*, Miss lyckas den villkorliga vidarebefordraren.

1. Välj **OK** för att skapa den villkorliga vidarebefordraren.

Namn matchningen för resurserna i andra namn områden från de virtuella datorer som är anslutna till den hanterade domänen bör nu lösas korrekt. Frågor för den DNS-domän som kon figurer ATS i den villkorliga vidarebefordraren skickas till relevanta DNS-servrar.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du hanterar DNS finns i [artikeln DNS-verktyg på TechNet](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753579(v=ws.11)).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[expressroute]: ../expressroute/expressroute-introduction.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh