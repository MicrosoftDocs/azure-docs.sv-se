---
title: Felsöka replikeringsfel i VM-migrering utan agent
description: Få hjälp med fel i replikeringen
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: troubleshooting
ms.date: 08/17/2020
ms.openlocfilehash: 33e2bf641b75a5dd360498478f1ea70c7614fb38
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98071382"
---
# <a name="troubleshooting-replication-issues-in-agentless-vmware-vm-migration"></a>Felsöka replikeringsfel i VM-migrering utan agent

I den här artikeln beskrivs några vanliga problem och specifika fel som du kan stöta på när du replikerar lokala virtuella VMware-datorer med hjälp av Azure Migrate: Agent för Server Migration utan agent.

När du replikerar en virtuell VMware-dator med hjälp av metoden utan agent replikeras data från den virtuella datorns&#39;s diskar (VMDK: er) till replik Managed disks i din Azure-prenumeration. När replikeringen startar för en virtuell dator sker en inledande replikering där fullständiga kopior av diskarna replikeras. När den inledande replikeringen har slutförts schemaläggs stegvisa cykler regelbundet för att överföra eventuella ändringar som har inträffat sedan den tidigare replikeringen.

Du kan ibland se att det inte går att köra cykler för en virtuell dator. Dessa fel kan inträffa på grund av orsaker i fel i lokal nätverks konfiguration till problem på den Azure Migrate moln tjänst Server delen. I den här artikeln kommer vi att:

 - Visar hur du kan övervaka replikeringsstatus och lösa fel.
 - Lista några av de vanligaste replikeringsfel och föreslå ytterligare steg för att åtgärda dem.

## <a name="monitor-replication-status-using-the-azure-portal"></a>Övervaka replikeringsstatus med hjälp av Azure Portal

Använd följande steg för att övervaka replikeringsstatus för dina virtuella datorer:

  1. Gå till sidan servrar i Azure Migrate på Azure Portal.
  ![Bild 1](./media/troubleshoot-changed-block-tracking-replication/image0.png)
  1. Gå till sidan "replikera datorer" genom att klicka på "replikera servrar" i panelen Server migrering.
  ![Bild 2](./media/troubleshoot-changed-block-tracking-replication/image1.png)
  1. Du ser en lista över replikering av servrar och ytterligare information som status, hälsa, senaste synkroniseringstid osv. I kolumnen hälsa anges den aktuella replikeringens aktuella hälso tillstånd för den virtuella datorn. Värdet "kritisk" eller "varning" i hälso kolumnen indikerar vanligt vis att den tidigare replikeringen för den virtuella datorn misslyckades. Om du vill ha mer information högerklickar du på den virtuella datorn och väljer fel information. Sidan fel information innehåller information om felet och ytterligare information om hur du felsöker. Du ser också länken "senaste händelser" som kan användas för att navigera till sidan händelser för den virtuella datorn.
  ![Bild 3](./media/troubleshoot-changed-block-tracking-replication/image2.png)
  1. Klicka på senaste händelser om du vill se tidigare Körfel för den virtuella datorn. På sidan händelser söker du efter den senaste händelsen av typen "Replikeringsinitieringen misslyckades" eller "Det gick inte att utföra replikering för disken" för den virtuella datorn.
  ![Bild 4](./media/troubleshoot-changed-block-tracking-replication/image3.png)
  1. Klicka på händelsen för att förstå möjliga orsaker till felet och rekommenderade reparations steg. Använd informationen som visas för att felsöka och åtgärda felet.
 ![Bild 5](./media/troubleshoot-changed-block-tracking-replication/image4.png)

## <a name="common-replication-errors"></a>Vanliga replikeringsfel

I det här avsnittet beskrivs några vanliga fel och hur du kan felsöka dem.

## <a name="key-vault-operation-failed-error-when-trying-to-replicate-vms"></a>Key Vault åtgärden misslyckades vid försök att replikera virtuella datorer

**Fel:** "Key Vault åtgärden misslyckades. Åtgärd: Konfigurera hanterat lagrings konto, Key Vault: nyckel-valv-namn, lagrings konto: lagrings konto namnet misslyckades med felet: "

**Fel:** "Key Vault åtgärden misslyckades. Åtgärd: Skapa signatur definition för delad åtkomst, Key Vault: nyckel-valv-namn, lagrings konto: lagrings konto namnet misslyckades med felet: "

![Key Vault](./media/troubleshoot-changed-block-tracking-replication/key-vault.png)

Det här felet uppstår vanligt vis på grund av att användar åtkomst principen för Key Vault inte ger den inloggade användaren de behörigheter som krävs för att konfigurera lagrings konton som ska Key Vault hanteras. Om du vill kontrol lera användar åtkomst principen i nyckel valvet går du till sidan med nyckel valvet i portalen för nyckel valvet och väljer åtkomst principer 

När portalen skapar nyckel valvet lägger det också till en princip för användar åtkomst som beviljar den inloggade användaren behörighet att konfigurera lagrings konton som ska Key Vault hanteras. Detta kan inte utföras av två orsaker

- Den inloggade användaren är ett fjär huvud konto på Azure-klienten för kunder (CSP-prenumeration – och den inloggade användaren är partner administratör). Lösningen i det här fallet är att ta bort nyckel valvet, logga ut från portalen och sedan logga in med ett användar konto från kund klienten (inte ett fjär huvud konto) och försöka utföra åtgärden igen. CSP-partnern har vanligt vis ett användar konto i kunderna Azure Active Directory klient som de kan använda. Om de inte kan skapa ett nytt användar konto för sig själva i kunderna Azure Active Directory-klienten loggar du in på portalen som den nya användaren och försöker sedan utföra åtgärden replikera igen. Kontot som används måste ha behörighet som ägare eller deltagare och administratör för användar åtkomst till kontot i resurs gruppen (migrera projekt resurs gruppen)

- Det andra fallet var det kan inträffa när en användare (Användare1) försökte konfigurera replikeringen från början och påträffade ett fel, men nyckel valvet har redan skapats (och användar åtkomst principen har tilldelats korrekt till den här användaren). Nu vid ett senare tillfälle försöker en annan användare (användare2) Konfigurera replikering, men åtgärden konfigurera hanterat lagrings konto eller skapa SAS-definition Miss lyckas eftersom det inte finns någon princip för användar åtkomst som motsvarar användare2 i nyckel valvet.

**Lösning**: Lös problemet genom att skapa en princip för användar åtkomst för användare2 i användare2-behörighet för att konfigurera hanterat lagrings konto och generera SAS-definitioner. Användare2 kan göra detta från Azure PowerShell med hjälp av nedanstående cmdlets:

$userPrincipalId = $ (Get-AzureRmADUser-UserPrincipalName "user2_email_address"). Identitet

Set-AzureRmKeyVaultAccessPolicy-VaultName "keyvaultname"-ObjectId $userPrincipalId-PermissionsToStorage get, list, DELETE, set, Update, regeneratekey, Gets, lists, Deletes, Sets, Restore, backup, Restore


## <a name="disposeartefactstimedout"></a>DisposeArtefactsTimedOut

**Fel-ID:** 181008

**Fel meddelande:** Virtuell dator: VMName. Fel: tids gränsen påträffades för händelsen DisposeArtefactsTimeout i tillstånd & [Gateway. service. StateMachine. SnapshotReplication. SnapshotReplicationEngine + WaitingForArtefactsDisposalPreCycle (' WaitingForArtefactsDisposalPreCycle ')] '.

**Möjliga orsaker:**

Komponenten som försöker replikera data till Azure är antingen avstängd eller svarar inte. Möjliga orsaker är:

- Gateway-tjänsten som körs i Azure Migrates enheten är avstängd.
- Gateway-tjänsten har problem med anslutningen till lagrings kontot för Service Bus/Event Hub/-utrustning.

**Identifiera den exakta orsaken till DisposeArtefactsTimedOut och motsvarande lösning:**

1. Kontrollera att Azure Migrate-installationen är igång.
2. Kontrol lera om Gateway-tjänsten körs på enheten:
   1.  Logga in på Azure Migrate-enheten med hjälp av fjärr skrivbord och gör följande.

   2.  Öppna MMC-snapin-modulen för Microsoft-tjänster (kör > Services. msc) och kontrol lera om "Microsoft Azure Gateway-tjänsten körs. Starta tjänsten om tjänsten är stoppad eller inte körs. Du kan också öppna kommando tolken eller PowerShell och göra följande: "net start asrgwy"

3. Sök efter anslutnings problem mellan Azure Migrates-och enhets lagrings konto: 

    Kör följande kommando när du har laddat ned AzCopy i Azure Migrate-installationen:
    
    _AzCopy-bänk https://[konto]. blob. Core. Windows. net/[container]? SÄKERHETS_
    
    **Steg för att köra prestandatest testet:**
    
      1. [Ladda ned](../storage/common/storage-use-azcopy-v10.md) AzCopy
        
      2. Sök efter enhetens lagrings konto i resurs gruppen. Lagrings kontot har ett namn som liknar migrategwsa \* \* \* \* \* \* \* \* \* \* . Detta är värdet för parametern [Account] i kommandot ovan.
        
      3. Sök efter ditt lagrings konto i Azure Portal. Se till att den prenumeration du använder för att söka är samma prenumeration (mål prenumerationen) som lagrings kontot skapas i. Gå till behållare i avsnittet BLOB service. Klicka på + container och skapa en behållare. Lämna den offentliga åtkomst nivån till standardvärdet som valts.
        
      4. Gå till signaturen för delad åtkomst under Inställningar. Välj behållare i "tillåten resurs typ". Klicka på Skapa SAS och anslutnings sträng. Kopiera SAS-värdet.
        
      5. Kör kommandot ovan i kommando tolken genom att ersätta konto, behållare, SAS med värdena som hämtades i steg 2, 3 och 4.
        
      Du kan också [hämta](https://go.microsoft.com/fwlink/?linkid=2138967) Azure Storage utforska på installationen och försöka överföra 10 blobbar på ~ 64 MB till lagrings kontona. Om det inte finns något problem bör överföringen lyckas.
        
    **Lösning:** Om testet Miss lyckas finns det&#39;ett nätverks problem. Delta i det lokala nätverkets team för att kontrol lera anslutnings problem. Normalt kan det finnas vissa brand Väggs inställningar som orsakar felen.
    
4.  Sök efter anslutnings problem mellan Azure Migrate-installationen och Service Bus:

    Det här testet kontrollerar om Azure Migrate-enheten kan kommunicera med Azure Migrate moln tjänstens Server del. Enheten kommunicerar med tjänstens Server del via Service Bus-och Event Hub-meddelande köer. Om du vill verifiera anslutningen från installationen till Service Bus [laddar du ned](https://go.microsoft.com/fwlink/?linkid=2139104) Service Bus Explorer, försöker ansluta till enheten Service Bus och utför skicka meddelande/ta emot meddelande. Om det inte finns något problem bör det lyckas.

    **Steg för att köra testet:**

    1. Kopiera anslutnings strängen från den Service Bus som har skapats i det migrerade projektet
    2. Öppna Service Bus Explorer
    3. Gå till filen och Anslut
    4. Klistra in anslutnings strängen och klicka på Anslut
    5. Detta öppnar Service Bus namn området
    6. Välj Snapshot Manager i avsnittet. Högerklicka på Snapshot Manager, välj "ta emot meddelanden" > Välj "Peek" och klicka på OK
    7. Om anslutningen lyckas visas "[x] mottagna meddelanden" i konsolens utdata. Om anslutningen Miss lyckas visas ett meddelande om att anslutningen misslyckades
    
    **Lösning:** Om det här testet Miss lyckas uppstår ett nätverks problem. Delta i det lokala nätverkets team för att kontrol lera anslutnings problem. Normalt kan det finnas vissa brand Väggs inställningar som orsakar felen.

5. Anslutnings problem mellan Azure Migrate-utrustning och Azure Key Vault:

    Det här testet kontrollerar anslutnings problem mellan Azure Migrate-enheten och Azure Key Vault. Key Vault används för att hantera åtkomst till lagrings kontot som används för replikering.
    
    **Steg för att kontrol lera anslutningen:**
    
    1. Hämta Key Vault-URI i listan över resurser i resurs gruppen som motsvarar Azure Migrate Project.
    
    1. Öppna PowerShell i Azure Migrate-installationen och kör följande kommando:
    
    _test-NetConnection Key Vault URI-P 443_
    
    Det här kommandot försöker upprätta en TCP-anslutning och returnerar utdata.
    
     - I utdata kontrollerar du fältet "_TcpTestSucceeded_". Om värdet är "_Sant_" uppstår inga anslutnings problem mellan Azure Migrate-installationen och Azure Key Vault. Om värdet är "false", finns det ett anslutnings problem.
    
    **Lösning:** Om det här testet Miss lyckas uppstår ett anslutnings problem mellan Azure Migrate-apparaten och Azure Key Vault. Delta i det lokala nätverkets team för att kontrol lera anslutnings problem. Normalt kan det finnas vissa brand Väggs inställningar som orsakar felen.
    
## <a name="diskuploadtimedout"></a>DiskUploadTimedOut

**Fel-ID:** 1011

**Fel meddelande:** Data överföringen för disk DiskPath, DiskId för den virtuella datorn VMName; VMId slutfördes inte inom den förväntade tiden.

Det här felet tyder vanligt vis på att Azure Migrate-installationen som utför replikeringen inte kan ansluta till Azure-Cloud Services, eller att replikeringen fortskrider långsamt vilket gör att replikeringen tar slut.

Möjliga orsaker är:

- Azure Migrates apparaten är avstängd.
- Tjänsten Replication Gateway är inte igång.
- Tjänsten Replication Gateway har problem med anslutningen till någon av följande Azure-tjänst komponenter som används för replikering: Service Bus/Event Hub/Azure cache Storage-konto/Azure Key Vault.
- Gateway-tjänsten begränsas på vCenter-nivå vid försök att läsa disken.

**Identifiera rotor saken och lösa problemet:**

1. Kontrollera att Azure Migrate-installationen är igång.
2. Kontrol lera om Gateway-tjänsten körs på enheten:
   1.  Logga in på Azure Migrate-enheten med hjälp av fjärr skrivbord och gör följande.

   2.  Öppna MMC-snapin-modulen för Microsoft-tjänster (kör > Services. msc) och kontrol lera om "Microsoft Azure Gateway-tjänsten körs. Starta tjänsten om tjänsten är stoppad eller inte körs. Du kan också öppna kommando tolken eller PowerShell och göra följande: "net start asrgwy".


3. **Sök efter anslutnings problem mellan Azure Migrate-och cache Storage-konto:** 

    Kör följande kommando när du har laddat ned AzCopy i Azure Migrate-installationen:
    
    _AzCopy-bänk https://[konto]. blob. Core. Windows. net/[container]? SÄKERHETS_
    
    **Steg för att köra prestandatest testet:**
    
      1. [Ladda ned](../storage/common/storage-use-azcopy-v10.md) AzCopy
        
      2. Sök efter enhetens lagrings konto i resurs gruppen. Lagrings kontot har ett namn som liknar migratelsa \* \* \* \* \* \* \* \* \* \* . Detta är värdet för parametern [Account] i kommandot ovan.
        
      3. Sök efter ditt lagrings konto i Azure Portal. Se till att den prenumeration du använder för att söka är samma prenumeration (mål prenumerationen) som lagrings kontot skapas i. Gå till behållare i avsnittet BLOB service. Klicka på + container och skapa en behållare. Lämna den offentliga åtkomst nivån till standardvärdet som valts.
        
      4. Gå till signaturen för delad åtkomst under Inställningar. Välj behållare i "tillåten resurs typ". Klicka på Skapa SAS och anslutnings sträng. Kopiera SAS-värdet.
        
      5. Kör kommandot ovan i kommando tolken genom att ersätta konto, behållare, SAS med värdena som hämtades i steg 2, 3 och 4.
        
      Du kan också [hämta](https://go.microsoft.com/fwlink/?linkid=2138967) Azure Storage utforska på installationen och försöka överföra 10 blobbar på ~ 64 MB till lagrings kontona. Om det inte finns något problem bör överföringen lyckas.
        
    **Lösning:** Om testet Miss lyckas finns det&#39;ett nätverks problem. Delta i det lokala nätverkets team för att kontrol lera anslutnings problem. Normalt kan det finnas vissa brand Väggs inställningar som orsakar felen.
                
4.  **Anslutnings problem mellan Azure Migrate-utrustning och Azure Service Bus:**

    Det här testet kontrollerar om Azure Migrate-enheten kan kommunicera med Azure Migrate moln tjänstens Server del. Enheten kommunicerar med tjänstens Server del via Service Bus-och Event Hub-meddelande köer. Om du vill verifiera anslutningen från installationen till Service Bus [laddar du ned](https://go.microsoft.com/fwlink/?linkid=2139104) Service Bus Explorer, försöker ansluta till enheten Service Bus och utför skicka meddelande/ta emot meddelande. Om det inte finns något problem bör det lyckas.

    **Steg för att köra testet:**
    
    1. Kopiera anslutnings strängen från den Service Bus som har skapats i resurs gruppen som motsvarar Azure Migrate projekt
    
    1. Öppna Service Bus Explorer
    
    1. Gå till fil > Anslut
    
    1. Klistra in anslutnings strängen som du kopierade i steg 1 och klicka på Anslut
    
    1. Detta öppnar Service Bus namn området.
    
    1. Välj Snapshot Manager i namn området. Högerklicka på Snapshot Manager, välj "ta emot meddelanden" > Välj "Peek" och klicka på OK.
    
    Om anslutningen lyckas visas "[x] mottagna meddelanden" i konsolens utdata. Om anslutningen Miss lyckas visas ett meddelande om att anslutningen misslyckades.
    
    **Lösning:** Om det här testet Miss lyckas uppstår ett anslutnings problem mellan Azure Migrate-enheten och Service Bus. Delta i det lokala nätverkets team för att kontrol lera anslutnings problemen. Normalt kan det finnas vissa brand Väggs inställningar som orsakar felen.
    
 5. **Anslutnings problem mellan Azure Migrate-utrustning och Azure Key Vault:**

    Det här testet kontrollerar anslutnings problem mellan Azure Migrate-enheten och Azure Key Vault. Key Vault används för att hantera åtkomst till lagrings kontot som används för replikering.
    
    **Steg för att kontrol lera anslutningen:**
    
    1. Hämta Key Vault-URI i listan över resurser i resurs gruppen som motsvarar Azure Migrate Project.
    
    1. Öppna PowerShell i Azure Migrate-installationen och kör följande kommando:
    
    _test-NetConnection Key Vault URI-P 443_
    
    Det här kommandot försöker upprätta en TCP-anslutning och returnerar utdata.
    
    1. I utdata kontrollerar du fältet "_TcpTestSucceeded_". Om värdet är "_Sant_" uppstår inga anslutnings problem mellan Azure Migrate-installationen och Azure Key Vault. Om värdet är "false", finns det ett anslutnings problem.
    
    **Lösning:** Om det här testet Miss lyckas uppstår ett anslutnings problem mellan Azure Migrate-apparaten och Azure Key Vault. Delta i det lokala nätverkets team för att kontrol lera anslutnings problem. Normalt kan det finnas vissa brand Väggs inställningar som orsakar felen.
    
## <a name="encountered-an-error-while-trying-to-fetch-changed-blocks"></a>Ett fel påträffades vid försök att hämta ändrade block

Fel meddelande: "påträffade ett fel vid försök att hämta ändrings block"

Metoden för metoden utan Agent använder VMware: s ändrade block tracking Technology (KANALBINDNINGSTOKEN) för att replikera data till Azure. KANALBINDNINGSTOKEN låter Migreringsverktyg spåra och replikera enbart de block som har ändrats sedan den senaste replikeringen. Det här felet uppstår om spårning av ändrade block för en virtuell dator som replikeras återställs eller om spårningsfilen för ändrade block är skadad.

Det här felet kan lösas på följande två sätt:

- Om du har valt "reparera replikering automatiskt" genom att välja "Ja" när du utlöste replikeringen av den virtuella datorn försöker verktyget reparera det åt dig. Högerklicka på den virtuella datorn och välj reparera replikering.
- Om du inte väljer reparera replikering automatiskt eller om steget ovan inte fungerade för dig, stoppar du replikeringen för den virtuella datorn, [återställer ändrad block spårning](https://go.microsoft.com/fwlink/?linkid=2139203) på den virtuella datorn och konfigurerar sedan om replikeringen.

Ett känt problem som kan orsaka en KANALBINDNINGSTOKEN återställning av en virtuell dator på VMware vSphere 5,5 beskrivs i [VMware KB 2048201: ändring av block spårning](https://go.microsoft.com/fwlink/?linkid=2138888) återställs efter en lagring vMotion-åtgärd i vSphere 5. x. Om du använder VMware vSphere 5.5 kontrollerar du att du har uppdateringarna som beskrivs i den här KB-artikeln.

Du kan också återställa VMware-spårning av ändrade block på en virtuell dator med VMware PowerCLI.

## <a name="an-internal-error-occurred"></a>Ett internt fel har uppstått

Ibland kan du träffa ett fel som inträffar på grund av problem i VMware-miljön/API: et. Vi har identifierat följande fel uppsättningar som fel i VMware-miljön. Felen har ett fast format.

_Fel meddelande: ett internt fel uppstod. [Fel meddelande]_

Exempel: fel meddelande: ett internt fel uppstod. [En ogiltig ögonblicks bilds konfiguration identifierades].

I följande avsnitt visas några av de vanligaste VMware-felen och hur du kan åtgärda dem.

### <a name="error-message-an-internal-error-occurred-server-refused-connection"></a>Fel meddelande: ett internt fel uppstod. [Servern nekade anslutning]

Problemet är ett känt VMware-problem och inträffar i VDDK 6,7. Du måste stoppa Gateway-tjänsten som körs i Azure Migrate-enheten, [Hämta en uppdatering från VMware KB](https://go.microsoft.com/fwlink/?linkid=2138889)och starta om Gateway-tjänsten.

Steg för att stoppa Gateway-tjänsten:

1. Tryck på Windows + R, öppna Services. msc. Klicka på "Microsoft Azure Gateway Service" och stoppa den.
2. Du kan också öppna kommando tolken eller PowerShell och göra följande: net stop asrgwy. Se till att vänta tills du får meddelandet att tjänsten inte längre körs.

Steg för att starta Gateway-tjänsten:

1. Tryck på Windows + R, öppna Services. msc. Högerklicka på "Microsoft Azure Gateway Service" och starta den.
2. Du kan också öppna kommando tolken eller PowerShell och göra följande: net start asrgwy.

### <a name="error-message-an-internal-error-occurred-an-invalid-snapshot-configuration-was-detected"></a>Fel meddelande: ett internt fel uppstod. ["En ogiltig ögonblicks bilds konfiguration identifierades."]

Om du har en virtuell dator med flera diskar kan du stöta på det här felet om du tar bort en disk från den virtuella datorn. Se stegen i [den här VMware-artikeln](https://go.microsoft.com/fwlink/?linkid=2138890)för att åtgärda problemet.

### <a name="error-message-an-internal-error-occurred-generate-snapshot-hung"></a>Fel meddelande: ett internt fel uppstod. [Generera ögonblicks bild låst]

Det här problemet uppstår när ögonblicks bilds genereringen slutar svara. När det här problemet uppstår kan du se Skapa ögonblicks bilds aktivitet på 95% eller 99%. Se denna [VMware KB](https://go.microsoft.com/fwlink/?linkid=2138969) för att lösa problemet.

### <a name="error-message-an-internal-error-occurred-failed-to-consolidate-the-disks-on-vm-_reasons_"></a>Fel meddelande: ett internt fel uppstod. [Det gick inte att konsolidera diskarna på den virtuella datorn _[orsaker]_]

När vi konsoliderar diskar i slutet av en replikeringscykel, Miss lyckas åtgärden. Följ anvisningarna i [VMware KB](https://go.microsoft.com/fwlink/?linkid=2138970) genom att välja lämplig _orsak_ för att lösa problemet.

Följande fel inträffar när åtgärder för att skapa, ta bort eller konsolidera diskar fungerar inte i VMware-ögonblicksbilder. Följ anvisningarna i nästa avsnitt för att åtgärda felen:

### <a name="error-message-an-internal-error-occurred-another-task-is-already-in-progress"></a>Fel meddelande: ett internt fel uppstod. [En annan aktivitet pågår redan]

Det här problemet uppstår när det finns motstridiga aktiviteter i virtuella datorer som körs i bakgrunden eller när en aktivitet inom vCenter Server timeout. Följ den lösning som anges i följande [VMware KB](https://go.microsoft.com/fwlink/?linkid=2138891).

### <a name="error-message-an-internal-error-occurred-operation-not-allowed-in-current-state"></a>Fel meddelande: ett internt fel uppstod. [Åtgärden tillåts inte i det aktuella läget]

Det här problemet uppstår när vCenter Server hanterings agenter slutar fungera. Lös problemet genom att läsa lösningen i följande [VMware KB](https://go.microsoft.com/fwlink/?linkid=2138971).

### <a name="error-message-an-internal-error-occurred-snapshot-disk-size-invalid"></a>Fel meddelande: ett internt fel uppstod. [Storlek på ögonblicks bild disk ogiltig]

Detta är ett känt VMware-problem där disk storleken som anges av Snapshot blir noll. Följ den upplösning som angavs i [VMware KB](https://go.microsoft.com/fwlink/?linkid=2138972).

### <a name="error-message-an-internal-error-occurred-memory-allocation-failed-out-of-memory"></a>Fel meddelande: ett internt fel uppstod. [Det gick inte att allokera minne. Slut på minne.]

Detta inträffar när NFC-värdstyrenheten har slut på minne. För att lösa det här problemet måste du flytta den virtuella datorn (Compute vMotion) till en annan värd, som har kostnads fria resurser.

## <a name="replication-cycle-failed"></a>Det gick inte att utföra replikering

**Fel-ID:** 181008

**Fel meddelande:** Virtuell dator: ' VMName '. Fel: det gick inte att hitta någon disksnapshots för ögonblicks bild replikering med ögonblicks bild-ID: SnapshotID.

**Möjliga orsaker:**

Möjliga orsaker är:
1. Sökvägen till en eller flera av de inkluderade diskarna har ändrats på grund av Storage VMotion.
2. En eller flera av de inkluderade diskarna är inte längre anslutna till den virtuella datorn.
      
**Rekommenderade**

Följande rekommendationer ges
1. Återställ de inkluderade diskarna till den ursprungliga sökvägen med Storage vMotion och inaktivera sedan Storage vMotion.
2. Inaktivera Storage VMotion, om det är aktiverat, stoppa replikering på den virtuella datorn och replikera den virtuella datorn igen. Kontakta supporten om problemet kvarstår.

## <a name="next-steps"></a>Nästa steg

Fortsätt VM-replikering och utför [testmigrering](./tutorial-migrate-vmware.md#run-a-test-migration).
