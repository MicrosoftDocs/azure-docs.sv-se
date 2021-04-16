---
title: Återställa data från en Azure Backup Server
description: Återställ de data som du har skyddat till ett Recovery Services-valv från alla Azure Backup Server registrerade i valvet.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 780c88175397fb06e704e57062ae5c6d3b93d8b8
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519596"
---
# <a name="recover-data-from-azure-backup-server"></a>Återställa data från Azure Backup Server

Du kan använda Azure Backup Server för att återställa data som du har säkerhetskopierat till ett Recovery Services-valv. Processen för att göra detta är integrerad i Azure Backup Server-hanteringskonsolen och liknar återställningsarbetsflödet för andra Azure Backup komponenter.

> [!NOTE]
> Den här artikeln gäller [för System Center Data Protection Manager 2012 R2](https://support.microsoft.com/kb/3065246)med UR7 eller senare , kombinerat med den [senaste Azure Backup agenten](https://aka.ms/azurebackup_agent).
>
>

Så här återställer du data från en Azure Backup Server:

1. På fliken **Återställning** i Azure Backup Server väljer du **Lägg till extern DPM** (längst upp till vänster på skärmen).

    ![Lägg till extern DPM](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Ladda  ned nya autentiseringsuppgifter för valv från valvet som är associerat med **Azure Backup Server** där data återställs, välj Azure Backup Server i  listan över Azure Backup-servrar som är registrerade med Recovery Services-valvet och ange krypteringsfrasen som är associerad med den server vars data återställs.

    ![Externa DPM-autentiseringsuppgifter](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Endast Azure Backup servrar som är associerade med samma registreringsvalv kan återställa varandras data.
   >
   >

    När den externa Azure Backup Server har lagts till kan du bläddra bland data för den externa servern och den lokala Azure Backup Server på **fliken** Återställning.
3. Bläddra i den tillgängliga listan över produktionsservrar som skyddas av den externa Azure Backup Server och välj lämplig datakälla.

    ![Bläddra i extern DPM-server](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. Välj **månad och år** i **listrutan** Återställningspunkter, välj det återställningsdatum som krävs för när återställningspunkten skapades och välj **Återställningstid.** 

    En lista över filer och mappar visas i det nedre fönstret, som kan bläddras och återställas till valfri plats.

    ![Återställningspunkter för extern DPM-server](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Högerklicka på lämpligt objekt och välj **Återställ.**

    ![Extern DPM-återställning](./media/backup-azure-alternate-dpm-server/recover.png)
6. Granska **Återställningsval.** Kontrollera data och tid för säkerhetskopieringskopian som återställs, samt den källa som säkerhetskopian skapades från. Om valet är felaktigt väljer du **Avbryt för** att gå tillbaka till återställningsfliken och välja lämplig återställningspunkt. Om valet är korrekt väljer du **Nästa.**

    ![Sammanfattning av extern DPM-återställning](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. Välj **Återställ till en alternativ plats.** **Bläddra** till rätt plats för återställningen.

    ![Extern alternativ plats för DPM-återställning](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Välj det alternativ som är **relaterat till** att **skapa** kopia, Hoppa över eller **Skriva över**.

   * **Skapa kopia** – skapar en kopia av filen om det uppstår en namnkollision.
   * **Hoppa** över – om det finns en namnkollision återställs inte filen, vilket lämnar den ursprungliga filen.
   * **Skriva över –** om det finns en namnkollision skriver den över den befintliga kopian av filen.

     Välj lämpligt alternativ för Att **återställa säkerheten**. Du kan tillämpa säkerhetsinställningarna på måldatorn där data återställs eller de säkerhetsinställningar som var tillämpliga för produkten när återställningspunkten skapades.

     Identifiera om ett **meddelande** har skickats när återställningen har slutförts.

     ![Externa DPM-återställningsmeddelanden](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. På **skärmen** Sammanfattning visas de alternativ som valts hittills. När du **har valt** Återställ återställs data till lämplig lokal plats.

    ![Sammanfattning av alternativ för extern DPM-återställning](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > Återställningsjobbet kan övervakas **på fliken** Övervakning i Azure Backup Server.
   >
   >

    ![Övervaka återställning](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. Du kan välja **Rensa extern DPM** på fliken **Återställning** på DPM-servern för att ta bort vyn för den externa DPM-servern.

    ![Rensa extern DPM](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>Felsöka felmeddelanden

| Nej. | Felmeddelande | Felsökningsanvisningar |
|:---:|:--- |:--- |
| 1. |Den här servern är inte registrerad på det valv som anges av valvaudentialen. |**Orsak:** Det här felet visas när valvaudentialfilen som valts inte tillhör det Recovery Services-valv som är associerat Azure Backup Server där återställningen görs. <br> **Lösning:** Ladda ned valvaudentialfilen från Recovery Services-valvet som Azure Backup Server har registrerats till. |
| 2. |Antingen är återställningsbara data inte tillgängliga eller så är den valda servern inte en DPM-server. |**Orsak:** Det finns inga andra Azure Backup-servrar som är registrerade i Recovery Services-valvet eller servrarna har ännu inte laddat upp metadata, eller så är den valda servern inte en Azure Backup Server (med Windows Server eller Windows-klient). <br> **Lösning:** Om det finns andra Azure Backup-servrar som är registrerade i Recovery Services-valvet ser du till att den Azure Backup agenten är installerad. <br>Om det finns andra Azure Backup som är registrerade i Recovery Services-valvet väntar du en dag efter installationen för att starta återställningsprocessen. Nattjobbet laddar upp metadata för alla skyddade säkerhetskopior till molnet. Data kommer att vara tillgängliga för återställning. |
| 3. |Ingen annan DPM-server har registrerats för det här valvet. |**Orsak:** Det finns inga Azure Backup servrar som är registrerade i valvet som återställningen görs från.<br>**Lösning:** Om det finns andra Azure Backup-servrar som är registrerade i Recovery Services-valvet ser du till att den Azure Backup agenten är installerad.<br>Om det finns andra Azure Backup-servrar som är registrerade i Recovery Services-valvet väntar du en dag efter installationen för att starta återställningsprocessen. Nattjobbet laddar upp metadata för alla skyddade säkerhetskopior till molnet. Data kommer att vara tillgängliga för återställning. |
| 4. |Den angivna krypteringsfrasen matchar inte lösenfrasen som är associerad med följande server: **\<server name>** |**Orsak:** Den krypteringsfras som används vid kryptering av data från Azure Backup Server-data som återställs matchar inte den angivna krypteringsfrasen. Agenten kan inte dekryptera data, så återställningen misslyckas.<br>**Lösning:** Ange exakt samma krypteringsfras som är associerad med Azure Backup Server vars data återställs. |

## <a name="next-steps"></a>Nästa steg

Läs andra vanliga frågor och svar:

* [Vanliga frågor om](backup-azure-vm-backup-faq.yml) säkerhetskopiering av virtuella Azure-datorer
* [Vanliga frågor](backup-azure-file-folder-backup-faq.yml) om Azure Backup-agenten
