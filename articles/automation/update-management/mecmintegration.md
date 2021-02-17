---
title: Integrera Azure Automation Uppdateringshantering med Microsoft Endpoint Configuration Manager
description: Den här artikeln beskriver hur du konfigurerar Microsoft Endpoint Configuration Manager med Uppdateringshantering för att distribuera program uppdateringar till Manager-klienter.
services: automation
ms.subservice: update-management
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: a848c7c15bf786ba26b8a1fdb1dab41b9aa20b8d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575774"
---
# <a name="integrate-update-management-with-microsoft-endpoint-configuration-manager"></a>Integrera Uppdateringshantering med Microsoft Endpoint Configuration Manager

Kunder som har investerat i Microsoft Endpoint Configuration Manager för att hantera datorer, servrar och mobila enheter är också beroende av dess styrka och mognad vid hantering av program uppdateringar som en del av program uppdaterings hanterings cykeln (SUM).

Du kan rapportera och uppdatera hanterade Windows-servrar genom att skapa och förinstallera distributioner av program uppdateringar i Microsoft Endpoint Configuration Manager och få detaljerad status för slutförda uppdaterings distributioner med hjälp av [uppdateringshantering](overview.md). Om du använder Microsoft Endpoint Configuration Manager för uppdatering av Kompatibilitetsrapport, men inte för att hantera uppdaterings distributioner med dina Windows-servrar, kan du fortsätta att rapportera till Microsoft Endpoint Configuration Manager medan säkerhets uppdateringar hanteras med Azure Automation Uppdateringshantering.

>[!NOTE]
>Även om Uppdateringshantering stöder uppdaterings bedömning och uppdatering av Windows Server 2008 R2, stöder den inte klienter som hanteras av Microsoft Endpoint Configuration Manager som kör det här operativ systemet.

## <a name="prerequisites"></a>Förutsättningar

* Du måste ha [Azure Automation uppdateringshantering](overview.md) lagts till i ditt Automation-konto.
* Windows-servrar som för närvarande hanteras av din Microsoft Endpoint Configuration Manager-miljö måste också rapportera till Log Analytics-arbetsytan som också har Uppdateringshantering aktiverat.
* Den här funktionen är aktive rad i Microsoft Endpoint Configuration Manager aktuell gren version 1606 och högre. Om du vill integrera din Microsoft-slutpunkt Configuration Manager Central administrations plats eller en fristående primär plats med Azure Monitor loggar och importera samlingar, granskar [du anslut Configuration Manager till Azure Monitor loggar](../../azure-monitor/logs/collect-sccm.md).  
* Windows-agenter måste antingen konfigureras för att kommunicera med en Windows Server Update Services-server (WSUS) eller ha åtkomst till Microsoft Update om de inte tar emot säkerhets uppdateringar från Microsoft Endpoint Configuration Manager.

Hur du hanterar klienter som finns i Azure-IaaS med din befintliga Microsoft Endpoint Configuration Manager-miljö är främst beroende av anslutningen du har mellan Azure-datacenter och din infrastruktur. Den här anslutningen påverkar alla design ändringar som du kan behöva göra i Microsoft Endpoint Configuration Manager-infrastrukturen och relaterade kostnader för att stödja de nödvändiga ändringarna. För att förstå vilka överväganden för planering du behöver utvärdera innan du fortsätter kan du läsa [vanliga frågor och svar om Configuration Manager på Azure](/configmgr/core/understand/configuration-manager-on-azure#networking).

## <a name="manage-software-updates-from-microsoft-endpoint-configuration-manager"></a>Hantera program uppdateringar från Microsoft Endpoint Configuration Manager

Utför följande steg om du ska fortsätta att hantera uppdaterings distributioner från Microsoft Endpoint Configuration Manager. Azure Automation ansluter till Microsoft Endpoint Configuration Manager för att tillämpa uppdateringar på klient datorerna som är anslutna till din Log Analytics-arbetsyta. Uppdaterings innehåll är tillgängligt från klient datorns cache som om distributionen hanterades av Microsoft Endpoint Configuration Manager.

1. Skapa en program uppdaterings distribution från platsen på den översta nivån i din Microsoft Endpoint Configuration Manager-hierarki med hjälp av processen som beskrivs i [distribuera program uppdateringar](/configmgr/sum/deploy-use/deploy-software-updates). Den enda inställning som måste vara konfigurerad annorlunda än en standarddistribution är alternativet **Installera inte programuppdateringar** för att kontrollera distributionspaketets hämtningsbeteende. Detta beteende hanteras i Uppdateringshantering genom att en schemalagd uppdaterings distribution skapas i nästa steg.

2. I Azure Automation väljer du **uppdateringshantering**. Skapa en ny distribution genom att följa stegen som beskrivs i [skapa en uppdaterings distribution](deploy-updates.md#schedule-an-update-deployment) och välj **importerade grupper** i list rutan **typ** för att välja lämplig Microsoft Endpoint Configuration Manager-samling. Tänk på följande viktiga punkter:

    a. Om ett underhålls fönster har definierats på den valda enhets samlingen för Microsoft Endpoint Configuration Manager, följer medlemmarna i samlingen den i stället för **varaktighets** inställningen som definierats i den schemalagda distributionen.

    b. Medlemmar i mål samlingen måste ha en anslutning till Internet (antingen direkt, via en proxyserver eller via Log Analytics Gateway).

När du har slutfört uppdaterings distributionen via Azure Automation kommer de mål datorer som är medlemmar i den valda dator gruppen att installera uppdateringar vid den schemalagda tiden från deras lokala klientcachen. Du kan [visa status för uppdateringsdistributionen](deploy-updates.md#check-deployment-status) för att övervaka resultatet av distributionen.

## <a name="manage-software-updates-from-azure-automation"></a>Hantera program uppdateringar från Azure Automation

Om du vill hantera uppdateringar för virtuella Windows Server-datorer som är Microsoft Endpoint Configuration Manager-klienter, måste du konfigurera klient principen för att inaktivera funktionen hantering av programuppdateringar för alla klienter som hanteras av Uppdateringshantering. Som standard riktar sig klientinställningarna till alla enheter i hierarkin. Mer information om den här princip inställningen och hur du konfigurerar den finns [i Konfigurera klient inställningar i Configuration Manager](/configmgr/core/clients/deploy/configure-client-settings).

När du har genomfört den här konfigurations ändringen skapar du en ny distribution genom att följa stegen som beskrivs i [skapa en uppdaterings distribution](deploy-updates.md#schedule-an-update-deployment) och välja **importerade grupper** i list rutan **typ** för att välja lämplig Microsoft Endpoint Configuration Manager-samling.

## <a name="next-steps"></a>Nästa steg

Information om hur du konfigurerar ett integrations schema finns i [Schemalägga en uppdaterings distribution](deploy-updates.md#schedule-an-update-deployment).
