---
title: Replikera Azure Stack virtuella datorer till Azure med Azure Site Recovery | Microsoft Docs
description: Lär dig hur du konfigurerar haveri beredskap till Azure för Azure Stack virtuella datorer med tjänsten Azure Site Recovery.
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 300b239de74c7d21e03eb0c3b059663b5074964c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728393"
---
# <a name="replicate-azure-stack-vms-to-azure"></a>Replikera virtuella Azure Stack-datorer till Azure

Den här artikeln visar hur du konfigurerar haveri beredskap Azure Stack virtuella datorer till Azure med hjälp av [tjänsten Azure Site Recovery](site-recovery-overview.md).

Site Recovery bidrar till din strategi för affärs kontinuitet och haveri beredskap (BCDR). Tjänsten säkerställer att dina VM-arbetsbelastningar är tillgängliga när förväntade och oväntade avbrott inträffar.

- Site Recovery dirigerar och hanterar replikering av virtuella datorer till Azure Storage.
- När ett avbrott uppstår på den primära platsen använder du Site Recovery för att redundansväxla till Azure.
- Vid redundans skapas virtuella Azure-datorer från lagrade VM-data och användare kan fortsätta att komma åt arbets belastningar som körs på de virtuella Azure-datorerna.
- När allt är igång igen kan du återställa virtuella Azure-datorer till den primära platsen och börja replikera till Azure Storage igen.


I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * **Steg 1: Förbered virtuella Azure Stack-datorer för replikering**. Kontrol lera att de virtuella datorerna uppfyller Site Recovery krav och Förbered för installation av tjänsten Site Recovery mobilitet. Den här tjänsten är installerad på varje virtuell dator som du vill replikera.
> * **Steg 2: Konfigurera ett Recovery Services-valv**. Konfigurera ett valv för Site Recovery och ange vad du vill replikera. Site Recovery-komponenter och-åtgärder konfigureras och hanteras i valvet.
> * **Steg 3: Konfigurera käll miljön för replikering**. Konfigurera en Site Recovery konfigurations Server. Konfigurations servern är en enskild Azure Stack virtuell dator som kör alla de komponenter som krävs av Site Recovery. När du har konfigurerat konfigurations servern kan du registrera den i valvet.
> * **Steg 4: Konfigurera mål miljön för replikering**. Välj ditt Azure-konto och det Azure Storage-konto och nätverk som du vill använda. Under replikeringen kopieras VM-data till Azure Storage. Efter redundansväxlingen är virtuella Azure-datorer anslutna till det angivna nätverket.
> * **Steg 5: Aktivera replikering**. Konfigurera replikeringsinställningar och aktivera replikering för virtuella datorer. Mobilitets tjänsten installeras på en virtuell dator när replikering är aktive rad. Site Recovery utför en inledande replikering av den virtuella datorn och sedan börjar den pågående replikeringen.
> * **Steg 6: kör en granskning av haveri beredskap**: när replikeringen är igång kontrollerar du att redundansväxlingen fungerar som förväntat genom att köra en detalj granskning. Om du vill initiera detalj nivån kör du ett redundanstest i Site Recovery. Redundanstest påverkar inte din produktions miljö.

Med de här stegen slutförda kan du köra en fullständig redundans till Azure som och när du behöver.

## <a name="architecture"></a>Arkitektur

![Diagrammet visar Recovery Services valv för två klienter i moln som är kopplade till klient prenumerationer både på en gemensam Azure Stack infrastruktur.](./media/azure-stack-site-recovery/architecture.png)

**Plats** | **Komponent** |**Information**
--- | --- | ---
**Konfigurationsserver** | Körs på en enskild Azure Stack virtuell dator. | I varje prenumeration ställer du in en virtuell konfigurations Server. Den här virtuella datorn kör följande Site Recoverys komponenter:<br/><br/> – Konfigurations Server: samordnar kommunikationen mellan både lokalt och Azure och hanterar datareplikering. -Processerver: fungerar som en gateway för replikering. Den tar emot replikeringsdata, optimerar med cachelagring, komprimering och kryptering; och skickar den till Azure Storage.<br/><br/> Om de virtuella datorer som du vill replikera överskrider de gränser som anges nedan, kan du konfigurera en separat fristående processerver. [Läs mer](vmware-azure-set-up-process-server-scale.md).
**Mobilitetstjänsten** | Installeras på varje virtuell dator som du vill replikera. | I stegen i den här artikeln förbereder vi ett konto så att mobilitets tjänsten installeras automatiskt på en virtuell dator när replikering är aktiverat. Om du inte vill installera tjänsten automatiskt finns det ett antal andra metoder som du kan använda. [Läs mer](vmware-azure-install-mobility-service.md).
**Azure** | I Azure behöver du ett Recovery Services-valv, ett lagrings konto och ett virtuellt nätverk. |  Replikerade data lagras i lagrings kontot. Virtuella Azure-datorer läggs till i Azure-nätverket när redundansväxlingen sker.


Replikeringen fungerar på följande sätt:

1. I valvet anger du replikeringens källa och mål, konfigurerar konfigurations servern, skapar en replikeringsprincip och aktiverar replikering.
2. Mobilitets tjänsten är installerad på datorn (om du har använt push-installation) och datorerna påbörjar replikeringen i enlighet med replikeringsprincipen.
3. En inledande kopia av Server data replikeras till Azure Storage.
4. När den inledande replikeringen har slutförts börjar replikeringen av delta ändringar till Azure. Spårade ändringar för en dator lagras i en .hrl-fil.
5. Konfigurations servern dirigerar hantering av replikering med Azure (port HTTPS 443 utgående).
6. Processervern tar emot data från käll datorer, optimerar och krypterar den och skickar den till Azure Storage (port 443 utgående).
7. Replikerade datorer kommunicerar med konfigurations servern (port HTTPS 443 inkommande, för hantering av replikering. Datorer skickar replikeringsdata till processervern (port HTTPS 9443 inkommande – kan ändras).
8. Trafik replikeras till offentliga Azure Storage-slutpunkter, över Internet. Du kan även använda Azure ExpressRoute offentlig peering. Replikering av trafik via en plats-till-plats-VPN från en lokal plats till Azure stöds inte.

## <a name="prerequisites"></a>Förutsättningar

Det här är vad du behöver för att konfigurera det här scenariot.

**Krav** | **Information**
--- | ---
**Azure-prenumerations konto** | Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).
**Behörigheter för Azure-konto** | Det Azure-konto du använder måste ha behörighet att:<br/><br/> -Skapa ett Recovery Service-valv<br/><br/> -Skapa en virtuell dator i resurs gruppen och det virtuella nätverk som du använder för scenariot<br/><br/> – Skriv till det lagrings konto som du anger<br/><br/> Tänk på följande:<br/><br/> – Om du skapar ett konto är du administratör för din prenumeration och kan utföra alla åtgärder.<br/><br/> – Om du använder en befintlig prenumeration och inte är administratör måste du arbeta med administratören för att tilldela behörigheter för ägare eller deltagare.<br/><br/> – Läs [den här artikeln](site-recovery-role-based-linked-access-control.md)om du behöver mer detaljerade behörigheter.
**Azure Stack VM** | Du behöver en Azure Stack VM i klient prenumerationen som ska distribueras som Site Recovery konfigurations servern.


### <a name="prerequisites-for-the-configuration-server"></a>Krav för konfigurations servern

[!INCLUDE [site-recovery-config-server-reqs-physical](../../includes/site-recovery-config-server-reqs-physical.md)]



## <a name="step-1-prepare-azure-stack-vms"></a>Steg 1: Förbered Azure Stack virtuella datorer

### <a name="verify-the-operating-system"></a>Verifiera operativ systemet

Se till att de virtuella datorerna kör ett av de operativ system som sammanfattas i tabellen.


**Operativsystem** | **Information**
--- | ---
**64-bitars Windows** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 (från SP1)
**CentOS** | 5,2 till 5,11, 6,1 till 6,9, 7,0 till 7,3
**Ubuntu** | 14,04 LTS-Server, 16,04 LTS-Server. Granska [stödda kernels](vmware-physical-azure-support-matrix.md#ubuntu-kernel-versions)

### <a name="prepare-for-mobility-service-installation"></a>Förbereda för installation av mobilitets tjänsten

Du måste ha mobilitets tjänsten installerad för varje virtuell dator som du vill replikera. För att processervern ska installera tjänsten automatiskt på den virtuella datorn när replikering är aktive rad, verifiera inställningarna för virtuella datorer.

#### <a name="windows-machines"></a>Windows-datorer

- Du behöver en nätverks anslutning mellan den virtuella dator som du vill aktivera replikering på och datorn som kör processervern (som standard är den virtuella datorns konfigurations Server).
- Du behöver ett konto med administratörs behörighet (domän eller lokal) på den dator som du aktiverar replikering för.
    - Du anger det här kontot när du konfigurerar Site Recovery. Sedan använder processervern det här kontot för att installera mobilitets tjänsten när replikering är aktive rad.
    - Det här kontot används endast av Site Recovery för push-installationen och för att uppdatera mobilitets tjänsten.
    - Om du inte använder ett domän konto måste du inaktivera åtkomst kontroll för fjärranslutna användare på den virtuella datorn:
        - I registret skapar du DWORD-värdet **LocalAccountTokenFilterPolicy** under HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System.
        - Ange värdet 1.
        - Gör detta genom att skriva följande i kommando tolken: **REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System/V LocalAccountTokenFilterPolicy/t REG_DWORD/d 1**.
- I Windows-brandväggen på den virtuella datorn som du vill replikera, Tillåt fil-och skrivar delning och WMI.
    - Det gör du genom att köra **WF. msc** för att öppna konsolen Windows-brandväggen. Högerklicka på **regler för inkommande**  >  **trafik ny regel**. Välj **fördefinierad** och välj **fil-och skrivar delning** i listan. Slutför guiden och välj att tillåta anslutningen > **slutföras**.
    - För domän datorer kan du använda ett grup princip objekt för att göra detta.


#### <a name="linux-machines"></a>Linux-datorer

- Se till att det finns en nätverks anslutning mellan Linux-datorn och processervern.
- På den dator där du aktiverar replikering behöver du ett konto som är en rot användare på käll-Linux-servern:
    - Du anger det här kontot när du konfigurerar Site Recovery. Sedan använder processervern det här kontot för att installera mobilitets tjänsten när replikering är aktive rad.
    - Det här kontot används endast av Site Recovery för push-installationen och för att uppdatera mobilitets tjänsten.
- Kontrollera att /etc/hosts-filen på Linux-källservern innehåller poster som mappar det lokala värdnamnet till IP-adresser som är associerade med alla nätverkskort.
- Installera de senaste openssh-, openssh-server- och openssl-paketen på den dator som du vill replikera.
- Kontrollera att Secure Shell (SSH) är aktiverat och körs på port 22.
- Aktivera SFTP-undersystemet och lösenordsautentisering i sshd_config-filen:
    1. Det gör du genom att logga in som rot.
    2. Hitta raden som börjar med **PasswordAuthentication**, i filen/etc/ssh/sshd_config. Ta bort kommentarerna på raden och ändra värdet till **yes**.
    3. Hitta raden som börjar med **Subsystem** och ta bort den.

        ![Tjänsten Linux Mobility](./media/azure-stack-site-recovery/linux-mobility.png)

    4. Starta om sshd-tjänsten.


### <a name="note-the-vm-private-ip-address"></a>Anteckna den virtuella datorns privata IP-adress

Hitta IP-adressen för varje dator som du vill replikera:

1. Klicka på den virtuella datorn i Azure Stack-portalen.
2. På **resurs** -menyn klickar du på **nätverks gränssnitt**.
3. Anteckna den privata IP-adressen.

    ![Privat IP-adress](./media/azure-stack-site-recovery/private-ip.png)


## <a name="step-2-create-a-vault-and-select-a-replication-goal"></a>Steg 2: skapa ett valv och välj ett mål för replikering

1. I Azure Portal väljer du **skapa ett resurs**  >  **hanterings verktyg**  >  **säkerhets kopiering och Site Recovery**.
2. I **Namn** anger du ett eget namn som identifierar valvet.
3. I **resurs grupp** skapar eller väljer du en resurs grupp. Vi använder **conto sorg**.
4. Ange Azure-regionen i **plats**. Använder vi **Europa, västra**.
5. Om du snabbt vill komma åt valvet från instrument panelen väljer du **Fäst på instrument panelen**  >  **skapa**.

   ![Skapa ett nytt valv](./media/azure-stack-site-recovery/new-vault-settings.png)

   Det nya valvet visas på **instrument panelen**  >  **alla resurser** och på huvud sidan **Recovery Services valv** .

### <a name="select-a-replication-goal"></a>Väljer ett replikeringsmål

1. I **Recovery Services valv** > ange ett valv namn. Vi använder **ContosoVMVault**.
2. I **Komma igång** väljer du Site Recovery. Välj sedan **Förbered infrastrukturen**.
3. I **skydds mål**  >  **där** finns datorerna väljer du **lokalt**.
4. I **Till vilken plats ska dina datorer replikeras?** väljer du **Till Azure**.
5. I **är dina datorer virtualiserade** väljer du **inte virtualiserad/övrigt**. Välj sedan **OK**.

    ![Skyddsmål](./media/azure-stack-site-recovery/protection-goal.png)

## <a name="step-3-set-up-the-source-environment"></a>Steg 3: Konfigurera käll miljön

Konfigurera Configuration Server-datorn, registrera den i valvet och identifiera datorer som du vill replikera.

1. Klicka på **Förbered infrastruktur**  >  **källa**.
2. I **Förbered källa** klickar du på **+Konfigurationsserver**.

    ![Skärm bild av dialog rutan + konfigurations server med meddelandet "Klicka på + konfigurations server i kommando fältet ovan för att konfigurera en...".](./media/azure-stack-site-recovery/plus-config-srv.png)

3. I **Lägg till Server** kontrollerar du att **konfigurations servern** visas i **Server typ**.
5. Hämta installations filen Site Recovery Unified setup.
6. Ladda ned valvregistreringsnyckeln. Du behöver registrerings nyckeln när du kör enhetlig installation. Nyckeln är giltig i fem dagar efter att du har genererat den.

    ![Skärm bild av dialog rutan Lägg till server med Server typ inställd på konfigurations servern och nedladdnings knappen för valv registrerings nyckeln markerat.](./media/azure-stack-site-recovery/set-source2.png)


### <a name="run-azure-site-recovery-unified-setup"></a>Kör Azure Site Recovery enhetlig installation

Installera och registrera konfigurations servern genom att göra en RDP-anslutning till den virtuella dator som du vill använda för konfigurations servern och köra enhetlig installation.

Innan du börjar ska du kontrol lera att klockan är [synkroniserad med en tids Server](/windows-server/networking/windows-time-service/windows-time-service-top) på den virtuella datorn innan du börjar. Installationen Miss lyckas om tiden är mer än fem minuter av lokal tid.

Installera konfigurations servern nu:

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Konfigurations servern kan också installeras från kommando raden. [Läs mer](physical-manage-configuration-server.md#install-from-the-command-line).
>
> Det kan ta 15 minuter eller mer innan kontonamnet visas i portalen. Om du vill uppdatera omedelbart väljer du **konfigurations servrar**  >  **_Server namn_*_ > _* uppdatera Server**.

## <a name="step-4-set-up-the-target-environment"></a>Steg 4: Konfigurera mål miljön

Välj och kontrollera målresurserna.

1. I **Förbered infrastruktur**  >  **mål** väljer du den Azure-prenumeration som du vill använda.
2. Ange mål distributions modell.
3. Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk. Om du inte hittar dem måste du skapa minst ett lagrings konto och ett virtuellt nätverk för att kunna slutföra guiden.


## <a name="step-5-enable-replication"></a>Steg 5: Aktivera replikering

### <a name="create-a-replication-policy"></a>Skapa replikeringsprincip

1. Klicka på **Förbered**  >  **replikeringsinställningar** för infrastruktur.
2. I **Skapa replikeringsprincip** anger du ett principnamn.
3. I **Tröskelvärde för replikeringspunktmål** anger du gränsen för replikeringspunktmålet (RPO).
    - Återställnings punkter för replikerade data skapas i enlighet med tids uppsättningen.
    - Den här inställningen påverkar inte replikering, som är kontinuerlig. Den utfärdar bara en avisering om tröskel gränsen uppnås utan att en återställnings punkt skapas.
4. I **återställnings punktens kvarhållning** anger du hur länge varje återställnings punkt ska behållas. Replikerade virtuella datorer kan återställas till valfri punkt i den angivna tids perioden.
5. I **frekvens för programkonsekventa ögonblicks bilder** anger du hur ofta programkonsekventa ögonblicks bilder skapas.

    - En programkonsekvent ögonblicks bild är en tidpunkts ögonblicks bild av AppData i den virtuella datorn.
    - Tjänsten Volume Shadow Copy (VSS) säkerställer att appar på den virtuella datorn är i ett konsekvent tillstånd när ögonblicks bilden tas.
6. Välj **OK** för att skapa principen.


### <a name="confirm-deployment-planning"></a>Bekräfta distributionsplanering

Du kan hoppa över det här steget just nu. Klicka på Ja i list rutan **distributions planering** **, jag har gjort det**.



### <a name="enable-replication"></a>Aktivera replikering

Se till att du har slutfört alla aktiviteter i [steg 1: Förbered dator](#step-1-prepare-azure-stack-vms). Aktivera sedan replikering på följande sätt:

1. Välj **Replikera program**  >  **källa**.
2. I **Källa** väljer du konfigurationsservern.
3. I **typ av dator** väljer du **fysiska datorer**.
4. Välj processerver (konfigurationsserver). Klicka sedan på **OK**.
5. I **mål** väljer du den prenumeration och resurs grupp som du vill skapa de virtuella datorerna i efter redundansväxlingen. Välj den distributions modell som du vill använda för de virtuella datorerna som misslyckades.
6. Välj det Azure Storage-konto där du vill lagra replikerade data.
7. Välj det Azure-nätverk och undernät som virtuella Azure-datorer ska ansluta till efter en redundansväxling.
8. Välj **Konfigurera nu för valda datorer** om du vill använda nätverksinställningen på alla datorer som du väljer att skydda. Välj **Konfigurera senare** om du vill välja Azure-nätverket separat för varje dator.
9. I **fysiska datorer** klickar du på **+ fysisk dator**. Ange namn, IP-adress och OS-typ för varje dator som du vill replikera.

    - Använd den interna IP-adressen för datorn.
    - Om du anger den offentliga IP-adressen kanske replikeringen inte fungerar som förväntat.

10. I **Egenskaper**  >  **Konfigurera egenskaper** väljer du det konto som processervern ska använda för att automatiskt installera mobilitets tjänsten på datorn.
11. I **replikeringsinställningar**  >  **konfigurerar du replikeringsinställningar**, kontrollerar att rätt replikeringsprincip är markerad.
12. Klicka på **Aktivera replikering**.
13. Spåra förloppet för jobbet **Aktivera skydd** i **Inställningar**  >  **jobb**  >  **Site Recovery jobb**. När jobbet **Slutför skydd** har körts är datorn redo för redundans.

> [!NOTE]
> Site Recovery installerar mobilitetstjänsten när replikering är aktiverad för en virtuell dator.
>
> Det kan ta 15 minuter eller längre innan ändringarna träder i kraft och visas på portalen.
>
> Om du vill övervaka de virtuella datorer som du lägger till kontrollerar du den senaste identifierade tiden för virtuella datorer i **konfigurations servrarna**  >  **senaste kontakt på**. Om du vill lägga till virtuella datorer utan att vänta på den schemalagda identifieringen markerar du konfigurationsservern (välj den inte) och väljer **Uppdatera**.


## <a name="step-6-run-a-disaster-recovery-drill"></a>Steg 6: köra en granskning av haveri beredskap

Du kör ett redundanstest till Azure för att se till att allt fungerar som förväntat. Den här redundansväxlingen påverkar inte din produktions miljö.

### <a name="verify-machine-properties"></a>Verifiera dator egenskaper

Innan du kör ett redundanstest kontrollerar du datorns egenskaper och kontrollerar att de uppfyller [Azure-kraven](vmware-physical-azure-support-matrix.md#azure-vm-requirements). Du kan visa och ändra egenskaper på följande sätt:

1. I **Skyddade objekt** klickar du på **Replikerade objekt** > VM.
2. I fönstret **Replikerade objekt** finns det en sammanfattning av VM-informationen, hälsostatus och de senaste tillgängliga återställningspunkterna. Klicka på **Egenskaper** för att se mer information.
3. I **beräkning och nätverk** ändrar du inställningarna efter behov.

    - Du kan ändra namn på Azure VM, resurs grupp, mål storlek, [tillgänglighets uppsättning](../virtual-machines/windows/tutorial-availability-sets.md)och hanterad disk.
    - Du kan också visa och ändra nätverks inställningar. Detta omfattar det nätverk/undernät som den virtuella Azure-datorn är ansluten till efter redundansväxlingen och den IP-adress som ska tilldelas den virtuella datorn.
1. I **diskar** kan du Visa information om operativ systemet och data diskarna på den virtuella datorn.


### <a name="run-a-test-failover"></a>Köra ett redundanstest

När du kör ett redundanstest händer följande:

1. En kravkontroll körs för att säkerställa att alla de villkor som krävs för redundans är på plats.
2. Redundansväxlingen bearbetar data med den angivna återställnings punkten:
    - **Senast bearbetad**: datorn växlar över till den senaste återställnings punkten som bearbetats av Site Recovery. Tidsstämpeln visas. Med det här alternativet läggs ingen tid på bearbetning av data, så den ger ett lågt RTO (mål för återställningstid).
    - **Senaste program – konsekvent**: datorn växlar över till den senaste programkonsekventa återställnings punkten.
    - **Anpassad**: Välj den återställnings punkt som ska användas för redundans.

3. En virtuell Azure-dator skapas med hjälp av bearbetade data.
4. Redundanstest kan automatiskt rensa virtuella Azure-datorer som skapades under detalj nivån.

Kör ett redundanstest för en virtuell dator på följande sätt:

1. I **Inställningar**  >  **replikerade objekt** klickar du på den virtuella datorn > **+ testa redundansväxlingen**.
2. I den här genom gången ska vi välja att använda den **senaste bearbetade** återställnings punkten.
3. I **redundanstestning** väljer du Azure-nätverket.
4. Starta redundansväxlingen genom att klicka på **OK**.
5. Följ förloppet genom att klicka på den virtuella datorn för att öppna dess egenskaper. Du kan också klicka på jobbet **testa redundans** i *valv namn*  >  **Inställningar**  >  **jobb**  > **Site Recovery jobb**.
6. När redundansväxlingen är klar visas repliken av den virtuella Azure-datorn i Azure-portalen > **Virtual Machines**. Kontrol lera att den virtuella datorn har rätt storlek, att den är ansluten till rätt nätverk och att den körs.
7. Du bör nu kunna ansluta till den replikerade virtuella datorn i Azure. [Läs mer](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
8. Vill du ta bort virtuella Azure-datorer som skapades under redundanstestningen klickar du på **Rensa redundanstestning** på den virtuella datorn. I **anteckningar** sparar du alla observationer som är kopplade till redundanstest.

## <a name="fail-over-and-fail-back"></a>Redundans och återställning

När du har konfigurerat replikeringen och kör en detalj granskning för att kontrol lera att allt fungerar kan du växla över till Azure vid behov.

Innan du kör en redundansväxling, om du vill ansluta till datorn i Azure efter redundansväxlingen, ska du [förbereda för att ansluta](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) innan du börjar.

Kör sedan en redundansväxling på följande sätt:


1. I **Inställningar**  >  **replikerade objekt** klickar du på datorn > **redundans**.
2. Välj den återställnings punkt som du vill använda.
3. I **redundanstestning** väljer du Azure-nätverket.
4. Välj **Stäng datorn innan du påbörjar redundans**. Med den här inställningen försöker Site Recovery stänga av käll datorn innan redundansväxlingen påbörjas. Redundansväxlingen fortsätter dock även om avstängningen Miss lyckas.
5. Starta redundansväxlingen genom att klicka på **OK**. Du kan följa förloppet för redundansväxlingen på **jobb** sidan.
6. När redundansväxlingen är klar visas repliken av den virtuella Azure-datorn i Azure-portalen > **Virtual Machines**. Om du har för berett för att ansluta efter redundansväxlingen kontrollerar du att den virtuella datorn har rätt storlek, är ansluten till rätt nätverk och kör.
7. När du har verifierat den virtuella datorn klickar du på **genomför** för att slutföra redundansväxlingen. Detta tar bort alla tillgängliga återställnings punkter.

> [!WARNING]
> Avbryt inte en pågående redundansväxling: Innan redundansen startas så stoppas replikeringen av den virtuella datorn. Om du avbryter en pågående redundans så stoppas redundansen, men den virtuella datorn kommer inte att replikeras igen.


### <a name="fail-back-to-azure-stack"></a>Växla tillbaka till Azure Stack

När den primära platsen är igång igen kan du växla tillbaka från Azure till Azure Stack. Det gör du genom att följa stegen som visas [här](/azure-stack/operator/site-recovery-failback).

## <a name="conclusion"></a>Slutsats

I den här artikeln replikerade vi Azure Stack virtuella datorer till Azure. Med replikering på plats körde vi en haveri beredskap för att se till att redundans till Azure fungerade som förväntat. Artikeln innehåller också anvisningar för att köra en fullständig redundansväxling till Azure och att återställa till Azure Stack.

## <a name="next-steps"></a>Nästa steg

När du har återställt igen kan du återställa den virtuella datorn och börja replikera den till Azure igen för att göra detta genom att upprepa stegen i den här artikeln.