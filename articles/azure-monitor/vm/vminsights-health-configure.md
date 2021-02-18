---
title: Konfigurera övervakning i Azure Monitor for VMs gäst hälsa (för hands version)
description: Beskriver hur du ändrar standard övervakning för Azure Monitor for VMs gäst hälsa (för hands version) med hjälp av Azure Portal.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/14/2020
ms.openlocfilehash: 427bdec2b5e5ab14d566375d5ad8f9da9dc3e81b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100625618"
---
# <a name="configure-monitoring-in-azure-monitor-for-vms-guest-health-preview"></a>Konfigurera övervakning i Azure Monitor for VMs gäst hälsa (för hands version)
Azure Monitor for VMs gäst hälsa kan du se hälso tillståndet för en virtuell dator så som den definieras av en uppsättning prestanda mätningar som samplas med jämna mellanrum. I den här artikeln beskrivs hur du kan ändra standard övervakning med hjälp av Azure Portal. Det beskriver också grundläggande koncept för Övervakare som krävs för att [Konfigurera övervakning med en data insamlings regel](vminsights-health-configure-dcr.md).

## <a name="open-monitor-configuration"></a>Öppna övervaknings konfiguration
Öppna konfigurations lager plats för övervakning Azure Portal genom att välja övervakaren och sedan fliken **konfiguration** .

[![Konfiguration av övervaka Detaljer](media/vminsights-health-overview/monitor-details-configuration.png)](media/vminsights-health-overview/monitor-details-configuration.png#lightbox)

## <a name="enable-or-disable-monitors"></a>Aktivera eller inaktivera övervakare
Både enhets övervakare och sammanställda övervakare har status inställningar för **hälso tillstånd** som gör att du kan aktivera eller inaktivera övervakaren. När en övervakare är aktive rad visas dess hälsa och används för att ställa in hälso tillståndet för den virtuella datorn. När en övervakare är inaktive rad beräknas inte dess hälsa och används inte för att ange hälso tillståndet för den virtuella datorn.

| Inställning | Beskrivning |
|:---|:---|
| Enabled | Övervakaren är aktive rad oavsett inställningen för dess överordnade. |
| Inaktiverad | Övervakaren är inaktive rad oavsett inställningen för dess överordnade. |
| Samma som överordnad | Övervakaren kommer att aktive ras eller inaktive ras beroende på inställningen för dess överordnade. |

När en övervakare är inaktive rad visas alla villkor som de inte är tillgängliga som de visas i följande exempel.

![Övervakaren är inaktive rad](media/vminsights-health-configure/disabled-monitor.png)


> [!NOTE]
> Om en överordnad övervakare har inaktiverats inaktive ras även alla underordnade övervakare. Om du aktiverar den underordnade övervakaren explicit, aktive ras den överordnade även, men dess konfigurations status förblir densamma. I det här fallet visas följande meddelande i den överordnade övervakaren.
>
> *Det finns en avvikelse som övervakarens konfigurerade status är inaktive rad men hälso tillståndet visar inte att. Detta beror på att de konfigurerade ändringarna sprids eller att alla dess underordnade övervakare uttryckligen har Aktiver ATS.*

## <a name="enable-or-disable-virtual-machine"></a>Aktivera eller inaktivera virtuell dator
Du kan inaktivera övervakning för en virtuell dator för att tillfälligt stoppa alla Övervakare. Du kan till exempel inaktivera övervakning för en virtuell dator, till exempel när du utför underhåll på den.

| Inställning | Beskrivning |
|:---|:---|
| Enabled  | Datorns hälso tillstånd visas. |
| Inaktiverad | Datorns hälso tillstånd visas som **inaktiverat**. Aviseringar skapas inte. |

## <a name="health-state-logic"></a>Hälso tillstånds logik
Hälso tillstånds logiken för en enhets övervakare definierar kriterierna för att ställa in hälso tillståndet. Du kan ange hur många hälso tillstånd som en övervakare har och tröskelvärdet för hur varje hälso tillstånd beräknas.

![Exempel på hälso kriterier](media/vminsights-health-configure/sample-health-criteria.png)

Sammanställda övervakare anger ingen hälso tillstånds logik. Deras hälso tillstånd anges av enhets övervakarna under dem enligt deras hälso tillstånds sammanslagning.

Enhets övervakare har två eller tre hälso tillstånd. Var och en kommer alltid att ha ett felfritt tillstånd och eventuellt ett varnings tillstånd, ett kritiskt tillstånd eller både och. Varnings-och kritisk tillstånd varje kräver unika villkor som definierar när övervakaren anges till det här tillståndet. Det felfria tillståndet har inga kriterier eftersom det här tillståndet anges när villkoren för de andra tillstånden inte är uppfyllda.

I följande exempel anges processor användningen till följande hälso tillstånd:

- Kritisk om den är större än 90%.
- Varning om den är större än eller lika med 80%.
- Felfri om den är under 80%. Detta är underförstådd eftersom det är villkoret där inget av de andra villkoren gäller.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera övervakare i skala med hjälp av data insamlings regler.](vminsights-health-configure-dcr.md)