---
title: Backa upp en SharePoint-servergrupp till Azure med DPM
description: Den här artikeln innehåller en översikt över DPM/Azure Backup serverskydd för en SharePoint-servergrupp till Azure
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 3524107b545c151fcf931b89c629a61d83f47ace
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515166"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-dpm"></a>Backa upp en SharePoint-servergrupp till Azure med DPM

Du kan servera en SharePoint-servergrupp Microsoft Azure genom att använda System Center Data Protection Manager (DPM) på ungefär samma sätt som när du använde andra datakällor. Azure Backup ger flexibilitet i säkerhetskopieringsschemat för att skapa dagliga, veckovisa, månatliga eller årliga säkerhetskopieringspunkter och ger dig kvarhållningsprincipalternativ för olika säkerhetskopieringspunkter. DPM tillhandahåller möjligheten att lagra lokala diskkopior för snabba återställningstidsmål (RTO) och att lagra kopior i Azure för ekonomisk och långsiktig kvarhållning.

Att backa upp SharePoint till Azure med DPM är en process som påminner om hur du backar upp SharePoint till DPM lokalt. Särskilda överväganden för Azure beskrivs i den här artikeln.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>SharePoint-versioner som stöds och relaterade skyddsscenarier

En lista över SharePoint-versioner som stöds och DPM-versioner som krävs för att säkerhetskopiera dem finns i [Vad kan DPM säkerhetskopiera?](/system-center/dpm/dpm-protection-matrix#applications-backup)

## <a name="before-you-start"></a>Innan du börjar

Det finns några saker som du måste bekräfta innan du kan servera en SharePoint-servergrupp till Azure.

### <a name="prerequisites"></a>Förutsättningar

Innan du fortsätter måste du se till att du har uppfyllt alla krav för [att Microsoft Azure Backup](backup-azure-dpm-introduction.md#prerequisites-and-limitations) skydda arbetsbelastningar. Vissa uppgifter för förutsättningar är: skapa ett säkerhetskopieringsvalv, ladda ned valvautentiseringsuppgifter, installera Azure Backup Agent och registrera DPM/Azure Backup Server med valvet.

Ytterligare krav och begränsningar finns i artikeln Back up SharePoint with DPM ( [Back up SharePoint with DPM).](/system-center/dpm/back-up-sharepoint#prerequisites-and-limitations)

## <a name="configure-backup"></a>Konfigurera säkerhetskopiering

Om du vill säkerhetskopiera SharePoint-servergruppen konfigurerar du skydd för SharePoint med hjälp av ConfigureSharePoint.exe och skapar sedan en skyddsgrupp i DPM. Anvisningar finns i Konfigurera [säkerhetskopiering](/system-center/dpm/back-up-sharepoint#configure-backup) i DPM-dokumentationen.

## <a name="monitoring"></a>Övervakning

Om du vill övervaka säkerhetskopieringsjobbet följer du anvisningarna i [Övervaka DPM-säkerhetskopiering](/system-center/dpm/back-up-sharepoint#monitoring)

## <a name="restore-sharepoint-data"></a>Återställa SharePoint-data

Information om hur du återställer ett SharePoint-objekt från en disk med DPM finns i [Återställa SharePoint-data.](/system-center/dpm/back-up-sharepoint#restore-sharepoint-data)

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Återställa en SharePoint-databas från Azure med hjälp av DPM

1. Om du vill återställa en SharePoint-innehållsdatabas bläddrar du igenom olika återställningspunkter (som du visade tidigare) och väljer den återställningspunkt som du vill återställa.

    ![DPM SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Dubbelklicka på SharePoint-återställningspunkten för att visa tillgänglig information om SharePoint-katalogen.

   > [!NOTE]
   > Eftersom SharePoint-servergruppen skyddas för långsiktig kvarhållning i Azure är ingen kataloginformation (metadata) tillgänglig på DPM-servern. När en SharePoint-innehållsdatabas behöver återställas till en tidpunkt måste du därför katalogisera SharePoint-servergruppen igen.
   >
   >
3. Välj **Omkatalogisera**.

    ![DPM SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Statusfönstret **Cloud Recatalog** öppnas.

    ![DPM SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    När katalogisering är klar ändras statusen till *Lyckades.* Välj **Stäng**.

    ![DPM SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Välj det SharePoint-objekt som visas på fliken **DPM-återställning** för att hämta innehållsdatabasstrukturen. Högerklicka på objektet och välj sedan **Återställ.**

    ![DPM SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Följ nu återställningsstegen tidigare i den här artikeln för att återställa en SharePoint-innehållsdatabas från disk.

## <a name="switching-the-front-end-web-server"></a>Växla Front-End webbserver

Om du har fler än en frontend-webbserver och vill växla den server som DPM använder för att skydda servergruppen, följer du anvisningarna i [Växla Front-End server](/system-center/dpm/back-up-sharepoint#switching-the-front-end-web-server).

## <a name="next-steps"></a>Nästa steg

* [Azure Backup Server och DPM – vanliga frågor och svar](backup-azure-dpm-azure-server-faq.yml)
* [Felsöka System Center Data Protection Manager](backup-azure-scdpm-troubleshooting.md)
