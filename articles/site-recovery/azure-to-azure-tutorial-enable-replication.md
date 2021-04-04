---
title: Självstudie för att konfigurera haveri beredskap för virtuella Azure-datorer med Azure Site Recovery
description: I den här självstudien konfigurerar du haveri beredskap för virtuella Azure-datorer till en annan Azure-region med hjälp av tjänsten Site Recovery.
ms.topic: tutorial
ms.date: 11/03/2020
ms.custom: mvc
ms.openlocfilehash: 473a264ef497cab4bd4f88372600161b33178099
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97656877"
---
# <a name="tutorial-set-up-disaster-recovery-for-azure-vms"></a>Självstudie: Konfigurera haveri beredskap för virtuella Azure-datorer

Den här självstudien visar hur du konfigurerar haveri beredskap för virtuella Azure-datorer med hjälp av [Azure Site Recovery](site-recovery-overview.md). I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Verifiera Azure-inställningar och-behörigheter
> * Förbered virtuella datorer som du vill replikera
> * skapar ett Recovery Services-valv
> * Aktivera VM-replikering

När du aktiverar replikering för en virtuell dator för att konfigurera haveri beredskap installeras Site Recovery mobilitets tjänst tillägget på den virtuella datorn och registrerar det med Azure Site Recovery. Vid replikering skickas de virtuella dator diskarna till ett cache Storage-konto i käll regionen. Data skickas därifrån till mål regionen och återställnings punkterna genereras från data. När du växlar över en virtuell dator under haveri beredskap används en återställnings punkt för att återställa den virtuella datorn i mål regionen.

> [!NOTE]
> Självstudier ger instruktioner med de enklaste standardinställningarna. Läs [den här artikeln](azure-to-azure-how-to-enable-replication.md)om du vill konfigurera haveri beredskap för virtuella Azure-datorer med anpassade inställningar.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här självstudien:

- [Granska regioner som stöds](azure-to-azure-support-matrix.md#region-support). Du kan konfigurera katastrof återställning för virtuella Azure-datorer mellan två regioner i samma geografi.
- Du behöver en eller flera virtuella Azure-datorer. Kontrol lera att virtuella [Windows](azure-to-azure-support-matrix.md#windows) -eller [Linux](azure-to-azure-support-matrix.md#replicated-machines---linux-file-systemguest-storage) -datorer stöds.
- Granska [beräknings](azure-to-azure-support-matrix.md#replicated-machines---compute-settings)-, [lagrings](azure-to-azure-support-matrix.md#replicated-machines---storage)-och [nätverks](azure-to-azure-support-matrix.md#replicated-machines---networking) krav för virtuella datorer.
- Den här självstudien förutsätter att virtuella datorer inte är krypterade. [Följ den här artikeln](azure-to-azure-how-to-enable-replication-ade-vms.md)om du vill konfigurera en katastrof återställning för krypterade virtuella datorer.

## <a name="check-azure-settings"></a>Kontrol lera Azure-inställningarna

Kontrol lera behörigheter och inställningar i mål regionen.

### <a name="check-permissions"></a>Kontrol lera behörigheter

Ditt Azure-konto måste ha behörighet att skapa ett Recovery Services-valv och för att skapa virtuella datorer i mål regionen.

- Om du precis har skapat en kostnads fri Azure-prenumeration är du konto administratör och ingen ytterligare åtgärd krävs.
- Om du inte är administratör kan du arbeta med administratören för att få de behörigheter som du behöver.
    - **Skapa ett valv**: administratörs-eller ägar behörigheter för prenumerationen. 
    - **Hantera Site Recovery åtgärder i valvet**: den inbyggda Azure-rollen *Site Recovery Contributor* .
    - **Skapa virtuella Azure-datorer i mål regionen**: antingen den inbyggda rollen *virtuell dator deltagare* eller specifika behörigheter för att:
        - Skapa en virtuell dator i det valda virtuella nätverket.
        - Skriv till ett Azure Storage-konto.
        - Skriv till en Azure-hanterad disk.

### <a name="verify-target-settings"></a>Verifiera mål inställningarna

Vid haveri beredskap skapas virtuella datorer i mål regionen när du växlar över från käll regionen. 

Kontrol lera att din prenumeration har tillräckligt med resurser i mål regionen. Du måste kunna skapa virtuella datorer med storlekar som matchar de virtuella datorerna i käll regionen. När du konfigurerar haveri beredskap, Site Recovery väljer samma storlek (eller den närmast möjliga storleken) för den virtuella mål datorn.


## <a name="prepare-vms"></a>Förbereda virtuella datorer

Se till att de virtuella datorerna har utgående anslutning och de senaste rot certifikaten. 


### <a name="set-up-vm-connectivity"></a>Konfigurera VM-anslutning

Virtuella datorer som du vill replikera behöver utgående nätverks anslutning.

> [!NOTE]
> Site Recovery har inte stöd för en autentiseringsproxy för att styra nätverksanslutningar.

#### <a name="outbound-connectivity-for-urls"></a>Utgående anslutning för webbadresser

Om du använder en URL-baserad brand Väggs-proxy för att kontrol lera utgående anslutning ger du åtkomst till följande URL: er:

| **Namn**                  | **Kommersiellt**                               | **Myndigheter**                                 | **Beskrivning** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Storage                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | Gör att data kan skrivas från den virtuella datorn till cachelagringskontot i källregionen. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Tillhandahåller auktorisering och autentisering för Site Recovery-tjänstens webbadresser. |
| Replikering               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Låter den virtuella datorn kommunicera med Site Recovery-tjänsten. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Låter den virtuella datorn skriva övervaknings- och diagnostikdata för Site Recovery. |

#### <a name="outbound-connectivity-for-ip-address-ranges"></a>Utgående anslutning för IP-adressintervall

Om du använder nätverks säkerhets grupper (NSG: er) för att kontrol lera anslutningen skapar du service-tag-baserade NSG-regler som tillåter HTTPS utgående till port 443 för dessa [service märken](../virtual-network/service-tags-overview.md#available-service-tags)(grupper med IP-adresser):

**Tag** | **Tillåt** 
--- | ---
Lagrings tag gen  |Tillåter att data skrivs från den virtuella datorn till cache Storage-kontot.   
Azure AD-tagg | Tillåter åtkomst till alla IP-adresser som motsvarar Azure AD.   
EventsHub-tagg | Ger åtkomst till Site Recovery övervakning.  
AzureSiteRecovery-tagg | Tillåter åtkomst till tjänsten Site Recovery i vilken region som helst.   
GuestAndHybridManagement-tagg | Använd om du vill uppgradera den Site Recovery mobilitets agent som körs på virtuella datorer som är aktiverade för replikering.

[Lär dig mer](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) om obligatoriska taggar och taggnings exempel.

### <a name="verify-vm-certificates"></a>Verifiera certifikat för virtuella datorer

Kontrol lera att de virtuella datorerna har de senaste rot certifikaten. Annars kan den virtuella datorn inte registreras med Site Recovery på grund av säkerhets begränsningar.

- **Virtuella Windows-datorer**: installera alla de senaste Windows-uppdateringarna på den virtuella datorn så att alla betrodda rot certifikat finns på datorn. I en frånkopplad miljö följer du standard processerna för Windows Update och certifikat uppdateringar.
- **Virtuella Linux-datorer**: Följ de rikt linjer som tillhandahålls av Linux-distributören för att hämta de senaste betrodda rot certifikaten och listan över återkallade certifikat (CRL).

## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Skapa ett Recovery Services-valv i vilken region som helst, förutom i käll regionen som du vill replikera virtuella datorer från.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Skriv *återställning* i rutan Sök. Under **tjänster** väljer du **Recovery Services valv**.

    ![Sök efter Recovery Services valv](./media/azure-to-azure-tutorial-enable-replication/search.png)

3. I **Recovery Services valv** väljer du **Lägg till**.
4. I **skapa Recovery Services Vault**  >  -**grunderna** väljer du den prenumeration som du vill skapa valvet i.
5. I **resurs grupp** väljer du en befintlig resurs grupp för valvet eller skapar en ny.
6. I **valv namn** anger du ett eget namn som identifierar valvet.
7. I **region** väljer du den Azure-region där du vill placera valvet. [Kontrol lera regioner som stöds](https://azure.microsoft.com/pricing/details/site-recovery/).
8. Välj **Granska + skapa**.

   ![Valv inställningar på sidan för att skapa ett nytt valv](./media/azure-to-azure-tutorial-enable-replication/vault-basics.png)

9. I **Granska + skapa** väljer du **Skapa**.

10. Valv distributionen börjar. Följ förloppet i aviseringarna.
11. När valvet har distribuerats väljer du **Fäst på instrument panelen** för att spara den för snabb referens. Välj **gå till resurs** för att öppna det nya valvet. 
    
    ![Knappar för att öppna valvet efter distribution och fästa på instrument panelen](./media/azure-to-azure-tutorial-enable-replication/vault-deploy.png)

### <a name="enable-site-recovery"></a>Aktivera Site Recovery

I valv inställningarna väljer du **aktivera Site Recovery**.

![Val för att aktivera Site Recovery i valvet](./media/azure-to-azure-tutorial-enable-replication/enable-site-recovery.png)

## <a name="enable-replication"></a>Aktivera replikering

Välj käll inställningar och aktivera VM-replikering. 

### <a name="select-source-settings"></a>Välj käll inställningar

1. På sidan valv > **Site Recovery** , under **Azure Virtual Machines**, väljer du **Aktivera replikering**.

    ![Val för att aktivera replikering för virtuella Azure-datorer](./media/azure-to-azure-tutorial-enable-replication/enable-replication.png)

2. På **käll** >  **käll plats** väljer du den Azure-region där virtuella datorer körs för närvarande.
3. I **distributions modell för virtuell Azure-dator** lämnar du standard inställningen för **Resource Manager** .
4. I **käll prenumeration** väljer du den prenumeration som de virtuella datorerna körs i. Du kan välja vilken prenumeration som helst i samma Azure Active Directory-klient (AD) som valvet.
5. I **resurs grupp för källa** väljer du den resurs grupp som innehåller de virtuella datorerna.
6. I **haveri beredskap mellan tillgänglighets zoner** lämnar du standardvärdet **no** .

     ![Konfigurera källan](./media/azure-to-azure-tutorial-enable-replication/source.png)

7. Välj **Nästa**.

### <a name="select-the-vms"></a>Välj virtuella datorer

Site Recovery hämtar de virtuella datorer som är associerade med den valda prenumerationen/resurs gruppen.

1. I **Virtual Machines** väljer du de virtuella datorer som du vill aktivera för haveri beredskap.

     ![Sidan för att välja virtuella datorer för replikering](./media/azure-to-azure-tutorial-enable-replication/select-vm.png)

2. Välj **Nästa**.

### <a name="review-replication-settings"></a>Granska replikeringsinställningar

1. Granska inställningarna i **replikeringsinställningar**. Site Recovery skapar standardinställningar/princip för mål regionen. I den här självstudien använder vi standardinställningarna.
2. Välj **Aktivera replikering**.

    ![Sida för att anpassa inställningar och aktivera replikering](./media/azure-to-azure-tutorial-enable-replication/enable-vm-replication.png)   

3. Spåra replikeringens förlopp i aviseringarna.

     ![Spåra förloppet i meddelanden ](./media/azure-to-azure-tutorial-enable-replication/notification.png) ![ Spåra meddelande om slutförd replikering](./media/azure-to-azure-tutorial-enable-replication/notification-success.png)

4. De virtuella datorer som du aktiverar visas på sidan valv > **replikerade objekt** .

    ![Den virtuella datorn på sidan replikerade objekt](./media/azure-to-azure-tutorial-enable-replication/replicated-items.png)


## <a name="next-steps"></a>Nästa steg

I den här självstudien har du aktiverat haveri beredskap för en virtuell Azure-dator. Nu ska du köra en detalj granskning för att kontrol lera att redundansväxlingen fungerar som förväntat.

> [!div class="nextstepaction"]
> [Köra ett programåterställningstest](azure-to-azure-tutorial-dr-drill.md)
