---
title: Privata slutpunkter
description: Förstå processen med att skapa privata slut punkter för Azure Backup och scenarier där privata slut punkter används för att upprätthålla säkerheten för dina resurser.
ms.topic: conceptual
ms.date: 05/07/2020
ms.openlocfilehash: 9363aaf45a7c092d8a773a07803c8c1bce1eedd7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101728220"
---
# <a name="private-endpoints-for-azure-backup"></a>Privata slut punkter för Azure Backup

Med Azure Backup kan du säkerhetskopiera och återställa data på ett säkert sätt från dina Recovery Services-valv med [privata slut punkter](../private-link/private-endpoint-overview.md). Privata slut punkter använder en eller flera privata IP-adresser från ditt VNet, vilket effektivt ansluter tjänsten till ditt VNet.

Den här artikeln hjälper dig att förstå processen med att skapa privata slut punkter för Azure Backup och scenarier där privata slut punkter används för att upprätthålla säkerheten för dina resurser.

## <a name="before-you-start"></a>Innan du börjar

- Privata slut punkter kan bara skapas för nya Recovery Services-valv (som inte har några registrerade objekt i valvet). Därför måste privata slut punkter skapas innan du försöker skydda några objekt i valvet.
- Ett virtuellt nätverk kan innehålla privata slut punkter för flera Recovery Services-valv. Ett Recovery Services valv kan också ha privata slut punkter för det i flera virtuella nätverk. Det maximala antalet privata slut punkter som kan skapas för ett valv är dock 12.
- När en privat slut punkt har skapats för ett valv kommer valvet att låsas upp. Den är inte tillgänglig (för säkerhets kopiering och återställning) från nätverk som skiljer sig från dem som innehåller en privat slut punkt för valvet. Om alla privata slut punkter för valvet tas bort, kommer valvet att vara tillgängligt från alla nätverk.
- En privat slut punkts anslutning för säkerhets kopiering använder totalt 11 privata IP-adresser i ditt undernät, inklusive de som används av Azure Backup för lagring. Det här antalet kan vara högre (upp till 25) för vissa Azure-regioner. Vi föreslår att du har tillräckligt med privata IP-adresser tillgängliga när du försöker skapa privata slut punkter för säkerhets kopiering.
- Även om ett Recovery Services valv används av (både) Azure Backup och Azure Site Recovery, diskuterar den här artikeln användning av privata slut punkter för enbart Azure Backup.
- Azure Active Directory stöder för närvarande inte privata slut punkter. IP-adresser och FQDN: er som krävs för att Azure Active Directory ska fungera i en region måste vara tillåtna utgående åtkomst från det skyddade nätverket när du säkerhetskopierar databaser i virtuella Azure-datorer och säkerhets kopiering med MARS-agenten. Du kan också använda NSG-Taggar och Azure Firewall-taggar för att tillåta åtkomst till Azure AD, efter vad som är tillämpligt.
- Virtuella nätverk med nätverks principer stöds inte för privata slut punkter. Du måste inaktivera nätverks principer innan du fortsätter.
- Du måste registrera om Recovery Services Resource Provider med prenumerationen om du registrerade den innan maj 1 2020. Om du vill registrera providern igen går du till prenumerationen i Azure Portal, navigerar till **resurs leverantören** i det vänstra navigerings fältet och väljer sedan **Microsoft. RecoveryServices** och väljer **Omregistrera**.
- [Återställning mellan regioner](backup-create-rs-vault.md#set-cross-region-restore) för SQL och SAP HANA databas säkerhets kopieringar stöds inte om valvet har aktiverade privata slut punkter.
- När du flyttar ett Recovery Services valv som redan använder privata slut punkter till en ny klient måste du uppdatera Recovery Services-valvet för att återskapa och konfigurera om valvets hanterade identitet och skapa nya privata slut punkter efter behov (som bör finnas i den nya klienten). Om detta inte är slutfört kommer säkerhets kopierings-och återställnings åtgärderna att börja fungera. Dessutom måste alla rollbaserade behörigheter för åtkomst kontroll (RBAC) som kon figurer ATS i prenumerationen konfigureras om.

## <a name="recommended-and-supported-scenarios"></a>Rekommenderade och stödda scenarier

När privata slut punkter är aktiverade för valvet används de för säkerhets kopiering och återställning av SQL och SAP HANA arbets belastningar i en Azure VM-och MARS agent-säkerhetskopiering. Du kan också använda valvet för säkerhets kopiering av andra arbets belastningar även (de kräver inte privata slut punkter). Förutom säkerhets kopiering av SQL och SAP HANA arbets belastningar och säkerhets kopiering med MARS-agenten används även privata slut punkter för att utföra fil återställning för Azure VM-säkerhetskopiering. Mer information finns i följande tabell:

| Säkerhets kopiering av arbets belastningar i Azure VM (SQL, SAP HANA), säkerhets kopiering med MARS-agenten | Användning av privata slut punkter rekommenderas för att tillåta säkerhets kopiering och återställning utan att behöva lägga till i en lista över tillåtna IP-adresser/FQDN: er för Azure Backup eller Azure Storage från dina virtuella nätverk. I det scenariot ser du till att virtuella datorer som är värdar för SQL-databaser kan komma åt Azure AD IP-adresser eller FQDN. |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **VIRTUELL Azure-säkerhetskopiering**                                         | Säkerhets kopiering av virtuella datorer kräver inte att du tillåter åtkomst till några IP-adresser eller FQDN. Det kräver därför inte privata slut punkter för säkerhets kopiering och återställning av diskar.  <br><br>   Fil återställning från ett valv som innehåller privata slut punkter skulle dock begränsas till virtuella nätverk som innehåller en privat slut punkt för valvet. <br><br>    När du använder ACL'ed ohanterade diskar bör du se till att lagrings kontot som innehåller diskarna ger till gång till **betrodda Microsoft-tjänster** om det är ACL'ed. |
| **Azure Files säkerhets kopiering**                                      | Azure Files säkerhets kopior lagras i det lokala lagrings kontot. Det kräver därför inte privata slut punkter för säkerhets kopiering och återställning. |

## <a name="get-started-with-creating-private-endpoints-for-backup"></a>Kom igång med att skapa privata slut punkter för säkerhets kopiering

I följande avsnitt beskrivs de steg som ingår i att skapa och använda privata slut punkter för Azure Backup i dina virtuella nätverk.

>[!IMPORTANT]
> Vi rekommenderar starkt att du följer stegen i samma ordning som anges i det här dokumentet. Om du inte gör det kan det leda till att valvet återges som inkompatibelt för att använda privata slut punkter och kräver att du startar om processen med ett nytt valv.

## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Privata slut punkter för säkerhets kopiering kan bara skapas för Recovery Services valv som inte har några objekt skyddade (eller inte haft några objekt som försökte skyddas eller registrerats tidigare). Vi föreslår att du skapar ett nytt valv att börja med. Mer information om hur du skapar ett nytt valv finns i  [skapa och konfigurera ett Recovery Services valv](backup-create-rs-vault.md).

I [det här avsnittet](#create-a-recovery-services-vault-using-the-azure-resource-manager-client) finns information om hur du skapar ett valv med hjälp av Azure Resource Manager-klienten. Detta skapar ett valv med dess hanterade identitet redan aktive rad.

## <a name="enable-managed-identity-for-your-vault"></a>Aktivera hanterad identitet för ditt valv

Hanterade identiteter tillåter valvet att skapa och använda privata slut punkter. Det här avsnittet handlar om hur du aktiverar den hanterade identiteten för ditt valv.

1. Gå till ditt Recovery Services valv – > **identitet**.

    ![Ändra identitetens status till på](./media/private-endpoints/identity-status-on.png)

1. Ändra **statusen** till **på** och välj **Spara**.

1. Ett **objekt-ID** genereras, vilket är valvets hanterade identitet.

    >[!NOTE]
    >Den hanterade identiteten får **inte** inaktive ras (även tillfälligt). Inaktive ring av den hanterade identiteten kan leda till inkonsekvent beteende.

## <a name="grant-permissions-to-the-vault-to-create-required-private-endpoints"></a>Ge valvet behörighet att skapa nödvändiga privata slut punkter

För att skapa nödvändiga privata slut punkter för Azure Backup måste valvet (den hanterade identiteten för valvet) ha behörighet till följande resurs grupper:

- Resurs gruppen som innehåller det virtuella mål nätverket
- Resurs gruppen där de privata slut punkterna ska skapas
- Resurs gruppen som innehåller Privat DNS zoner, som beskrivs i detalj [här](#create-private-endpoints-for-azure-backup)

Vi rekommenderar att du ger **deltagar** rollen för dessa tre resurs grupper till valvet (hanterad identitet). Följande steg beskriver hur du gör detta för en viss resurs grupp (detta måste göras för var och en av de tre resurs grupperna):

1. Gå till resurs gruppen och navigera till **Access Control (IAM)** i det vänstra fältet.
1. När **Access Control** går du till **Lägg till en roll tilldelning**.

    ![Lägg till en rolltilldelning](./media/private-endpoints/add-role-assignment.png)

1. I fönstret **Lägg till roll tilldelning** väljer du **deltagare** som **roll** och använder **namnet** på valvet som **huvud konto**. Välj valvet och välj **Spara** när du är färdig.

    ![Välj roll och huvud konto](./media/private-endpoints/choose-role-and-principal.png)

Information om hur du hanterar behörigheter på en mer detaljerad nivå finns i [skapa roller och behörigheter manuellt](#create-roles-and-permissions-manually).

## <a name="create-private-endpoints-for-azure-backup"></a>Skapa privata slut punkter för Azure Backup

I det här avsnittet beskrivs hur du skapar en privat slut punkt för ditt valv.

1. Navigera till valvet som du skapade ovan och gå till **anslutningar för privata slut punkter** i det vänstra navigerings fältet. Välj **+ privat slut punkt** överst för att börja skapa en ny privat slut punkt för valvet.

    ![Skapa ny privat slut punkt](./media/private-endpoints/new-private-endpoint.png)

1. En gång i processen för att **skapa en privat slut punkt** måste du ange information om hur du skapar din privata slut punkts anslutning.
  
    1. **Grundläggande** information: Fyll i den grundläggande informationen för dina privata slut punkter. Regionen ska vara samma som valvet och den resurs som säkerhets kopie ras.

        ![Fyll i grundläggande information](./media/private-endpoints/basics-tab.png)

    1. **Resurs**: den här fliken kräver att du väljer den PaaS-resurs som du vill skapa anslutningen för. Välj **Microsoft. RecoveryServices/valv** från resurs typen för den önskade prenumerationen. När du är färdig väljer du namnet på Recovery Services valvet som **resurs** -och **AzureBackup** som **mål under resurs**.

        ![Välj resurs för anslutningen](./media/private-endpoints/resource-tab.png)

    1. **Konfiguration**: i konfiguration anger du det virtuella nätverk och undernät där du vill att den privata slut punkten ska skapas. Detta är det VNet där den virtuella datorn finns.

        För att du ska kunna ansluta privat behöver du nödvändiga DNS-poster. Beroende på din nätverks konfiguration kan du välja något av följande:

          - Integrera din privata slut punkt med en privat DNS-zon: Välj **Ja** om du vill integrera.
          - Använd din anpassade DNS-Server: Välj **Nej** om du vill använda din egen DNS-server.

        Hantering av DNS-poster för båda dessa [beskrivs senare](#manage-dns-records).

          ![Ange det virtuella nätverket och under nätet](./media/private-endpoints/configuration-tab.png)

    1. Du kan också lägga till **taggar** för din privata slut punkt.
    1. Fortsätt att **Granska + skapa** när du har skrivit in information. När verifieringen är klar väljer du **skapa** för att skapa den privata slut punkten.

## <a name="approve-private-endpoints"></a>Godkänn privata slut punkter

Om användaren som skapar den privata slut punkten också är ägare av Recovery Services-valvet, godkänns den privata slut punkten som skapades ovan automatiskt. Annars måste ägaren av valvet godkänna den privata slut punkten innan du kan använda den. I det här avsnittet beskrivs manuella godkännanden av privata slut punkter via Azure Portal.

Se [manuellt godkännande av privata slut punkter med hjälp av Azure Resource Manager-klienten](#manual-approval-of-private-endpoints-using-the-azure-resource-manager-client) för att använda Azure Resource Manager-klienten för att godkänna privata slut punkter.

1. I Recovery Services-valvet navigerar du till **anslutningar för privata slut punkter** i det vänstra fältet.
1. Välj den privata slut punkts anslutning som du vill godkänna.
1. Välj **Godkänn** i det översta fältet. Du kan också välja **avvisa** eller **ta bort** om du vill avvisa eller ta bort slut punkts anslutningen.

    ![Godkänn privata slut punkter](./media/private-endpoints/approve-private-endpoints.png)

## <a name="manage-dns-records"></a>Hantera DNS-poster

Som tidigare beskrivits behöver du nödvändiga DNS-poster i dina privata DNS-zoner eller-servrar för att kunna ansluta privat. Du kan antingen integrera din privata slut punkt direkt med Azures privata DNS-zoner eller använda dina anpassade DNS-servrar för att uppnå detta, baserat på dina nätverks inställningar. Detta måste göras för alla tre tjänsterna: säkerhets kopiering, blobbar och köer.

### <a name="when-integrating-private-endpoints-with-azure-private-dns-zones"></a>När du integrerar privata slut punkter med Azures privata DNS-zoner

Om du väljer att integrera din privata slut punkt med privata DNS-zoner lägger backup till de DNS-poster som krävs. Du kan visa de privata DNS-zoner som används under **DNS-konfigurationen** av den privata slut punkten. Om dessa DNS-zoner inte finns skapas de automatiskt när den privata slut punkten skapas. Du måste dock kontrol lera att det virtuella nätverket (som innehåller de resurser som ska säkerhets kopie RAS) är korrekt länkat till alla tre privata DNS-zoner, enligt beskrivningen nedan.

![DNS-konfiguration i Azures privata DNS-zon](./media/private-endpoints/dns-configuration.png)

#### <a name="validate-virtual-network-links-in-private-dns-zones"></a>Verifiera virtuella nätverks länkar i privata DNS-zoner

För **varje privat DNS-** zon som anges ovan (för säkerhets kopiering, blobbar och köer) gör du följande:

1. Navigera till respektive **virtuella nätverks länkar** -alternativ i det vänstra navigerings fältet.
1. Du bör kunna se en post för det virtuella nätverk som du har skapat den privata slut punkten för, som den som visas nedan:

    ![Virtuellt nätverk för privat slut punkt](./media/private-endpoints/virtual-network-links.png)

1. Om du inte ser någon post lägger du till en virtuell nätverks länk till alla DNS-zoner som inte har dem.

    ![Lägg till virtuellt nätverks länk](./media/private-endpoints/add-virtual-network-link.png)

### <a name="when-using-custom-dns-server-or-host-files"></a>När du använder anpassade DNS-servrar eller-databasfiler

Om du använder dina anpassade DNS-servrar måste du skapa de DNS-zoner som krävs och lägga till DNS-posterna som krävs av de privata slut punkterna på dina DNS-servrar. För blobbar och köer kan du också använda villkorliga vidarebefordrare.

#### <a name="for-the-backup-service"></a>För säkerhets kopierings tjänsten

1. I DNS-servern skapar du en DNS-zon för säkerhets kopiering enligt följande namngivnings konvention:

    |Zon |Tjänst |
    |---------|---------|
    |`privatelink.<geo>.backup.windowsazure.com`   |  Backup        |

    >[!NOTE]
    > I ovanstående text `<geo>` avser region koden (till exempel *EUs* och *Ne* för USA, östra och Nord Europa). Se följande listor för regions koder:
    >
    > - [Alla offentliga moln](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx)
    > - [Kina](/azure/china/resources-developer-guide#check-endpoints-in-azure)
    > - [Tyskland](../germany/germany-developer-guide.md#endpoint-mapping)
    > - [US Gov](../azure-government/documentation-government-developer-guide.md)

1. Därefter måste vi lägga till de DNS-poster som krävs. Om du vill visa de poster som behöver läggas till i DNS-zonen för säkerhets kopiering navigerar du till den privata slut punkt som du skapade ovan och går till alternativet **DNS-konfiguration** i det vänstra navigerings fältet.

    ![DNS-konfiguration för anpassad DNS-Server](./media/private-endpoints/custom-dns-configuration.png)

1. Lägg till en post för varje FQDN och IP-adress som visas som en typ poster i din DNS-zon för säkerhets kopiering. Om du använder en värd fil för namn matchning ska du göra motsvarande poster i värd filen för varje IP-adress och FQDN enligt följande format:

    `<private ip><space><backup service privatelink FQDN>`

>[!NOTE]
>Som du ser i skärm bilden ovan visas FQDN-namnen `xxxxxxxx.<geo>.backup.windowsazure.com` och inte `xxxxxxxx.privatelink.<geo>.backup. windowsazure.com` . I sådana fall måste du se till att du inkluderar (och vid behov lägger till) `.privatelink.` enligt det angivna formatet.

#### <a name="for-blob-and-queue-services"></a>För blob-och Queue Services

För blobbar och köer kan du antingen använda villkorliga vidarebefordrare eller skapa DNS-zoner på DNS-servern.

##### <a name="if-using-conditional-forwarders"></a>Om du använder villkorliga vidarebefordrare

Om du använder villkorliga vidarebefordrare lägger du till vidarebefordrare för blob-och Queue-FQDN enligt följande:

|FQDN  |IP-adress  |
|---------|---------|
|`privatelink.blob.core.windows.net`     |  168.63.129.16       |
|`privatelink.queue.core.windows.net`     | 168.63.129.16        |

##### <a name="if-using-private-dns-zones"></a>Om du använder privata DNS-zoner

Om du använder DNS-zoner för blobbar och köer måste du först skapa dessa DNS-zoner och senare lägga till nödvändiga poster.

|Zon |Tjänst  |
|---------|---------|
|`privatelink.blob.core.windows.net`     |  Blob     |
|`privatelink.queue.core.windows.net`     | Kö        |

Just nu skapar vi bara zonerna för blobbar och köer när du använder anpassade DNS-servrar. Att lägga till DNS-poster görs senare i två steg:

1. När du registrerar den första säkerhets kopierings instansen, det vill säga när du konfigurerar säkerhets kopiering för första gången
1. När du kör den första säkerhets kopieringen

Vi utför de här stegen i följande avsnitt.

## <a name="use-private-endpoints-for-backup"></a>Använd privata slut punkter för säkerhets kopiering

När de privata slut punkterna som har skapats för valvet i ditt VNet har godkänts, kan du börja använda dem för att utföra säkerhets kopiering och återställning.

>[!IMPORTANT]
>Se till att du har slutfört alla steg som anges ovan i dokumentet innan du fortsätter. För att Sammanfattning måste du ha slutfört stegen i följande check lista:
>
>1. Skapade ett (nytt) Recovery Services valv
>2. Har aktiverat valvet för att använda systemtilldelad hanterad identitet
>3. Tilldelade relevanta behörigheter till valvets hanterade identitet
>4. En privat slut punkt har skapats för valvet
>5. Godkände den privata slut punkten (om inte automatiskt godkänd)
>6. Säkerställt att alla DNS-poster har lagts till korrekt (förutom blob-och Queue-poster för anpassade servrar, som kommer att diskuteras i följande avsnitt)

### <a name="check-vm-connectivity"></a>Kontrol lera VM-anslutning

I den virtuella datorn i det låsta nätverket kontrollerar du följande:

1. Den virtuella datorn ska ha åtkomst till AAD.
2. Kör **nslookup** på säkerhets kopierings-URL: en ( `xxxxxxxx.privatelink.<geo>.backup. windowsazure.com` ) från din virtuella dator för att säkerställa anslutningen. Detta bör returnera den privata IP-adress som tilldelats i det virtuella nätverket.

### <a name="configure-backup"></a>Konfigurera säkerhetskopiering

När du ser till att ovanstående check lista och åtkomst har slutförts, kan du fortsätta att konfigurera säkerhets kopiering av arbets belastningar till valvet. Om du använder en anpassad DNS-server måste du lägga till DNS-poster för blobbar och köer som är tillgängliga när du har konfigurerat den första säkerhets kopieringen.

#### <a name="dns-records-for-blobs-and-queues-only-for-custom-dns-servershost-files-after-the-first-registration"></a>DNS-poster för blobbar och köer (endast för anpassade DNS-servrar/-värd-filer) efter den första registreringen

När du har konfigurerat säkerhets kopiering för minst en resurs i ett aktiverat valv för privat slut punkt lägger du till de DNS-poster som krävs för blobbar och köer enligt beskrivningen nedan.

1. Navigera till din resurs grupp och Sök efter den privata slut punkt som du skapade.
1. Förutom namnet på den privata slut punkten som du fått av dig visas två fler privata slut punkter som skapas. Dessa börjar med `<the name of the private endpoint>_ecs` och används med respektive suffix `_blob` `_queue` .

    ![Privata slut punkts resurser](./media/private-endpoints/private-endpoint-resources.png)

1. Navigera till var och en av dessa privata slut punkter. I DNS-konfigurationsobjektet för var och en av de två privata slut punkterna visas en post med och ett fullständigt domän namn och en IP-adress. Lägg till båda dessa på din anpassade DNS-Server förutom de som beskrivs ovan.
Om du använder en värd fil ska du göra motsvarande poster i värd filen för varje IP/FQDN enligt följande format:

    `<private ip><space><blob service privatelink FQDN>`<br>
    `<private ip><space><queue service privatelink FQDN>`

    ![BLOB-DNS-konfiguration](./media/private-endpoints/blob-dns-configuration.png)

Förutom ovanstående, finns det en annan post som krävs efter den första säkerhets kopieringen, som diskuteras [senare](#dns-records-for-blobs-only-for-custom-dns-servershost-files-after-the-first-backup).

### <a name="backup-and-restore-of-workloads-in-azure-vm-sql-and-sap-hana"></a>Säkerhets kopiering och återställning av arbets belastningar i virtuell Azure-dator (SQL och SAP HANA)

När den privata slut punkten har skapats och godkänts krävs inga andra ändringar från klient sidan för att använda den privata slut punkten (om du inte använder SQL-tillgänglighetsgrupper, som vi diskuterar senare i det här avsnittet). All kommunikation och data överföring från det skyddade nätverket till valvet utförs via den privata slut punkten. Men om du tar bort privata slut punkter för valvet efter att en server (SQL eller SAP HANA) har registrerats på den, måste du registrera om behållaren med valvet. Du behöver inte sluta skydda dem.

#### <a name="dns-records-for-blobs-only-for-custom-dns-servershost-files-after-the-first-backup"></a>DNS-poster för blobbar (endast för anpassade DNS-servrar/-värd-filer) efter den första säkerhets kopieringen

När du har kört den första säkerhets kopieringen och använder en anpassad DNS-server (utan villkorlig vidarebefordran), är det troligt att säkerhets kopieringen Miss kommer. Om det händer:

1. Navigera till din resurs grupp och Sök efter den privata slut punkt som du skapade.
1. Förutom de tre privata slut punkter som diskuterats tidigare ser du nu en fjärde privat slut punkt med namnet som börjar med `<the name of the private endpoint>_prot` och är suffix med `_blob` .

    ![Privata endpoing med suffixet "prot"](./media/private-endpoints/private-endpoint-prot.png)

1. Navigera till den här nya privata slut punkten. I alternativet DNS-konfiguration visas en post med ett fullständigt domän namn och en IP-adress. Lägg till dessa på din privata DNS-Server förutom de som beskrivs ovan.

    Om du använder en värd fil ska du göra motsvarande poster i värd filen för varje IP-adress och FQDN enligt följande format:

    `<private ip><space><blob service privatelink FQDN>`

>[!NOTE]
>Nu bör du kunna köra **nslookup** från den virtuella datorn och matcha till privata IP-adresser när du är färdig på valvets URL: er för säkerhets kopiering och lagring.

### <a name="when-using-sql-availability-groups"></a>När du använder SQL-tillgänglighetsgrupper

När du använder SQL Availability groups (AG) måste du etablera villkorlig vidarebefordran i den anpassade AG DNS enligt beskrivningen nedan:

1. Logga in på domänkontrollanten.
1. Under DNS-programmet lägger du till villkorliga vidarebefordrare för alla tre DNS-zoner (säkerhets kopiering, blobbar och köer) till värd-IP-168.63.129.16 eller den anpassade DNS-serverns IP-adress, om det behövs. Följande skärm bilder visas när du vidarebefordrar till Azure-värd-IP. Om du använder en egen DNS-Server ersätter du med IP-adressen för DNS-servern.

    ![Villkorliga vidarebefordrare i DNS-hanteraren](./media/private-endpoints/dns-manager.png)

    ![Ny villkorlig vidarebefordrare](./media/private-endpoints/new-conditional-forwarder.png)

### <a name="backup-and-restore-through-mars-agent"></a>Säkerhetskopiera och Återställ via MARS-agenten

När du använder MARS-agenten för att säkerhetskopiera lokala resurser, se till att ditt lokala nätverk (som innehåller dina resurser som ska säkerhets kopie RAS) är peer-kopplat med det virtuella Azure-nätverket som innehåller en privat slut punkt för valvet, så att du kan använda det. Sedan kan du fortsätta att installera MARS-agenten och konfigurera säkerhets kopieringen enligt beskrivningen här. Du måste dock se till att all kommunikation för säkerhets kopiering sker via endast peer-nätverket.

Men om du tar bort privata slut punkter för valvet efter att en MARS-agent har registrerats på den, måste du registrera om behållaren med valvet. Du behöver inte sluta skydda dem.

## <a name="additional-topics"></a>Ytterligare information

### <a name="create-a-recovery-services-vault-using-the-azure-resource-manager-client"></a>Skapa ett Recovery Services valv med hjälp av Azure Resource Manager-klienten

Du kan skapa Recovery Services-valvet och aktivera dess hanterade identitet (vilket gör att den hanterade identiteten krävs, som vi senare ser) med hjälp av Azure Resource Manager-klienten. Ett exempel på detta är att delas nedan:

```rest
armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>?api-version=2017-07-01-preview @C:\<filepath>\MSIVault.json
```

JSON-filen ovan bör ha följande innehåll:

Begär JSON:

```json
{
  "location": "eastus2",
  "name": "<vaultname>",
  "etag": "W/\"datetime'2019-05-24T12%3A54%3A42.1757237Z'\"",
  "tags": {
    "PutKey": "PutValue"
  },
  "properties": {},
  "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
  "type": "Microsoft.RecoveryServices/Vaults",
  "sku": {
    "name": "RS0",
    "tier": "Standard"
  },
  "identity": {
    "type": "systemassigned"
  }
}
```

Svars-JSON:

```json
{
   "location": "eastus2",
   "name": "<vaultname>",
   "etag": "W/\"datetime'2020-02-25T05%3A26%3A58.5181122Z'\"",
   "tags": {
     "PutKey": "PutValue"
   },
   "identity": {
     "tenantId": "<tenantid>",
     "principalId": "<principalid>",
     "type": "SystemAssigned"
   },
   "properties": {
     "provisioningState": "Succeeded",
     "privateEndpointStateForBackup": "None",
     "privateEndpointStateForSiteRecovery": "None"
   },
   "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
   "type": "Microsoft.RecoveryServices/Vaults",
   "sku": {
     "name": "RS0",
     "tier": "Standard"
   }
 }
```

>[!NOTE]
>Valvet som skapats i det här exemplet via Azure Resource Manager-klienten har redan skapats med en systemtilldelad hanterad identitet.

### <a name="managing-permissions-on-resource-groups"></a>Hantera behörigheter för resurs grupper

Den hanterade identiteten för valvet måste ha följande behörigheter i resurs gruppen och det virtuella nätverk där de privata slut punkterna ska skapas:

- `Microsoft.Network/privateEndpoints/*` Detta krävs för att utföra CRUD på privata slut punkter i resurs gruppen. Den ska tilldelas till resurs gruppen.
- `Microsoft.Network/virtualNetworks/subnets/join/action` Detta krävs på det virtuella nätverk där privat IP ska anslutas till den privata slut punkten.
- `Microsoft.Network/networkInterfaces/read` Detta krävs på resurs gruppen för att hämta det nätverks gränssnitt som skapats för den privata slut punkten.
- Rollen Privat DNS Zone-deltagare den här rollen finns redan och kan användas för att ge `Microsoft.Network/privateDnsZones/A/*` och ha `Microsoft.Network/privateDnsZones/virtualNetworkLinks/read` behörighet.

Du kan använda någon av följande metoder för att skapa roller med nödvändiga behörigheter:

#### <a name="create-roles-and-permissions-manually"></a>Skapa roller och behörigheter manuellt

Skapa följande JSON-filer och Använd PowerShell-kommandot i slutet av avsnittet för att skapa roller:

PrivateEndpointContributorRoleDef.jspå

```json
{
  "Name": "PrivateEndpointContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows management of Private Endpoint",
  "Actions": [
    "Microsoft.Network/privateEndpoints/*",
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

NetworkInterfaceReaderRoleDef.jspå

```json
{
  "Name": "NetworkInterfaceReader",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows read on networkInterfaces",
  "Actions": [
    "Microsoft.Network/networkInterfaces/read"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

PrivateEndpointSubnetContributorRoleDef.jspå

```json
{
  "Name": "PrivateEndpointSubnetContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows adding of Private Endpoint connection to Virtual Networks",
  "Actions": [
    "Microsoft.Network/virtualNetworks/subnets/join/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

```azurepowershell
 New-AzRoleDefinition -InputFile "PrivateEndpointContributorRoleDef.json"
 New-AzRoleDefinition -InputFile "NetworkInterfaceReaderRoleDef.json"
 New-AzRoleDefinition -InputFile "PrivateEndpointSubnetContributorRoleDef.json"
```

#### <a name="use-a-script"></a>Använd ett skript

1. Starta **Cloud Shell** i Azure Portal och välj **Ladda upp fil** i PowerShell-fönstret.

    ![Välj Ladda upp fil i PowerShell-fönstret](./media/private-endpoints/upload-file-in-powershell.png)

1. Överför följande skript: [VaultMsiPrereqScript](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/VaultMsiPrereqScript.ps1)

1. Gå till din arbetsmapp (till exempel: `cd /home/user` )

1. Kör följande skript:

    ```azurepowershell
    ./VaultMsiPrereqScript.ps1 -subscription <subscription-Id> -vaultPEResourceGroup <vaultPERG> -vaultPESubnetResourceGroup <subnetRG> -vaultMsiName <msiName>
    ```

    Följande är parametrarna:

    - **prenumeration**: * * SubscriptionId som har resurs gruppen där den privata slut punkten för valvet ska skapas och under nätet där valvets privata slut punkt ska kopplas

    - **vaultPEResourceGroup**: resurs grupp där den privata slut punkten för valvet ska skapas

    - **vaultPESubnetResourceGroup**: resurs grupp för det undernät som den privata slut punkten ska kopplas till

    - **vaultMsiName**: namnet på valvets MSI, som är samma som **VaultName**

1. Slutför autentiseringen och skriptet tar kontexten för den angivna prenumerationen som anges ovan. Det skapar lämpliga roller om de saknas från klienten och tilldelar roller till valvets MSI.

### <a name="creating-private-endpoints-using-azure-powershell"></a>Skapa privata slut punkter med Azure PowerShell

#### <a name="auto-approved-private-endpoints"></a>Automatiskt godkända privata slut punkter

```azurepowershell
$vault = Get-AzRecoveryServicesVault `
        -ResourceGroupName $vaultResourceGroupName `
        -Name $vaultName
  
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name $privateEndpointConnectionName `
        -PrivateLinkServiceId $vault.ID `
        -GroupId "AzureBackup"  

$vnet = Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $VMResourceGroupName
$subnet = $vnet | Select -ExpandProperty subnets | Where-Object {$_.Name -eq '<subnetName>'}


$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $vmResourceGroupName `
        -Name $privateEndpointName `
        -Location $location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -Force
```

### <a name="manual-approval-of-private-endpoints-using-the-azure-resource-manager-client"></a>Manuellt godkännande av privata slut punkter med hjälp av Azure Resource Manager-klienten

1. Använd **GetVault** för att hämta anslutnings-ID: t för den privata slut punkten.

    ```rest
    armclient GET /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/vaults/<vaultname>?api-version=2017-07-01-preview
    ```

    Då returneras anslutnings-ID för privat slut punkt. Namnet på anslutningen kan hämtas med hjälp av den första delen av anslutnings-ID: t enligt följande:

    `privateendpointconnectionid = {peName}.{vaultId}.backup.{guid}`

1. Hämta **anslutnings-ID för privat slutpunkt** (och **namnet på den privata slut punkten**, där det behövs) från svaret och Ersätt det i följande JSON-och Azure Resource Manager-URI och försök att ändra status till "godkänd/nekad/frånkopplad", som visas i exemplet nedan:

    ```rest
    armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>?api-version=2020-02-02-preview @C:\<filepath>\BackupAdminApproval.json
    ```

    UTGÖR

    ```json
    {
    "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>",
    "properties": {
        "privateEndpoint": {
        "id": "/subscriptions/<subscriptionid>/resourceGroups/<pergname>/providers/Microsoft.Network/privateEndpoints/pename"
        },
        "privateLinkServiceConnectionState": {
        "status": "Disconnected",  //choose state from Approved/Rejected/Disconnected
        "description": "Disconnected by <userid>"
        }
    }
    }
    ```

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

F. Kan jag skapa en privat slut punkt för ett befintligt säkerhets kopierings valv?<br>
A. Nej, privata slut punkter kan bara skapas för nya säkerhets kopierings valv. Därför får valvet inte ha haft några objekt skyddade. I själva verket kan inga försök att skydda objekt i valvet göras innan du skapar privata slut punkter.

F. Jag försökte skydda ett objekt till mitt valv, men det misslyckades och valvet innehåller fortfarande inga objekt som är skyddade för det. Kan jag skapa privata slut punkter för det här valvet?<br>
A. Nej, valvet får inte ha haft några försök att skydda några objekt tidigare.

F. Jag har ett valv som använder privata slut punkter för säkerhets kopiering och återställning. Kan jag senare lägga till eller ta bort privata slut punkter för det här valvet även om jag har säkerhetskopierat objekt som skyddas?<br>
A. Ja. Om du redan har skapat privata slut punkter för ett valv och skyddade säkerhets kopierings objekt till den, kan du senare lägga till eller ta bort privata slut punkter efter behov.

F. Kan den privata slut punkten för Azure Backup också användas för Azure Site Recovery?<br>
A. Nej, den privata slut punkten för säkerhets kopiering kan bara användas för Azure Backup. Du måste skapa en ny privat slut punkt för Azure Site Recovery om den stöds av tjänsten.

F. Jag missade något av stegen i den här artikeln och gick vidare för att skydda min data källa. Kan jag fortfarande använda privata slut punkter?<br>
A. Inte följer stegen i artikeln och fortsätter att skydda objekt kan leda till att valvet inte kan använda privata slut punkter. Vi rekommenderar därför att du läser den här check listan innan du fortsätter att skydda objekt.

F. Kan jag använda min egen DNS-server i stället för att använda Azures privata DNS-zon eller en integrerad privat DNS-zon?<br>
A. Ja, du kan använda dina egna DNS-servrar. Se dock till att alla obligatoriska DNS-poster läggs till enligt rekommendationerna i det här avsnittet.

F. Behöver jag utföra några ytterligare åtgärder på min server när jag har följt processen i den här artikeln?<br>
A. När du har använt processen som beskrivs i den här artikeln behöver du inte göra ytterligare arbete för att använda privata slut punkter för säkerhets kopiering och återställning.

## <a name="next-steps"></a>Nästa steg

- Läs om alla [säkerhetsfunktioner i Azure Backup](security-overview.md)