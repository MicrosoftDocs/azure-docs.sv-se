---
title: Integrera Azure Monitor for VMs karta med Operations Manager | Microsoft Docs
description: Azure Monitor for VMs identifierar automatiskt program komponenter i Windows-och Linux-system och mappar kommunikationen mellan tjänsterna. Den här artikeln beskriver hur du använder Map-funktionen för att automatiskt skapa distribuerade program diagram i Operations Manager.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/12/2019
ms.openlocfilehash: 0722a1806cc94102f92045c78850d96ed9890d02
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100621251"
---
# <a name="integrate-system-center-operations-manager-with-azure-monitor-for-vms-map-feature"></a>Integrera System Center Operations Manager med Azure Monitor for VMs kart funktion

I Azure Monitor for VMs kan du Visa identifierade program komponenter på virtuella Windows-och Linux-datorer (VM: ar) som körs i Azure eller i din miljö. Med den här integreringen mellan kart funktionen och System Center Operations Manager kan du automatiskt skapa distribuerade program diagram i Operations Manager som baseras på de dynamiska beroende Maps i Azure Monitor for VMs. I den här artikeln beskrivs hur du konfigurerar din System Center Operations Manager hanterings grupp så att den stöder den här funktionen.

>[!NOTE]
>Om du redan har distribuerat Tjänstkarta kan du Visa dina kartor i Azure Monitor for VMs, som innehåller ytterligare funktioner för att övervaka hälso tillstånd och prestanda för virtuella datorer. Kart funktionen i Azure Monitor for VMs är avsedd att ersätta den fristående Tjänstkarta-lösningen. Läs mer i [Azure Monitor for VMS översikt](../vm/vminsights-overview.md).

## <a name="prerequisites"></a>Förutsättningar

* En System Center Operations Manager hanterings grupp (2012 R2 eller senare).
* En Log Analytics arbets yta som har kon figurer ATS för att stödja Azure Monitor for VMs.
* En eller flera virtuella Windows-och Linux-datorer eller fysiska datorer som övervakas av Operations Manager och skickar data till Log Analytics-arbetsytan. Linux-servrar som rapporterar till en Operations Manager hanterings grupp måste konfigureras för att ansluta direkt till Azure Monitor. Mer information finns i översikten samla in [logg data med Log Analytics agenten](../agents/log-analytics-agent.md).
* Ett huvud namn för tjänsten med åtkomst till den Azure-prenumeration som är associerad med Log Analytics-arbetsytan. Mer information finns i [skapa ett huvud namn för tjänsten](#create-a-service-principal).

## <a name="install-the-service-map-management-pack"></a>Installera hanterings paketet för Tjänstkarta

Du aktiverar integrationen mellan Operations Manager och Map-funktionen genom att importera Microsoft.SystemCenter. ServiceMap Management Pack (Microsoft.SystemCenter. ServiceMap. mpb). Du kan ladda ned hanterings paket paketet från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=55763). Paketet innehåller följande hanterings paket:

* Vyer för Microsoft Tjänstkarta-program
* Microsoft System Center Tjänstkarta internt
* Åsidosättningar för Microsoft System Center Tjänstkarta
* Microsoft System Center-Tjänstkarta

## <a name="configure-integration"></a>Konfigurera integrering

När du har installerat Tjänstkarta hanterings paketet visas en ny nod **tjänstkarta** under **Operations Management Suite** i rutan **Administration** i Operations Manager drift konsolen.

>[!NOTE]
>[Operations Management Suite var en samling tjänster](../terminology.md#april-2018---retirement-of-operations-management-suite-brand) som innehöll Log Analytics, är nu en del av [Azure Monitor](../overview.md).

Gör så här för att konfigurera Azure Monitor for VMs Map-integrering:

1. Öppna konfigurations guiden i fönstret **tjänstkarta översikt** genom att klicka på **Lägg till arbets yta**.  

    ![Tjänstkarta översikts fönster](media/service-map-scom/scom-configuration.png)

2. I fönstret **anslutnings konfiguration** anger du klient namn eller ID, program-ID (även kallat användar namn eller clientID) och lösen ord för tjänstens huvud namn och klickar sedan på **Nästa**. Mer information finns i skapa ett huvud namn för tjänsten.

    ![Fönstret anslutnings konfiguration](media/service-map-scom/scom-config-spn.png)

3. I fönstret **Val av prenumeration** väljer du den Azure-prenumeration, Azure-resurs grupp (den som innehåller Log Analytics-arbetsytan) och Log Analytics arbets yta och klickar sedan på **Nästa**.

    ![Arbets ytan Operations Manager konfiguration](media/service-map-scom/scom-config-workspace.png)

4. I fönstret **Val av dator grupp** väljer du vilka tjänstkarta dator grupper som du vill synkronisera till Operations Manager. Klicka på **Lägg till/ta bort dator grupper**, Välj grupper i listan över **tillgängliga dator grupper** och klicka på **Lägg till**.  När du är klar med att välja grupper klickar du på **OK** för att avsluta.

    ![Dator grupper för Operations Manager konfiguration](media/service-map-scom/scom-config-machine-groups.png)

5. I fönstret **Server val** konfigurerar du gruppen tjänstkarta servrar med de servrar som du vill synkronisera mellan Operations Manager och kart funktionen. Klicka på **Lägg till/ta bort servrar**.

    För att integreringen ska kunna bygga ett distribuerat program diagram för en server måste servern vara:

   * Övervakad av Operations Manager
   * Konfigurerad att rapportera till den Log Analytics arbets ytan som kon figurer ATS med Azure Monitor for VMs
   * Visas i gruppen Tjänstkarta servrar

     ![Konfigurations gruppen Operations Manager](media/service-map-scom/scom-config-group.png)

6. Valfritt: Välj resurspoolen för alla hanterings servrar för att kommunicera med Log Analytics och klicka sedan på **Lägg till arbets yta**.

    ![Skärm bild av skärmen Server pool i Lägg till Microsoft Operations Management Suite arbets yta där resurspoolen alla hanterings servrar har valts.](media/service-map-scom/scom-config-pool.png)

    Det kan ta en minut att konfigurera och registrera Log Analytics-arbetsytan. När den har kon figurer ATS initierar Operations Manager den första kart synkroniseringen.

    ![Skärm bild av slut för ande skärmen i Lägg till Microsoft Operations Management Suite arbets yta som bekräftar att arbets ytan har lagts till.](media/service-map-scom/scom-config-success.png)

## <a name="monitor-integration"></a>Övervaka integrering

När Log Analytics arbets ytan är ansluten visas en ny mapp Tjänstkarta i **övervaknings** fönstret i Operations Manager drift konsolen.

![Fönstret Operations Manager övervakning](media/service-map-scom/scom-monitoring.png)

Mappen Tjänstkarta innehåller fyra noder:

* **Aktiva aviseringar**: visar alla aktiva aviseringar om kommunikationen mellan Operations Manager och Azure Monitor.  

  >[!NOTE]
  >De här aviseringarna är inte Log Analytics-aviseringar som synkroniseras med Operations Manager, de genereras i hanterings gruppen baserat på arbets flöden som definierats i Tjänstkarta hanterings paketet.

* **Servrar**: visar de övervakade servrar som är konfigurerade för synkronisering från Azure Monitor for VMS Map-funktion.

    ![Fönstret Operations Manager övervaknings servrar](media/service-map-scom/scom-monitoring-servers.png)

* **Dator grupp beroende vyer**: visar alla dator grupper som synkroniseras från kart funktionen. Du kan klicka på valfri grupp om du vill visa dess diagram över distribuerade program.

    ![Skärm bild från Tjänstkarta visar ett diagram med bilder för varje dator grupp och linjer som anger beroenden mellan dem.](media/service-map-scom/scom-group-dad.png)

* **Vyer för Server beroende**: visar en lista över alla servrar som har synkroniserats från kart funktionen. Du kan klicka på valfri server om du vill visa det distribuerade program diagrammet.

    ![Skärm bild från Tjänstkarta visar ett diagram med bilder för varje server och linje som visar beroenden mellan dem.](media/service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Redigera eller ta bort arbets ytan

Du kan redigera eller ta bort den konfigurerade arbets ytan via **tjänstkarta översikts** fönstret (**administrations** fönstret > **Operations Management Suite**  >  **tjänstkarta**).

> [!NOTE]
> [Operations Management Suite var en samling tjänster](../terminology.md#april-2018---retirement-of-operations-management-suite-brand) som innehöll Log Analytics, som nu ingår i [Azure Monitor](../overview.md).

Du kan bara konfigurera en Log Analytics arbets yta i den här versionen.

![Fönstret Operations Manager redigera arbets yta](media/service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Konfigurera regler och åsidosättningar

En regel, *Microsoft.SystemCenter. ServiceMapImport. Rule*, hämtar regelbundet information från Azure Monitor for VMS kart funktion. Om du vill ändra synkroniseringsfrekvensen kan du åsidosätta regeln och ändra värdet för parametern **IntervalMinutes**.

![Fönstret Operations Manager åsidosätter egenskaper](media/service-map-scom/scom-overrides.png)

* **Aktive rad**: Aktivera eller inaktivera automatiska uppdateringar.
* **IntervalMinutes**: anger tiden mellan uppdateringar. Standard intervallet är en timme. Om du vill synkronisera Maps oftare kan du ändra värdet.
* **TimeoutSeconds**: anger efter hur lång tid tids gränsen för begäran ska utföras.
* **TimeWindowMinutes**: anger tidsintervallet för att fråga efter data. Standardvärdet är 60 minuter, vilket är det högsta tillåtna intervallet.

## <a name="known-issues-and-limitations"></a>Kända problem och begränsningar

I den aktuella designen presenteras följande problem och begränsningar:

* Du kan bara ansluta till en enda Log Analytics-arbetsyta.
* Även om du kan lägga till servrar i gruppen Tjänstkarta servrar manuellt via fönstret **redigering** , synkroniseras inte kartorna för dessa servrar direkt. De kommer att synkroniseras från Azure Monitor for VMs Map-funktionen under nästa synkronisering.
* Om du gör ändringar i de distribuerade program diagrammen som skapats av hanterings paketet kommer ändringarna förmodligen att skrivas över vid nästa synkronisering med Azure Monitor for VMs.

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Officiella Azure-dokumentation om hur du skapar ett huvud namn för tjänsten finns i:

* [Skapa ett huvud namn för tjänsten med hjälp av PowerShell](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Skapa ett huvud namn för tjänsten med hjälp av Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli)
* [Skapa ett huvud namn för tjänsten med hjälp av Azure Portal](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="suggestions"></a>Förslag

Har du några kommentarer till oss om integrering med Azure Monitor for VMs kart funktion eller den här dokumentationen? Besök vår [användares röst sida](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map)där du kan föreslå funktioner eller rösta på befintliga förslag.

