---
title: Återställa data från en Azure Backup Server
description: Återställ de data som du har skyddat till ett Recovery Services valv från alla Azure Backup Server som är registrerade på det valvet.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: ed8c937f97ec7a74662a8b46a354b0a6db39a2b0
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98806021"
---
# <a name="recover-data-from-azure-backup-server"></a>Återställa data från Azure Backup Server

Du kan använda Azure Backup Server för att återställa de data som du har säkerhetskopierat till ett Recovery Services-valv. Processen för att göra detta integreras i Azure Backup Server hanterings konsolen och liknar återställnings arbets flödet för andra Azure Backup-komponenter.

> [!NOTE]
> Den här artikeln gäller för [System Center Data Protection Manager 2012 R2 med UR7 eller senare](https://support.microsoft.com/kb/3065246), kombinerat med den [senaste Azure Backup agenten](https://aka.ms/azurebackup_agent).
>
>

Återställa data från en Azure Backup Server:

1. På fliken **återställning** i Azure Backup Server hanterings konsolen väljer du **Lägg till extern DPM** (längst upp till vänster på skärmen).

    ![Lägg till extern DPM](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Hämta nya **autentiseringsuppgifter för valvet** från valvet som är kopplat till **Azure Backup Server** där data återställs, välj Azure Backup Server i listan över Azure Backup servrar som är registrerade i Recovery Services valvet och ange den **krypterings lösen fras** som är kopplad till servern vars data återställs.

    ![Externa autentiseringsuppgifter för DPM](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Endast Azure Backup-servrar som är kopplade till samma registrerings valv kan återställa var and s data.
   >
   >

    När den externa Azure Backup Server har lagts till kan du bläddra i data från den externa servern och den lokala Azure Backup Server från fliken **återställning** .
3. Bläddra i listan över tillgängliga produktions servrar som skyddas av den externa Azure Backup Server och välj lämplig data källa.

    ![Bläddra i extern DPM-server](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. Välj **månad och år** i list rutan **återställnings punkter** , Välj det önskade **återställnings datumet** för när återställnings punkten skapades och välj **återställnings tiden**.

    En lista över filer och mappar visas i det nedre fönstret, som kan bläddras och återställas till valfri plats.

    ![Externa DPM-serverns återställnings punkter](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Högerklicka på lämpligt objekt och välj **Återställ**.

    ![Extern DPM-återställning](./media/backup-azure-alternate-dpm-server/recover.png)
6. Granska valet för att **återställa**. Kontrol lera data och tid för säkerhets kopian som återställs, samt den källa från vilken säkerhets kopian skapades. Om valet är felaktigt väljer du **Avbryt** för att gå tillbaka till fliken återställning för att välja lämplig återställnings punkt. Om valet är korrekt väljer du **Nästa**.

    ![Sammanfattning av extern DPM-återställning](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. Välj **Återställ till en alternativ plats**. **Bläddra** till rätt plats för återställningen.

    ![Alternativ plats för extern DPM-återställning](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Välj alternativet för att **Skapa kopia**, **hoppa över** eller **Skriv över**.

   * **Skapa kopia** – skapar en kopia av filen om det finns en namn konflikt.
   * **Hoppa över** – om det finns en namn konflikt återställs inte filen, vilket innebär att den ursprungliga filen är kvar.
   * **Överskrivning** – om det finns en namn konflikt skrivs den befintliga kopian av filen över.

     Välj lämpligt alternativ för att **återställa säkerheten**. Du kan tillämpa säkerhets inställningarna på mål datorn där data återställs eller de säkerhets inställningar som gällde för produkten vid den tidpunkt då återställnings punkten skapades.

     Identifiera om ett **meddelande** skickas när återställningen har slutförts.

     ![Aviseringar om extern DPM-återställning](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. På sidan **Sammanfattning** visas de alternativ som valts hittills. När du väljer **Återställ** återställs data till lämplig lokal plats.

    ![Sammanfattning av alternativ för extern DPM-återställning](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > Återställnings jobbet kan övervakas på fliken **övervakning** i Azure Backup Server.
   >
   >

    ![Övervaka återställning](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. Du kan välja **Rensa extern DPM** på fliken **återställning** på DPM-servern för att ta bort vyn av den externa DPM-servern.

    ![Rensa extern DPM](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>Fel sökning av fel meddelanden

| Nej. | Felmeddelande | Felsökningsanvisningar |
|:---:|:--- |:--- |
| 1. |Den här servern är inte registrerad på valvet som anges av valvets autentiseringsuppgifter. |**Orsak:** Det här felet visas när den valda valv filen inte tillhör det Recovery Services valv som är associerat med Azure Backup Server där återställningen gjordes. <br> **Lösning:** Hämta valvet från det Recovery Services valv som Azure Backup Server har registrerats för. |
| 2. |Antingen är inte återställnings bara data tillgängliga eller också är den valda servern inte en DPM-server. |**Orsak:** Det finns inga andra Azure Backup-servrar registrerade i Recovery Services-valvet, eller så har servrarna inte överfört metadata än, eller så är den valda servern inte en Azure Backup Server (med Windows Server eller Windows-klient). <br> **Lösning:** Om det finns andra Azure Backup-servrar som är registrerade i Recovery Services-valvet ser du till att den senaste Azure Backup-agenten är installerad. <br>Om det finns andra Azure Backup-servrar som är registrerade i Recovery Services-valvet väntar du en dag efter installationen för att starta återställnings processen. Natt jobbet överför metadata för alla skyddade säkerhets kopieringar till molnet. Data kommer att vara tillgängliga för återställning. |
| 3. |Ingen annan DPM-server är registrerad för det här valvet. |**Orsak:** Det finns inga andra Azure Backup-servrar som är registrerade för valvet som återställningen görs från.<br>**Lösning:** Om det finns andra Azure Backup-servrar som är registrerade i Recovery Services-valvet ser du till att den senaste Azure Backup-agenten är installerad.<br>Om det finns andra Azure Backup-servrar som är registrerade i Recovery Services-valvet väntar du en dag efter installationen för att starta återställnings processen. Natt jobbet överför metadata för alla skyddade säkerhets kopieringar till molnet. Data kommer att vara tillgängliga för återställning. |
| 4. |Den angivna krypterings lösen frasen matchar inte lösen frasen som är associerad med följande Server: **\<server name>** |**Orsak:** Krypterings lösen frasen som används vid kryptering av data från Azure Backup Server data som återställs stämmer inte överens med den krypterings lösen fras som angavs. Agenten kan inte dekryptera data och därför Miss lyckas återställningen.<br>**Lösning:** Ange exakt samma krypterings lösen fras som är kopplad till den Azure Backup Server vars data återställs. |

## <a name="next-steps"></a>Nästa steg

Läs andra vanliga frågor och svar:

* [Vanliga frågor](backup-azure-vm-backup-faq.yml) om säkerhets kopiering av virtuella Azure-datorer
* [Vanliga frågor](backup-azure-file-folder-backup-faq.md) om Azure Backup-agenten
