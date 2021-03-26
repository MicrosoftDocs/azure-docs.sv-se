---
title: Installera Micro Focus Enterprise Server 5,0 och Enterprise Developer 5,0 på Azure | Microsoft Docs
description: I den här artikeln får du lära dig hur du installerar Micro Focus Enterprise Server 5,0 och Enterprise Developer 5,0 på Microsoft Azure.
services: virtual-machines
documentationcenter: ''
author: maggsl
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 06/29/2020
tags: ''
keywords: ''
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 5001f5df8ebab3e8366a2b31b1c5eb8ccc69c985
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "104951359"
---
# <a name="install-micro-focus-enterprise-server-50-and-enterprise-developer-50-on-azure"></a>Installera Micro Focus Enterprise Server 5,0 och Enterprise Developer 5,0 på Azure

Den här artikeln visar hur du konfigurerar [Micro Focus Enterprise Server 5,0](https://www.microfocus.com/documentation/enterprise-developer/ed50pu5/ES-WIN/GUID-F7D8FD6E-BDE0-4169-8D8C-96DDFFF6B495.html) och [Micro Focus enterprise Developer 5,0](https://www.microfocus.com/documentation/enterprise-developer/ed50/) på Microsoft Azure.

En gemensam arbets belastning i Azure är en utvecklings-och test miljö. Det här scenariot är vanligt eftersom det är så kostnads effektivt och enkelt att distribuera och avbryta. Med Enterprise Server har Micro Focus skapat en av de största tillgängliga plattformarna för stordatorer som är tillgängliga. Du kan köra z/OS-arbetsbelastningar på en billigare x86-plattform på Azure med antingen virtuella Windows-eller Linux-datorer (VM).

Den här installationen använder virtuella Azure-datorer som kör Windows Server 2016-avbildningen från Azure Marketplace med Microsoft SQL Server 2017 redan har installerats. Den här inställningen gäller även för Azure Stack.

Motsvarande utvecklings miljö för Enterprise Server är företags utvecklare, som körs på antingen Microsoft Visual Studio 2017 eller senare, Visual Studio Community (kostnads fri att ladda ned) eller Sol förmörkelse. Den här artikeln visar hur du distribuerar den med en virtuell Windows Server 2016-dator som medföljer Visual Studio 2017 eller senare installerat.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar bör du gå igenom följande krav:

-   En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

-   Micro Focus-programvaran och en giltig licens (eller prov licens). Om du är en befintlig Micro Focus-kund kontaktar du din Micro Focus-representant. Annars kan du [begära en utvärderings version](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

-   Hämta dokumentationen för [Enterprise Server och Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/ed50/).

    > [!Note]
    > Det finns några alternativ för att kontrol lera åtkomsten till dina virtuella datorer:
    > -   Vi rekommenderar att du konfigurerar Azure- [skydds](https://azure.microsoft.com/services/azure-bastion/).
    > -   En [plats-till-plats VPN-tunnel (virtuellt privat nätverk)](../../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md) .
    > -   En virtuell hoppsida-dator.

## <a name="install-enterprise-server"></a>Installera Enterprise Server

1.  Överväg att skapa en ny resurs grupp för det här projektet, till exempel **RGMicroFocusEntServer**, för bättre säkerhet och hanterbarhet. Använd den första delen av namnet i Azure om du vill välja typ av resurs för att göra det lättare att hitta dem i en lista.

2.  Skapa en virtuell dator. Från Azure Marketplace väljer du den virtuella dator och det operativ system som du vill använda. Här är en rekommenderad installation:

    -   **Enterprise Server:** Välj **ES2 v3 VM** (med 2 virtuella processorer och 16 GB minne) med Windows Server 2016 och SQL Server 2017 installerat. Den här avbildningen är tillgänglig från Azure Marketplace. Företags servern kan även använda Azure SQL Database.

    -   **Enterprise-utvecklare:** Välj **B2MS VM** (med 2 virtuella processorer och 8 GB minne) med Windows 10 och Visual Studio installerat. Den här avbildningen är tillgänglig från Azure Marketplace.

3.  I avsnittet **grundläggande** anger du ditt användar namn och lösen ord. Välj den **prenumeration** och **plats/region** som du vill använda för de virtuella datorerna. Välj **RGMicroFocusEntServer** för resurs gruppen.

4.  Sätt båda virtuella datorerna i samma virtuella nätverk så att de kan kommunicera med varandra.

5.  Acceptera standardinställningarna för resten av inställningarna. Kom ihåg det användar namn och lösen ord som du skapar för administratören för dessa virtuella datorer.

6.  När de virtuella datorerna har skapats öppnar du de inkommande portarna **9003, 86** och **80** för HTTP och **3389** för Remote Desktop Protocol (RDP) på Enterprise Server-datorn och **3389** på Developer-datorn.

7.  Om du vill logga in på den virtuella datorn för Enterprise Server väljer du den virtuella ES2 v3-datorn i Azure Portal. Gå till avsnittet **Översikt** och välj **Anslut** för att starta en RDP-session. Logga in med de autentiseringsuppgifter som du skapade för den virtuella datorn.

8.  Läs in följande två filer från RDP-sessionen. Eftersom du använder Windows kan du dra och släppa filerna i RDP-sessionen:

    -   `es\_50.exe`, installations filen för företags servern.

    -   `mflic`, motsvarande licens fil – Enterprise Server läser inte in utan den.

9.  Starta installationen genom att dubbelklicka på filen. I det första fönstret väljer du installations platsen och accepterar slut användar avtalet.

    ![Skärm bild som visar dialog rutan Micro Focus Enterprise Server där du kan starta installationen.](media/install-image-1.png)

    När installationen är klar visas följande meddelande:

    ![Skärm bild som visar ett meddelande om att ett meddelande visas i dialog rutan för Micro Focus Enterprise Server.](media/install-image-2.png)

 ### <a name="check-for-updates"></a>Sök efter uppdateringar

Efter installationen måste du kontrol lera om det finns fler uppdateringar som ett antal förutsättningar, till exempel Microsoft C++ Redistributable och .NET Framework, installeras tillsammans med Enterprise Server.

### <a name="upload-the-license"></a>Ladda upp licensen

1.  Starta licens administration för Micro Focus.

2.  Välj **Starta** \> **Micro Focus License Manager** \> **License administration** och klicka sedan på fliken **Installera** . Välj vilken typ av licens format som ska överföras: en licens fil eller en licens kod på 16 tecken. Till exempel, i **licens fil**, bläddrar du till * `mflic` filen överfördes tidigare till den virtuella datorn och väljer **Installera licenser**.

    ![Skärm bild som visar dialog rutan licens administration för Micro Focus där du kan välja Installera licenser.](media/install-image-3.png)

3.  Kontrol lera att Enterprise Server läser in. Försök att starta administrations webbplatsen för företags servern från en webbläsare med följande URL: `http://localhost:86/` . Sidan administration av företags server visas som visas.

    ![Sidan administration av företags server](media/install-image-4.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>Installera Enterprise Developer på Developer Machine

1.  Välj den resurs grupp som skapades tidigare (till exempel **RGMicroFocusEntServer**) och välj sedan Developer-avbildningen.

2.  Logga in på den virtuella datorn genom att gå till avsnittet **Översikt** och välj **Anslut**. Den här inloggningen startar en RDP-session. Logga in med de autentiseringsuppgifter som du skapade för den virtuella datorn.

3.  Läs in följande två filer från RDP-sessionen (dra och släpp om du vill):

    -   `edvs2017.exe`, installations filen för företags servern.

    -   `mflic`kommer motsvarande licens fil (Enterprise Developer inte att läsas in utan den).

4.  Starta installationen genom att dubbelklicka på **edvs2017.exes** filen. I det första fönstret väljer du installations platsen och accepterar slut användar avtalet. Om du vill kan du välja **Installera Rumba 9,5** för att installera denna termin Ale mula Tor som du förmodligen behöver.

    ![Konfigurations dialog rutan för Micro Focus Enterprise Developer för Visual Studio 2017](media/install-image-5.png)

5.  När installationen är klar visas följande meddelande:

    ![Installations meddelandet har slutförts](media/install-image-6.png)

6.  Starta Micro Focus License Manager precis som du gjorde för företags servern. Välj **Starta** \> **Micro Focus License Manager** \> **License administration** och klicka på fliken **Installera** .

7.  Välj typ av licens format som ska överföras: en licens fil eller en licens kod på 16 tecken. Till exempel, i **licens fil**, bläddrar du till den fil som `mflic` laddats upp tidigare till den virtuella datorn och väljer  **Installera licenser**.

    ![Dialog rutan licens administration för Micro Focus](media/install-image-7.png)

När Enterprise Developer laddas, är din distribution av en Micro Focus-utveckling och test miljö på Azure slutförd!

**Nästa steg**

-   [Konfigurera BankDemo-programmet](./demo.md)

-   [Kör Enterprise Server i Docker-behållare](./run-enterprise-server-container.md)

-   [Migrering av program från stordatorer](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)