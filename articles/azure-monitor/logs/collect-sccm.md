---
title: Ansluta Configuration Manager till Azure Monitor | Microsoft Docs
description: Den här artikeln visar stegen för att ansluta Configuration Manager till arbets ytan i Azure Monitor och börja analysera data.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/30/2020
ms.openlocfilehash: 540ca293afdd7514793b8eec390b4751885859df
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100624329"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>Anslut Configuration Manager till Azure Monitor
Du kan ansluta din Microsoft Endpoint Configuration Manager-miljö till Azure Monitor för att synkronisera enhets samlings data och referera till dessa samlingar i Azure Monitor och Azure Automation.  

> [!IMPORTANT]
> Den här funktionen är föråldrad från och med Configuration Manager version 2010.<!-- 8269855 --> Mer information finns i [borttagna och föråldrade funktioner för Configuration Manager](/mem/configmgr/core/plan-design/changes/deprecated/removed-and-deprecated-cmfeatures).

## <a name="prerequisites"></a>Förutsättningar

Azure Monitor stöder Configuration Manager aktuella grenen, version 1606 och senare.

>[!NOTE]
>Funktionen för att ansluta Configuration Manager med en Log Analytics arbets yta är valfri och är inte aktive rad som standard. Du måste aktivera den här funktionen innan du använder den. Mer information finns i avsnittet [Enable optional features from updates](/configmgr/core/servers/manage/install-in-console-updates#bkmk_options).

## <a name="configuration-overview"></a>Översikt över konfiguration

Följande steg sammanfattar stegen för att konfigurera Configuration Manager-integrering med Azure Monitor.  

1. I Azure Active Directory registrerar du Configuration Manager som en webbapp och/eller webb-API-app och kontrollerar att du har klient-ID och klientens hemliga nyckel från registreringen från Azure Active Directory. Se [använda Portal för att skapa Active Directory program och tjänstens huvud namn som har åtkomst till resurser](../../active-directory/develop/howto-create-service-principal-portal.md) för detaljerad information om hur du utför det här steget.

2. I Azure Active Directory [beviljar du Configuration Manager (den registrerade webbappen) med behörighet att komma åt Azure Monitor](#grant-configuration-manager-with-permissions-to-log-analytics).

3. I Configuration Manager lägger du till en anslutning med hjälp av guiden **Azure-tjänster** .

4. [Ladda ned och installera Log Analytics agent för Windows](#download-and-install-the-agent) på den dator som kör tjänst anslutnings punktens plats system roll för Configuration Manager. Agenten skickar Configuration Manager data till arbets ytan Log Analytics i Azure Monitor.

5. I Azure Monitor [importerar du samlingar från Configuration Manager](#import-collections) som dator grupper.

6. I Azure Monitor kan du visa data från Configuration Manager som [dator grupper](computer-groups.md).

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Bevilja Configuration Manager behörighet att Log Analytics

I följande procedur beviljar du rollen *deltagare* i din Log Analytics-arbetsyta till AD-programmet och tjänstens huvud namn som du skapade tidigare för Configuration Manager. Om du inte redan har en arbets yta, se [skapa en arbets yta i Azure Monitor](../learn/quick-create-workspace.md) innan du fortsätter. Detta gör att Configuration Manager kan autentisera och ansluta till din Log Analytics-arbetsyta.  

> [!NOTE]
> Du måste ange behörigheter i Log Analytics arbets ytan för Configuration Manager. Annars visas ett fel meddelande när du använder konfigurations guiden i Configuration Manager.
>

1. I Azure Portal klickar du på **Alla tjänster** längst upp till vänster. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics**.

2. I listan med Log Analytics arbets ytor väljer du den arbets yta som du vill ändra.

3. Välj **åtkomst kontroll (IAM)** i den vänstra rutan.

4. På sidan åtkomst kontroll (IAM) klickar du på **Lägg till roll tilldelning** och fönstret **Lägg till roll tilldelning** visas.

5. I list rutan **roll** i rutan **Lägg till roll tilldelning** väljer du rollen **deltagare** .  

6. Under List rutan **tilldela åtkomst till** väljer du det Configuration Manager-program som SKAPADES i AD tidigare och klickar sedan på **OK**.  

## <a name="download-and-install-the-agent"></a>Hämta och installera agenten

Läs artikeln [Anslut Windows-datorer till Azure Monitor i Azure](../agents/agent-windows.md) för att förstå de metoder som är tillgängliga för att installera Log Analytics-agenten för Windows på den dator som är värd för plats system rollen Configuration Manager tjänst anslutnings punkt.  

## <a name="connect-configuration-manager-to-log-analytics-workspace"></a>Anslut Configuration Manager till Log Analytics arbets yta

>[!NOTE]
> För att du ska kunna lägga till en Log Analytics anslutning måste Configuration Managers miljön ha en [tjänst anslutnings punkt](/configmgr/core/servers/deploy/configure/about-the-service-connection-point) konfigurerad för online-läge.

> [!NOTE]
> Du måste ansluta platsen på den översta nivån i hierarkin för att Azure Monitor. Om du ansluter en fristående primär plats till Azure Monitor och sedan lägger till en central administrations plats i din miljö måste du ta bort och återskapa anslutningen i den nya hierarkin.

1. I arbets ytan **Administration** i Configuration Manager väljer du **moln tjänster** och väljer sedan **Azure-tjänster**. 

2. Högerklicka på **Azure-tjänster** och välj sedan **Konfigurera Azure-tjänster**. Sidan **Konfigurera Azure-tjänster** visas. 
   
3. På skärmen **Allmänt** bekräftar du att du har utfört följande åtgärder och att du har information om varje objekt och väljer sedan **Nästa**.

4. På sidan Azure-tjänster i guiden Azure-tjänster:

    1. Ange ett **namn** för objektet i Configuration Manager.
    2. Ange en valfri **Beskrivning** som hjälper dig att identifiera tjänsten.
    3. Välj Azure Service **OMS-anslutaren**.

    >[!NOTE]
    >OMS kallas nu Log Analytics som är en funktion i Azure Monitor.

5. Välj **Nästa** för att fortsätta till sidan Egenskaper för Azure-appen i guiden Azure-tjänster.

6. På sidan **app** i guiden Azure-tjänster väljer du först Azure-miljön i listan och klickar sedan på **Importera**.

7. På sidan **Importera appar** anger du följande information:

    1. Ange **Azure AD-klientens namn** för appen.

    2. Ange för **Azure AD-klient-ID** för Azure AD-klienten. Du hittar den här informationen på sidan Azure Active Directory **Egenskaper** . 

    3. Ange för **program namnet** på program namnet.

    4. Ange för **klient-ID**, program-ID: t för den skapade Azure AD-appen som skapades tidigare.

    5. Ange för **hemlig nyckel**, klientens hemliga nyckel för den skapade Azure AD-appen.

    6. Ange för den **hemliga nyckelns** förfallo datum, förfallo datum för nyckeln.

    7. Ange för **app-ID-URI**: n för app-ID för den skapade Azure AD-appen som skapades tidigare.

    8. Välj **Verifiera** och till höger bör resultaten visas som **verifierade!**.

8. På sidan **konfiguration** granskar du informationen för att kontrol lera fälten **Azure-prenumeration**, **Azure-resurs grupp** och **Operations Management Suite-arbetsytan** i förväg, vilket innebär att Azure AD-programmet har tillräcklig behörighet i resurs gruppen. Om fälten är tomma anger det att programmet inte har de rättigheter som krävs. Välj de enhets samlingar som ska samlas in och vidarebefordras till arbets ytan och välj sedan **Lägg till**.

9. Granska alternativen på sidan **Bekräfta inställningar** och välj **Nästa** för att börja skapa och konfigurera anslutningen.

10. När konfigurationen är klar visas sidan **slut för ande** . Välj **Stäng**. 

När du har länkat Configuration Manager till Azure Monitor kan du lägga till eller ta bort samlingar och visa egenskaperna för anslutningen.

## <a name="update-log-analytics-workspace-connection-properties"></a>Uppdatera Log Analytics arbets ytans anslutnings egenskaper

Om ett lösen ord eller en klient hemlig nyckel går ut eller förloras måste du uppdatera Log Analytics anslutnings egenskaperna manuellt.

1. I arbets ytan **Administration** i Configuration Manager väljer du **Cloud Services** och väljer sedan **OMS Connector** för att öppna sidan **Egenskaper för OMS-anslutning** .
2. På den här sidan klickar du på fliken **Azure Active Directory** för att **Visa din klient**, **klient-ID**, **förfallo datum för klientens hemliga nyckel**. **Verifiera** din **klient hemliga nyckel** om den har upphört att gälla.

## <a name="import-collections"></a>Importera samlingar

När du har lagt till en Log Analytics-anslutning till Configuration Manager och installerat agenten på den dator som kör tjänst anslutnings punktens plats system roll i Configuration Manager, är nästa steg att importera samlingar från Configuration Manager i Azure Monitor som dator grupper.

När du har slutfört den inledande konfigurationen för att importera enhets samlingar från din-hierarki hämtas samlings informationen var 3: e timme så att medlemskapet hålls aktuellt. Du kan välja att inaktivera det när som helst.

1. I Azure Portal klickar du på **Alla tjänster** längst upp till vänster. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics arbets ytor**.
2. I listan med Log Analytics arbets ytor väljer du den arbets yta Configuration Manager har registrerats med.  
3. Välj **Avancerade inställningar**.
4. Välj **dator grupper** och välj sedan **SCCM**.  
5. Välj **importera Configuration Manager samlings medlemskap** och klicka sedan på **Spara**.  
   
    ![Skärm bild av datorn grupperar avancerade inställningar för S C C M som innehåller alternativet för att importera Configuration Manager samlings medlemskap.](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Visa data från Configuration Manager

När du har lagt till en Log Analytics-anslutning till Configuration Manager och installerat agenten på den dator som kör plats system rollen för tjänst anslutnings punkten i Configuration Manager, skickas data från agenten till Log Analytics arbets ytan i Azure Monitor. I Azure Monitor visas Configuration Manager samlingarna som [dator grupper](./computer-groups.md). Du kan visa grupperna från sidan **Configuration Manager** under **Settings\Computer grupper**.

När samlingarna har importer ATS kan du se hur många datorer med samlings medlemskap som har identifierats. Du kan också se hur många samlingar som har importer ATS.

![Skärm bild av datorn grupperar avancerade inställningar för S C C M visar alternativet för att importera Configuration Manager samlings medlemskap har valts.](./media/collect-sccm/sccm-computer-groups02.png)

När du klickar på någon av dessa öppnas logg Frågeredigeraren som visar antingen alla importerade grupper eller alla datorer som tillhör varje grupp. Med [loggs ökning](../log-query/log-query-overview.md)kan du utföra ytterligare djupgående analys av medlemskaps data för samlingen.

## <a name="next-steps"></a>Nästa steg

Använd [loggs ökningen](../log-query/log-query-overview.md) för att visa detaljerad information om dina Configuration Manager data.

