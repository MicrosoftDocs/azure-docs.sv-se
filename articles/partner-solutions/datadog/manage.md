---
title: Hantera en Datadog-resurs – Azure-partner lösningar
description: I den här artikeln beskrivs hantering av en Datadog-resurs i Azure Portal. Så här konfigurerar du enkel inloggning, tar bort en gemensam organisation och får support.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/19/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 04aef540bc134e5ec307be6a232ce47f0923e528
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105046359"
---
# <a name="manage-the-datadog-resource"></a>Hantera Datadog-resursen

Den här artikeln visar hur du hanterar inställningarna för din Azure-integrering med Datadog.

## <a name="resource-overview"></a>Resurs översikt

Om du vill se information om din Datadog-resurs väljer du **Översikt** i det vänstra fönstret.

:::image type="content" source="media/manage/resource-overview.png" alt-text="Översikt över Datadog-resurs" border="true" lightbox="media/manage/resource-overview.png":::

Informationen är:

- Namn på resursgrupp
- Plats/region
- Prenumeration
- Taggar
- Länk till enkel inloggning till Datadog-organisation
- Datadog-erbjudande/-plan
- Fakturerings period

Den innehåller också länkar till Datadog-instrumentpaneler, loggar och värd kartor.

Översikts skärmen innehåller en sammanfattning av resurserna som skickar loggar och mått till Datadog.

- Resurs typ – resurs typ för Azure.
- Totalt antal resurser – antalet resurser för resurs typen.
- Resurser som skickar loggar – antal resurser som skickar loggar till Datadog via integreringen.
- Resurser som skickar mått – antal resurser som skickar mått till Datadog via integreringen.

## <a name="reconfigure-rules-for-metrics-and-logs"></a>Konfigurera om regler för mått och loggar

Om du vill ändra konfigurations reglerna för mått och loggar väljer du **mått och loggar** i det vänstra fönstret.

:::image type="content" source="media/manage/reconfigure-metrics-and-logs.png" alt-text="Ändra konfigurationen för loggar och mått för Datadog-resursen." border="true":::

Mer information finns i [Konfigurera mått och loggar](create.md#configure-metrics-and-logs).

## <a name="view-monitored-resources"></a>Visa övervakade resurser

Om du vill se en lista över resurser som avger loggar till Datadog väljer du **övervakade resurser** i den vänstra rutan.

:::image type="content" source="media/manage/view-monitored-resources.png" alt-text="Visa resurser som övervakas av Datadog" border="true":::

Du kan filtrera listan över resurser efter resurs typ, resurs grupp namn, plats och om resursen skickar loggar och mått.

Kolumn **loggarna till Datadog** anger om resursen skickar loggar till Datadog. Om resursen inte skickar loggar anger det här fältet Varför loggar inte skickas till Datadog. Orsaken kan vara:

- Resursen stöder inte sändning av loggar. Endast resurs typer med övervaknings logg kategorier kan konfigureras att skicka loggar till Datadog.
- Gränsen för fem diagnostiska inställningar har uppnåtts. Varje Azure-resurs kan ha högst fem diagnostikinställningar. Mer information finns i [diagnostikinställningar](../../azure-monitor/essentials/diagnostic-settings.md).
- Fel. Resursen har kon figurer ATS för att skicka loggar till Datadog, men blockeras av ett fel.
- Loggarna är inte konfigurerade. Endast Azure-resurser som har lämpliga resurs taggar är konfigurerade för att skicka loggar till Datadog.
- Regionen stöds inte. Azure-resursen finns i en region som inte har stöd för att skicka loggar till Datadog.
- Datadog-agenten har inte kon figurer ATS. Virtuella datorer utan Datadog-agenten genererar inte loggar till Datadog.

## <a name="api-keys"></a>API-nycklar

Om du vill visa en lista över API-nycklar för Datadog-resursen väljer du **nycklarna** i det vänstra fönstret. Du ser information om nycklarna.

:::image type="content" source="media/manage/keys.png" alt-text="API-nycklar för Datadog-organisationen." border="true":::

Azure Portal tillhandahåller en skrivskyddad vy av API-nycklarna. Om du vill hantera nycklarna väljer du Datadog Portal-länken. När du har gjort ändringar i Datadog-portalen uppdaterar du vyn Azure Portal.

Med Azure Datadog-integreringen kan du installera Datadog-agenten på en virtuell dator eller App Service. Om en standard nyckel inte är markerad Miss lyckas installationen av Datadog-agenten.

## <a name="monitor-virtual-machines-using-the-datadog-agent"></a>Övervaka virtuella datorer med Datadog-agenten

Du kan installera Datadog-agenter på virtuella datorer som tillägg. Gå till **agenten för virtuella datorer** under Datadog org-konfigurationer i det vänstra fönstret. Den här skärmen visar en lista över alla virtuella datorer i prenumerationen.

Följande data visas för varje virtuell dator:

- Resurs namn – namn på virtuell dator
- Resurs status – om den virtuella datorn har stoppats eller körs. Datadog-agenten kan bara installeras på virtuella datorer som kör. Om den virtuella datorn stoppas kommer installationen av Datadog-agenten att inaktive ras.
- Agent version – versions numret för Datadog-agenten.
- Agent status – om Datadog-agenten körs på den virtuella datorn.
- Integreringar aktiverade – de viktigaste måtten som samlas in av Datadog-agenten.
- Installations metod – det verktyg som används för att installera Datadog-agenten. Till exempel chef eller skript.
- Skicka loggar – om Datadog-agenten skickar loggar till Datadog.

Välj den virtuella dator där Datadog-agenten ska installeras. Välj **Installera agent**.

Portalen ber om bekräftelse på att du vill installera agenten med standard nyckeln. Välj **OK** för att påbörja installationen. Azure visar statusen som **installeras** tills agenten är installerad och etablerad.

När Datadog-agenten har installerats ändras statusen till installerad.

Om du vill se att Datadog-agenten har installerats väljer du den virtuella datorn och navigerar till fönstret tillägg.

Du kan avinstallera Datadog-agenter på en virtuell dator genom att gå till den **virtuella dator agenten**. Välj den virtuella datorn och **Avinstallera agent**.

## <a name="monitor-app-services-using-the-datadog-agent-as-an-extension"></a>Övervaka App Services med Datadog-agenten som ett tillägg

Du kan installera Datadog-agenter på App Services som ett tillägg. Gå till **App Service tillägget** i det vänstra fönstret. Den här skärmen visar en lista över alla app Services i prenumerationen.

Följande data element visas för varje App Service:

- Resurs namn – namn på virtuell dator.
- Resurs status – oavsett om App Service har stoppats eller körs. Datadog-agenten kan bara installeras på App Services som kör. Om App Service stoppas inaktive ras installationen av Datadog-agenten.
- App Service-plan – den speciella plan som kon figurer ATS för App Service.
- Agent version – versions numret för Datadog-agenten.

Om du vill installera Datadog-agenten väljer du app service och **installera tillägg**. Den senaste Datadog-agenten är installerad på App Service som ett tillägg.

Portalen bekräftar att du vill installera Datadog-agenten. Program inställningarna för den specifika app-tjänsten uppdateras också med standard nyckeln. App Service startas om efter att installationen av Datadog-agenten har slutförts.

Välj **OK** för att påbörja installations processen för Datadog-agenten. Portalen visar statusen som **installeras** tills agenten har installerats. När Datadog-agenten har installerats ändras statusen till installerad.

Om du vill avinstallera Datadog-agenter på App Service går du till **App Service tillägget**. Välj app service och **avinstallations tillägg**

## <a name="reconfigure-single-sign-on"></a>Konfigurera om enkel inloggning

Om du vill konfigurera om enkel inloggning väljer du **enkel inloggning** i det vänstra fönstret.

Om du vill upprätta enkel inloggning via Azure Active Directory väljer du **aktivera enkel inloggning via Azure Active Directory**.

Portalen hämtar lämpligt Datadog-program från Azure Active Directory. Appen kommer från namnet på den företags app som du valde när du ställde in integrering. Välj Datadog-appens namn enligt nedan:
 
:::image type="content" source="media/manage/reconfigure-single-sign-on.png" alt-text="Konfigurera om programmet för enkel inloggning." border="true":::
 
## <a name="disable-or-enable-integration"></a>Inaktivera eller aktivera integrering

Du kan sluta skicka loggar och mått från Azure till Datadog. Du kommer att fortsätta debiteras för andra Datadog-tjänster som inte är relaterade till övervaknings mått och loggar.

Om du vill inaktivera Azure-integrering med Datadog går du till **Översikt**. Välj **inaktivera** och **OK**.
 
:::image type="content" source="media/manage/disable.png" alt-text="Inaktivera Datadog-resurs." border="true":::

Om du vill aktivera Azure-integrering med Datadog går du till **Översikt**. Välj **Aktivera** och **OK**. Om du väljer **Aktivera** hämtas all tidigare konfiguration för mått och loggar. Konfigurationen avgör vilka Azure-resurser som genererar mått och loggar till Datadog. När du har slutfört steget skickas mått och loggar till Datadog.
 
:::image type="content" source="media/manage/enable.png" alt-text="Aktivera Datadog-resurs." border="true":::

## <a name="delete-datadog-resource"></a>Ta bort Datadog-resurs

Gå till **Översikt** i vänster fönster och välj **ta bort**. Bekräfta att du vill ta bort Datadog-resursen. Välj **Ta bort**.

:::image type="content" source="media/manage/delete.png" alt-text="Ta bort Datadog-resurs" border="true":::

Om det bara finns en Datadog-resurs som är mappad till en Datadog-organisation skickas inte loggar och mått längre till Datadog. Alla fakturerings steg för Datadog via Azure Marketplace.

Om fler än en Datadog-resurs är mappad till Datadog-organisationen, kommer borttagning av Datadog-resursen att sluta skicka loggar och mått för den Datadog resursen. Eftersom Datadog-organisationen är länkad till andra Azure-resurser fortsätter faktureringen via Azure Marketplace.

## <a name="getting-support"></a>Få support

Om du vill kontakta supporten om Azure Datadog-integrering väljer du **nytt supportbegäran** i den vänstra rutan. Välj länken till Datadog-portalen.

:::image type="content" source="media/manage/support-request.png" alt-text="Skapa en ny Datadog support förfrågan" border="true":::

## <a name="next-steps"></a>Nästa steg

Hjälp med fel sökning finns i [Felsöka Datadog-lösningar](troubleshoot.md).