---
title: Windows Virtual Desktop (klassisk) Diagnostic Log Analytics – Azure
description: Hur du använder Log Analytics med hjälp av funktionen Windows Virtual Desktop (klassisk).
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: bfe8026f099c341b68a1aa1fc5e3fac425e29403
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100578903"
---
# <a name="use-log-analytics-for-the-diagnostics-feature-in-windows-virtual-desktop-classic"></a>Använd Log Analytics för funktionen diagnostik i Windows Virtual Desktop (klassisk)

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer (klassisk), vilket inte stöder Azure Resource Manager virtuella Skriv bords objekt i Windows. Om du försöker hantera Azure Resource Manager virtuella Windows Desktop-objekt, se [den här artikeln](../diagnostics-log-analytics.md).

Windows Virtual Desktop erbjuder en diagnostisk funktion som gör det möjligt för administratören att identifiera problem via ett enda gränssnitt. Den här funktionen loggar diagnostikinformation när någon har tilldelat en virtuell Windows-dator roll som använder tjänsten. Varje logg innehåller information om vilken Windows-roll för virtuella skriv bord som ingick i aktiviteten, eventuella fel meddelanden som visas under sessionen, klient information och användar information. Funktionen diagnostik skapar aktivitets loggar för både användar-och administrations åtgärder. Varje aktivitets logg faller under tre huvud kategorier:

- Flödes prenumerations aktiviteter: när en användare försöker ansluta till sin feed genom att Microsoft Fjärrskrivbord program.
- Anslutnings aktiviteter: när en användare försöker ansluta till en stationär eller RemoteApp via Microsoft Fjärrskrivbord-program.
- Hanterings aktiviteter: när en administratör utför hanterings åtgärder i systemet, till exempel skapa värdar, tilldela användare till app-grupper och skapa roll tilldelningar.

Anslutningar som inte når Windows Virtual Desktop visas inte i diagnostiska resultat, eftersom själva roll tjänsten för diagnostik är en del av det virtuella Windows-skrivbordet. Problem med Windows Virtual Desktop-anslutning kan inträffa när användaren har problem med nätverks anslutningen.

## <a name="why-you-should-use-log-analytics"></a>Varför du bör använda Log Analytics

Vi rekommenderar att du använder Log Analytics för att analysera diagnostikdata i Azure-klienten som går bortom fel sökning av enskilda användare. Eftersom du kan hämta prestanda räknare för virtuella datorer i Log Analytics du ett verktyg för att samla in information för distributionen.

## <a name="before-you-get-started"></a>Innan du börjar

Innan du kan använda Log Analytics med funktionen diagnostik måste du [skapa en arbets yta](../../azure-monitor/vm/quick-collect-windows-computer.md#create-a-workspace).

När du har skapat arbets ytan följer du anvisningarna i [Anslut Windows-datorer till Azure Monitor](../../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key) för att få följande information:

- Arbetsyte-ID
- Den primära nyckeln för din arbets yta

Du behöver den här informationen senare i installations processen.

## <a name="push-diagnostics-data-to-your-workspace"></a>Skicka diagnostikdata till din arbets yta

Du kan skicka diagnostikdata från din Windows-klient för virtuella datorer till Log Analytics för din arbets yta. Du kan ställa in den här funktionen direkt när du först skapar din klient genom att länka arbets ytan till din klient organisation, eller så kan du ställa in den senare med en befintlig klient.

Om du vill länka din klient till din Log Analytics arbets yta när du konfigurerar din nya klient kör du följande cmdlet för att logga in på Windows Virtual Desktop med ditt TenantCreator-användar konto:

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
```

Om du ska länka en befintlig klient i stället för en ny klient kör du denna cmdlet i stället:

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String>
```

Du måste köra dessa cmdlets för varje klient som du vill länka till Log Analytics.

>[!NOTE]
>Om du inte vill länka Log Analytics arbets ytan när du skapar en klient kör du `New-RdsTenant` cmdleten i stället.

## <a name="cadence-for-sending-diagnostic-events"></a>Takt för att skicka diagnostiska händelser

Diagnostiska händelser skickas till Log Analytics när de är klara.

## <a name="example-queries"></a>Exempelfrågor

Följande exempel frågor visar hur Diagnostics-funktionen genererar en rapport för de mest frekventa aktiviteterna i systemet:

I det första exemplet visas anslutnings aktiviteter som initieras av användare med stöd för fjärr skrivbords klienter:

```powershell
WVDActivityV1_CL

| where Type_s == "Connection"

| join kind=leftouter (

    WVDErrorV1_CL

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g 

| join  kind=leftouter (

    WVDCheckpointV1_CL

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g

|project-away ActivityId_g, ActivityId_g1
```

I nästa exempel fråga visas hanterings aktiviteter av administratörer på klienterna:

```powershell
WVDActivityV1_CL

| where Type_s == "Management"

| join kind=leftouter (

    WVDErrorV1_CL

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g 

| join  kind=leftouter (

    WVDCheckpointV1_CL

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g

|project-away ActivityId_g, ActivityId_g1
```

## <a name="stop-sending-data-to-log-analytics"></a>Sluta skicka data till Log Analytics

Om du vill stoppa sändningen av data från en befintlig klient organisation till Log Analytics kör du följande cmdlet och anger tomma strängar:

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String>
```

Du måste köra denna cmdlet för varje klient som du vill sluta skicka data från.

## <a name="next-steps"></a>Nästa steg

Se [identifiera och diagnostisera problem](diagnostics-role-service-2019.md#common-error-scenarios)för att granska vanliga fel scenarier som kan identifieras av diagnostikprogrammet.
