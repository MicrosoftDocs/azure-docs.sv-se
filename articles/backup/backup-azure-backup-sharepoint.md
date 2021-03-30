---
title: Säkerhetskopiera en SharePoint-grupp till Azure med DPM
description: Den här artikeln innehåller en översikt över DPM/Azure Backup Server skydd för en SharePoint-grupp till Azure
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 7661d64e487c8b8badca240852d17bcf736ba8cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91254439"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-dpm"></a>Säkerhetskopiera en SharePoint-grupp till Azure med DPM

Du säkerhetskopierar en SharePoint-grupp till Microsoft Azure med hjälp av System Center Data Protection Manager (DPM) på samma sätt som du säkerhetskopierar andra data källor. Azure Backup ger flexibilitet i schemat för säkerhets kopiering för att skapa dagliga, vecko Visa eller årliga säkerhets kopierings punkter och ger dig bevarande princip alternativ för olika säkerhets kopierings punkter. DPM ger möjlighet att lagra lokala disk kopior för snabba återställnings mål (RTO) och för att lagra kopior till Azure för ekonomisk, långsiktig kvarhållning.

Att säkerhetskopiera SharePoint till Azure med DPM är en mycket liknande process för att säkerhetskopiera SharePoint till DPM lokalt. Särskilda överväganden för Azure kommer att anges i den här artikeln.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>SharePoint-versioner som stöds och relaterade skydds scenarier

En lista över SharePoint-versioner som stöds och DPM-versioner som krävs för att säkerhetskopiera dem finns i [Vad kan DPM säkerhetskopiera?](/system-center/dpm/dpm-protection-matrix#applications-backup)

## <a name="before-you-start"></a>Innan du börjar

Det finns några saker du behöver bekräfta innan du säkerhetskopierar en SharePoint-servergrupp till Azure.

### <a name="prerequisites"></a>Förutsättningar

Innan du fortsätter kontrollerar du att du har uppfyllt alla [krav för att använda Microsoft Azure Backup](backup-azure-dpm-introduction.md#prerequisites-and-limitations) för att skydda arbets belastningar. Vissa uppgifter för krav är: skapa ett säkerhets kopierings valv, Hämta autentiseringsuppgifter för valvet, installera Azure Backup Agent och registrera DPM/Azure Backup Server med valvet.

Ytterligare krav och begränsningar finns i artikeln [säkerhetskopiera SharePoint med DPM](/system-center/dpm/back-up-sharepoint#prerequisites-and-limitations) .

## <a name="configure-backup"></a>Konfigurera säkerhetskopiering

Om du vill säkerhetskopiera SharePoint-servergruppen konfigurerar du skydd för SharePoint med hjälp av ConfigureSharePoint.exe och skapar sedan en skyddsgrupp i DPM. Instruktioner finns i [Konfigurera säkerhets kopiering](/system-center/dpm/back-up-sharepoint#configure-backup) i DPM-dokumentationen.

## <a name="monitoring"></a>Övervakning

Övervaka säkerhets kopierings jobbet genom att följa anvisningarna i [övervaka DPM-säkerhetskopiering](/system-center/dpm/back-up-sharepoint#monitoring)

## <a name="restore-sharepoint-data"></a>Återställa SharePoint-data

Information om hur du återställer ett SharePoint-objekt från en disk med DPM finns i [återställa SharePoint-data](/system-center/dpm/back-up-sharepoint#restore-sharepoint-data).

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Återställa en SharePoint-databas från Azure med hjälp av DPM

1. Du återställer en SharePoint-innehållstyp genom att bläddra igenom olika återställnings punkter (se tidigare) och välja den återställnings punkt som du vill återställa.

    ![DPM SharePoint-Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Dubbelklicka på SharePoint-återställnings punkten för att Visa tillgänglig information om SharePoint-katalogen.

   > [!NOTE]
   > Eftersom SharePoint-servergruppen är skyddad för långsiktig kvarhållning i Azure är ingen katalog information (metadata) tillgänglig på DPM-servern. När en innehålls databas för SharePoint-databasen måste återställas måste du därför katalogisera SharePoint-servergruppen igen.
   >
   >
3. Välj **ny katalog**.

    ![DPM SharePoint-Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Fönstret status för **moln Omkatalogisering** öppnas.

    ![DPM SharePoint-Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    När fil förteckningen har slutförts ändras statusen till *lyckades*. Välj **Stäng**.

    ![DPM SharePoint-Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Välj det SharePoint-objekt som visas på fliken DPM- **återställning** för att hämta struktur för innehålls databasen. Högerklicka på objektet och välj sedan **Återställ**.

    ![DPM SharePoint-Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Följ återställnings stegen tidigare i den här artikeln för att återställa en SharePoint-innehålls databas från disken.

## <a name="switching-the-front-end-web-server"></a>Byta Front-End webb server

Om du har fler än en front webb server och vill byta server som DPM använder för att skydda Server gruppen, följer du anvisningarna i [byta Front-End webb server](/system-center/dpm/back-up-sharepoint#switching-the-front-end-web-server).

## <a name="next-steps"></a>Nästa steg

* [Azure Backup Server och DPM – vanliga frågor och svar](backup-azure-dpm-azure-server-faq.md)
* [Felsöka System Center Data Protection Manager](backup-azure-scdpm-troubleshooting.md)
