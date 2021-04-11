---
title: Konfigurera katastrof återställning av fysiska lokala servrar med Azure Site Recovery
description: Lär dig hur du konfigurerar haveri beredskap till Azure för lokala Windows-och Linux-servrar med tjänsten Azure Site Recovery.
ms.service: site-recovery
ms.topic: article
ms.date: 11/12/2019
ms.openlocfilehash: 5775f8ed9b657502f4865f3c4f311ea0dcd269ea
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581542"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-physical-servers"></a>Konfigurera katastrof återställning till Azure för lokala fysiska servrar

[Azure Site Recovery](site-recovery-overview.md)-tjänsten bidrar till din strategi för haveriberedskap genom att hantera och samordna replikering, redundans och återställning av fysiska servrar och virtuella Azure-datorer.

Den här självstudien visar hur du konfigurerar haveri beredskap för lokala fysiska Windows-och Linux-servrar till Azure. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera Azure och lokala krav
> * Skapa ett Recovery Services-valv för Site Recovery 
> * Konfigurera käll-och mål miljöer för replikering
> * Skapa replikeringsprincip
> * Aktivera replikering för en server

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

- Se till att du förstår [arkitekturen och komponenterna](physical-azure-architecture.md) i det här scenariot.
- Granska [support kraven](vmware-physical-secondary-support-matrix.md) för alla komponenter.
- Kontrol lera att de servrar som du vill replikera följer [kraven för virtuella Azure-datorer](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
- Förbered Azure. Du behöver en Azure-prenumeration, ett virtuellt Azure-nätverk och ett lagrings konto.
- Förbereda ett konto för automatisk installation av mobilitets tjänsten på varje server som du vill replikera.

Tänk på följande innan du börjar:

- Efter redundansväxlingen till Azure går det inte att återställa fysiska servrar tillbaka till lokala fysiska datorer. Du kan bara växla tillbaka till virtuella VMware-datorer. 
- Den här självstudien konfigurerar haveri beredskap för fysiska servrar till Azure med de enklaste inställningarna. Om du vill lära dig mer om andra alternativ läser du igenom guiden så här gör du:
    - Konfigurera [replikerings källa](physical-azure-set-up-source.md), inklusive Site Recovery konfigurations servern.
    - Konfigurera [replikeringsmålet](physical-azure-set-up-target.md).
    - Konfigurera en [replikeringsprincip](vmware-azure-set-up-replication.md) och [aktivera replikering](vmware-azure-enable-replication.md).


### <a name="set-up-an-azure-account"></a>Konfigurera ett Azure-konto

Skaffa ett Microsoft [Azure-konto](https://azure.microsoft.com/).

- Du kan börja med en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).
- Läs mer om [Site Recovery priser](site-recovery-faq.md#pricing)och få [pris information](https://azure.microsoft.com/pricing/details/site-recovery/).
- Ta reda på vilka [regioner som stöds](https://azure.microsoft.com/pricing/details/site-recovery/) för Site Recovery.

### <a name="verify-azure-account-permissions"></a>Verifiera behörigheter för Azure-kontot

Se till att ditt Azure-konto har behörighet att replikera virtuella datorer till Azure.

- Granska de [behörigheter](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) som du behöver för att replikera datorer till Azure.
- Verifiera och ändra [Azure-rollbaserad åtkomst kontroll (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) -behörigheter. 



### <a name="set-up-an-azure-network"></a>Skapa ett Azure-nätverk

Konfigurera ett [Azure-nätverk](../virtual-network/quick-create-portal.md).

- Virtuella Azure-datorer placeras i det här nätverket när de skapas efter en redundansväxling.
- Nätverket ska finnas i samma region som Recovery Services-valvet


## <a name="set-up-an-azure-storage-account"></a>Skapa ett Azure-lagringskonto

Konfigurera ett [Azure Storage-konto](../storage/common/storage-account-create.md).

- Site Recovery replikerar lokala datorer till Azure Storage. Virtuella Azure-datorer skapas från lagrings platsen när redundansväxlingen sker.
- Lagringskontot måste finnas i samma region som Recovery Services-valvet.


### <a name="prepare-an-account-for-mobility-service-installation"></a>Förbereda ett konto för installation av mobilitetstjänsten

Mobilitets tjänsten måste installeras på varje server som du vill replikera. Site Recovery installerar den här tjänsten automatiskt när du aktiverar replikering för servern. Om du vill installera automatiskt måste du förbereda ett konto som Site Recovery ska använda för att få åtkomst till servern.

- Du kan använda en domän eller ett lokalt konto
- För virtuella Windows-datorer, om du inte använder ett domän konto, inaktiverar du åtkomst kontroll för fjärran vändare på den lokala datorn. Det gör du genom att lägga till DWORD-posten **LocalAccountTokenFilterPolicy** i registret under **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** med värdet 1.
- Om du vill lägga till register posten för att inaktivera inställningen från en CLI, skriver du:       ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- För Linux bör kontot vara rot på käll-Linux-servern.


## <a name="create-a-vault"></a>Skapa ett valv

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Välj ett skyddsmål

Välj vad du vill replikera och replikera den till.

1. Klicka på **Recovery Services-valv** > valv.
2. På resurs-menyn klickar du på **Site Recovery**  >  **förbereda infrastruktur**  >  **skydds mål**.
3. I **skydds mål** väljer **du till Azure**  >  **inte virtualiserad/övrigt**.

## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

Konfigurera konfigurations servern, registrera den i valvet och identifiera virtuella datorer.

1. Klicka på **Site Recovery**  >  **Förbered infrastruktur**  >  **källa**.
2. Om du inte har en konfigurations Server klickar du på **+ konfigurations Server**.
3. I **Lägg till Server** kontrollerar du att **konfigurations servern** visas i **Server typ**.
4. Hämta installations filen Site Recovery Unified setup.
5. Ladda ned valvregistreringsnyckeln. Du behöver det när du kör enhetlig installation. Nyckeln är giltig i fem dagar efter att du har genererat den.

   ![Skärm bild som visar alternativen för att ladda ned installations filen och registrerings nyckeln.](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>Registrera konfigurations servern i valvet

Gör följande innan du börjar: 

#### <a name="verify-time-accuracy"></a>Kontrol lera tids exakthet
Kontrol lera att system klockan är synkroniserad med en [tids Server](/windows-server/networking/windows-time-service/windows-time-service-top)på datorns konfigurations Server. Den ska matcha. Om det är 15 minuter framför eller bakom, kan det hända att installationen Miss känner.

#### <a name="verify-connectivity"></a>Verifiera anslutningen
Kontrol lera att datorn har åtkomst till dessa URL: er baserat på din miljö: 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

IP-adressbaserade brand Väggs regler ska tillåta kommunikation till alla Azure-URL: er som anges ovan över HTTPS-port (443). För att förenkla och begränsa IP-intervallen rekommenderar vi att URL-filtrering görs.

- **Kommersiella** IP-adresser – tillåta [IP-intervall för Azure-datacenter](https://www.microsoft.com/download/confirmation.aspx?id=41653)och HTTPS-porten (443). Tillåt IP-adressintervall för Azure-regionen för din prenumeration för att stödja URL: erna AAD, backup, Replication och Storage.  
- **Myndigheter för myndigheter** – Tillåt URL: er för [Azure Government-DATAcenter](https://www.microsoft.com/en-us/download/details.aspx?id=57063)och HTTPS-porten (443) för alla USGov-regioner (Virginia, Texas, Arizona och Iowa) för att stödja URL: er för AAD, säkerhets kopiering, replikering och lagring.  

#### <a name="run-setup"></a>Kör installationsprogrammet
Kör enhetlig installation som lokal administratör för att installera konfigurations servern. Processervern och huvud mål servern installeras också som standard på konfigurations servern.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]


## <a name="set-up-the-target-environment"></a>Konfigurera målmiljön

Välj och kontrollera målresurserna.

1. Klicka på **Förbered infrastruktur**  >  **mål** och välj den Azure-prenumeration som du vill använda.
2. Ange mål distributions modell.
3. Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk.

   ![Skärm bild av alternativen för att konfigurera mål miljön.](./media/physical-azure-disaster-recovery/network-storage.png)


## <a name="create-a-replication-policy"></a>Skapa replikeringsprincip

1. Om du vill skapa en ny replikeringsprincip klickar du på Site Recovery principer för **infrastruktur**  >  **replikering**  >  **+ replikeringsprincip**.
2. I **Skapa replikeringsprincip** anger du ett principnamn.
3. I **Tröskelvärde för replikeringspunktmål** anger du gränsen för replikeringspunktmålet (RPO). Det här värdet anger hur ofta data återställnings punkter skapas. En avisering genereras när den kontinuerliga replikeringen överskrider den här gränsen.
4. I **Återställningspunkt för kvarhållning** anger du kvarhållningsperioden (i antal timmar) för varje återställningspunkt. Replikerade virtuella datorer kan återställas till valfri punkt i ett fönster. Upp till 24 timmars kvarhållning stöds för datorer replikerade till premiumlagring och 72 timmar för standardlagring.
5. I **frekvens för programkonsekventa ögonblicks bilder** anger du hur ofta återställnings punkter (i minuter) som innehåller programkonsekventa ögonblicks bilder ska skapas. Klicka på **OK** för att skapa principen.

    ![Skärm bild av alternativen för att skapa en replikeringsprincip.](./media/physical-azure-disaster-recovery/replication-policy.png)


Principen associeras automatiskt med konfigurationsservern. Som standard skapas automatiskt en matchande princip för återställning efter fel. Om replikeringsprincipen till exempel är **rep-princip** skapas **en princip för återställning efter fel** . Den här principen används inte förrän du initierar en återställning efter fel från Azure.

## <a name="enable-replication"></a>Aktivera replikering

Aktivera replikering för varje server.

- Site Recovery installerar mobilitets tjänsten när replikering är aktive rad.
- När du aktiverar replikering för en server kan det ta 15 minuter eller längre innan ändringarna börjar gälla och visas i portalen.

1. Klicka på **Replikera program**  >  **källa**.
2. I **Källa** väljer du konfigurationsservern.
3. I **typ av dator** väljer du **fysiska datorer**.
4. Välj processervern (konfigurations servern). Klicka sedan på **OK**.
5. I **mål** väljer du den prenumeration och resurs grupp där du vill skapa virtuella Azure-datorer efter redundansväxlingen. Välj den distributions modell som du vill använda i Azure (klassisk eller resurs hantering).
6. Välj Azure-lagringskonto som du vill använda när du replikerar data. 
7. Välj det Azure-nätverk och undernät som virtuella Azure-datorer ska ansluta till efter en redundansväxling.
8. Välj **Konfigurera nu för valda datorer** om du vill använda nätverks inställningen på alla datorer som du väljer att skydda. Välj **Konfigurera senare** om du vill välja Azure-nätverket för varje dator. 
9. I **fysiska datorer** och klicka på **+ fysisk dator**. Ange namn och IP-adress. Välj operativ system för den dator som du vill replikera. Det tar några minuter för servrarna att identifieras och visas. 
10. I **Egenskaper**  >  **Konfigurera egenskaper** väljer du det konto som ska användas av processervern för att automatiskt installera mobilitets tjänsten på datorn.
11. I **replikeringsinställningar**  >  **Konfigurera replikeringsinställningar** kontrollerar du att rätt replikeringsprincip är markerad. 
12. Klicka på **Aktivera replikering**. Du kan följa förloppet för jobbet **Aktivera skydd** i **Inställningar**  >  **jobb**  >  **Site Recovery jobb**. När jobbet **Slutför skydd** har körts är datorn redo för redundans.


Om du vill övervaka servrar som du lägger till kan du kontrol lera den senaste upptäckta tiden för dem i **konfigurations servrarna**  >  **senaste kontakt på**. Om du vill lägga till datorer utan att vänta på en schemalagd identifierings tid markerar du konfigurations servern (Klicka inte på den) och klickar på **Uppdatera**.

## <a name="next-steps"></a>Nästa steg

[Kör en granskning av haveri beredskap](tutorial-dr-drill-azure.md).
