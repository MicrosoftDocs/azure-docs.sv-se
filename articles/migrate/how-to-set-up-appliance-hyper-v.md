---
title: Konfigurera en Azure Migrate-apparat för Hyper-V
description: Lär dig hur du konfigurerar en Azure Migrate-apparat för att utvärdera och migrera virtuella Hyper-V-datorer.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 3a7486ce94b335f835a88b7f357c9c719f86d667
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704766"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Konfigurera en installation för virtuella Hyper-V-datorer

Följ den här artikeln för att konfigurera Azure Migrate-installationen för utvärdering av virtuella Hyper-V-datorer med verktyget [Azure Migrate: Server bedömning](migrate-services-overview.md#azure-migrate-server-assessment-tool) .

[Azure Migrate](migrate-appliance.md) -installationen är en förenklad utrustning som används av Azure Migrate: Server bedömning/migrering för att identifiera lokala virtuella Hyper-V-datorer och skicka VM-metadata/prestanda data till Azure.

Du kan distribuera installationen på ett par olika sätt:

- Konfigurera på en virtuell Hyper-V-dator med en nedladdad virtuell hård disk. Detta är den metod som beskrivs i den här artikeln.
- Konfigurera på en virtuell Hyper-V-dator eller fysisk dator med ett PowerShell-skript. [Den här metoden](deploy-appliance-script.md) ska användas om du inte kan konfigurera en virtuell dator med en virtuell hård disk, eller om du befinner dig i Azure Government.

När du har skapat enheten kontrollerar du att den kan ansluta till Azure Migrate: Server utvärdering, konfigurera den för första gången och registrera den med Azure Migrate projektet.

## <a name="appliance-deployment-vhd"></a>Installation av utrustning (VHD)

Så här konfigurerar du installationen med en VHD-mall:

- Ange ett namn på apparaten och generera en Azure Migrate projekt nyckel i portalen.
- Ladda ned en komprimerad virtuell Hyper-V-hårddisk från Azure Portal.
- Skapa installationen och kontrol lera att den kan ansluta till Azure Migrate Server utvärdering.
- Konfigurera enheten för första gången och registrera den med det Azure Migrate projektet med hjälp av Azure Migrate projekt nyckeln.

### <a name="generate-the-azure-migrate-project-key"></a>Generera Azure Migrate projekt nyckel

1. I **Migreringsmål** > **Servrar** > **Azure Migrate: Serverutvärdering** väljer du **Identifiera**.
2. I **identifiera datorer**  >  **är dina datorer virtualiserade?** väljer du **Ja, med Hyper-V**.
3. I **1: generera Azure Migrate projekt nyckel** anger du ett namn för Azure Migrate-installationen som ska konfigureras för identifiering av virtuella Hyper-V-datorer. namnet måste vara alfanumeriskt med 14 tecken eller färre.
1. Klicka på **generera nyckel** för att starta skapandet av de nödvändiga Azure-resurserna. Stäng inte sidan identifiera datorer när du skapar resurser.
1. När Azure-resurserna har skapats skapas en **Azure Migrate projekt nyckel** .
1. Kopiera nyckeln på samma sätt som du behöver den för att slutföra registreringen av enheten under konfigurationen.

### <a name="download-the-vhd"></a>Ladda ned den virtuella hård disken

I **2: Ladda ned Azure Migrate-enheten** väljer du. VHD-fil och klicka på **Hämta**. 

   ![Val för identifiering av datorer](./media/tutorial-assess-hyper-v/servers-discover.png)


   ![Val för generera nyckel](./media/tutorial-assess-hyper-v/generate-key-hyperv.png)


### <a name="verify-security"></a>Verifiera säkerhet

Kontrol lera att den zippade filen är säker innan du distribuerar den.

1. Öppna ett kommandofönster för administratör på den dator som du laddade ned filen till.
2. Kör följande kommando för att generera hashen för den virtuella hård disken
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Exempel på användning: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v3.20.09.25.zip -Algorithm SHA256```





## <a name="create-the-appliance-vm"></a>Skapa VM-enheten

Importera den hämtade filen och skapa den virtuella datorn.

1. Extrahera den zippade VHD-filen till en mapp på Hyper-V-värden som ska vara värd för den virtuella dator enheten. Tre mappar extraheras.
2. Öppna Hyper-V Manager. Klicka på **Importera virtuell dator** i **åtgärder**.

    ![Distribuera virtuell hård disk](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. I guiden Importera virtuell dator > **innan du börjar klickar du** på **Nästa**.
3. I **hitta mapp** anger du den mapp som innehåller den extraherade virtuella hård disken. Klicka på **Nästa**.
1. I **Välj virtuell dator** klickar du på **Nästa**.
2. I **Välj import typ** klickar **du på Kopiera den virtuella datorn (skapa ett nytt unikt ID)**. Klicka på **Nästa**.
3. I **Välj mål** låter du standardvärdet vara kvar. Klicka på **Nästa**.
4. Lämna standardinställningen i **Storage Folders**. Klicka på **Nästa**.
5. I **Välj nätverk** anger du den virtuella växel som den virtuella datorn ska använda. Växeln behöver Internet anslutning för att skicka data till Azure.
6. Granska inställningarna i **Sammanfattning**. Klicka sedan på **Slutför**.
7. Starta den virtuella datorn i Hyper-V Manager > **Virtual Machines**.


### <a name="verify-appliance-access-to-azure"></a>Verifiera åtkomst till enheten till Azure

Se till att den virtuella datorns virtuella datorer kan ansluta till Azure-URL: er för [offentliga](migrate-appliance.md#public-cloud-urls) och [offentliga](migrate-appliance.md#government-cloud-urls) moln.

### <a name="configure-the-appliance"></a>Konfigurera installationen

Konfigurera enheten för första gången.

> [!NOTE]
> Om du ställer in installationen med hjälp av ett [PowerShell-skript](deploy-appliance-script.md) i stället för den hämtade virtuella hård disken är de två första stegen i den här proceduren inte relevanta.

1. I Hyper-V Manager > **Virtual Machines** högerklickar du på den virtuella datorn > **Anslut**.
2. Ange språk, tidszon och lösen ord för enheten.
3. Öppna en webbläsare på vilken dator som helst som kan ansluta till den virtuella datorn och öppna URL: en för installations programmets webbapp: **https://-enhetens *namn eller IP-adress*: 44368**.

   Alternativt kan du öppna appen från apparatens skriv bord genom att klicka på genvägen till appen.
1. Godkänn **licens villkoren** och Läs informationen från tredje part.
1. I webbappen > **Konfigurera krav** gör du följande:
    - **Anslutning**: appen kontrollerar att den virtuella datorn har Internet åtkomst. Om den virtuella datorn använder en proxyserver:
      - Klicka på **Konfigurera proxy** till och ange proxyadress (i formuläret http://ProxyIPAddress eller http://ProxyFQDN) lyssnande port.
      - Ange autentiseringsuppgifter om proxyn kräver autentisering.
      - Endast HTTP-proxy stöds.
      - Om du har lagt till proxyinformation eller inaktiverat proxyn och/eller autentiseringen, klickar du på **Spara** för att utlösa anslutnings kontrollen igen.
    - **Tidssynkronisering**: tiden har verifierats. Tiden för installationen bör vara synkroniserad med Internet-tid för att VM-identifieringen ska fungera korrekt.
    - **Installera uppdateringar**: Azure Migrate Server Assessment kontrollerar att installations programmet har de senaste uppdateringarna installerade. När kontrollen är klar kan du klicka på **Visa apparat-tjänster** för att se status och versioner för komponenterna som körs på produkten.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrera enheten med Azure Migrate

1. Klistra in **Azure Migrate projekt nyckeln** som har kopierats från portalen. Om du inte har nyckeln går du till **Server utvärdering> identifiera> hantera befintliga apparater**, väljer det installations namn som du angav vid tidpunkten för att generera nyckeln och kopierar motsvarande nyckel.
1. Du behöver en enhets kod för att autentisera med Azure. När du klickar på **Logga in** öppnas en modal enhets kod som visas nedan.

    ![Modal visar enhets koden](./media/tutorial-discover-vmware/device-code.png)

1. Klicka på **Kopiera kod & inloggning** för att kopiera enhets koden och öppna en Azure-inloggnings tolk i en ny webbläsare-flik. Om den inte visas kontrollerar du att du har inaktiverat blockering av popup-fönster i webbläsaren.
1. På fliken nytt klistrar du in enhets koden och loggar in med ditt användar namn och lösen ord för Azure.
   
   Inloggning med en PIN-kod stöds inte.
3. Om du stänger fliken inloggning oavsiktligt utan att logga in, måste du uppdatera fliken webbläsare i Konfigurations hanteraren för att aktivera inloggnings knappen igen.
1. När du har loggat in går du tillbaka till föregående flik med installationen av Konfigurations hanteraren.
4. Om Azure-användarkontot som används för loggning har rätt [behörigheter](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account) för de Azure-resurser som skapades under den här nyckeln, initieras registrerings enheten.
1. När installationen av enheten har registrerats kan du se registrerings informationen genom att klicka på **Visa information**.



### <a name="delegate-credentials-for-smb-vhds"></a>Delegera autentiseringsuppgifter för SMB-VHD: er

Om du kör virtuella hård diskar på SMB: er måste du aktivera delegering av autentiseringsuppgifter från installationen till Hyper-V-värdarna. Så här gör du detta från enheten:

1. Kör det här kommandot på den virtuella datorn. HyperVHost1/HyperVHost2 är exempel värd namn.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
    ```

2. Du kan också göra detta i redigerare för lokalt grupprincipobjekt på enheten:
    - I dator konfiguration för **lokal dator princip**  >  klickar du på **administrativa mallar**  >    >  **delegering av systemautentiseringsuppgifter**.
    - Dubbelklicka på **Tillåt att delegera nya autentiseringsuppgifter** och välj **aktive rad**.
    - I **alternativ** klickar du på **Visa** och lägger till varje Hyper-V-värd som du vill identifiera i listan med **WSMan/** som prefix.
    - I  **delegering av autentiseringsuppgifter** dubbelklickar du på **Tillåt att delegera nya AUTENTISERINGSUPPGIFTER med endast NTLM-** serverautentisering. Lägg återigen till varje Hyper-V-värd som du vill identifiera i listan, med **WSMan/** som prefix.

## <a name="start-continuous-discovery"></a>Starta kontinuerlig identifiering

Anslut från enheten till Hyper-V-värdar eller-kluster och starta VM-identifiering.

1. I **steg 1: ange autentiseringsuppgifter för Hyper-v-värden**, klickar du på **Lägg till autentiseringsuppgifter** för att ange ett eget namn för autentiseringsuppgifter, lägga till **användar namn** och **lösen ord** för en Hyper-v-värd/-kluster som installationen ska använda för att identifiera virtuella datorer. Klicka på **Spara**.
1. Om du vill lägga till flera autentiseringsuppgifter samtidigt klickar du på **Lägg till fler** för att spara och lägga till fler autentiseringsuppgifter. Flera autentiseringsuppgifter stöds för identifiering av virtuella Hyper-V-datorer.
1. I **steg 2: Ange information om Hyper-v-värdar/-kluster** klickar du på **Lägg till identifierings källa** för att ange **IP-adress/FQDN** för Hyper-v-värd/kluster och det egna namnet för autentiseringsuppgifter för att ansluta till värden/klustret.
1. Du kan antingen **lägga till ett enskilt objekt** i taget eller **lägga till flera objekt** i taget. Det finns också ett alternativ för att tillhandahålla information om Hyper-V-värdar/-kluster via **import-CSV**.

    ![Val för att lägga till identifierings källa](./media/tutorial-assess-hyper-v/add-discovery-source-hyperv.png)

    - Om du väljer **Lägg till enstaka objekt** måste du ange ett eget namn för autentiseringsuppgifter och Hyper-V-värd/kluster **-IP-adress/FQDN** och klicka på **Spara**.
    - Om du väljer **Lägg till flera objekt** _(markerat som standard)_ kan du lägga till flera poster samtidigt genom att ange **IP-adress/FQDN** för Hyper-V-värd/-kluster med det egna namnet för autentiseringsuppgifter i text rutan. **Verifiera** de tillagda posterna och klicka på **Spara**.
    - Om du väljer **importera CSV** kan du ladda ned en CSV-mallfil, fylla i filen med Hyper-V-värd-/kluster **-IP-adress/FQDN** och eget namn för autentiseringsuppgifter. Sedan kan du importera filen till enheten, **Verifiera** posterna i filen och klicka på **Spara**.

1. När du klickar på Spara kommer installations programmet att försöka verifiera anslutningen till Hyper-V-värdarna/-klustren som lagts till och visa **verifierings status** i tabellen mot varje värd/kluster.
    - För verifierade värdar/kluster kan du Visa mer information genom att klicka på deras IP-adress/FQDN.
    - Om verifieringen Miss lyckas för en värd granskar du felet genom att klicka på **verifiering misslyckades** i kolumnen Status i tabellen. Åtgärda problemet och verifiera igen.
    - Klicka på **ta bort** om du vill ta bort värdar eller kluster.
    - Du kan inte ta bort en speciell värd från ett kluster. Du kan bara ta bort hela klustret.
    - Du kan lägga till ett kluster även om det finns problem med specifika värdar i klustret.
1. Du kan **Verifiera** anslutningen till värdarna/klustren varje gång innan du påbörjar identifieringen.
1. Klicka på **Starta identifiering** för att starta identifiering av virtuell dator från verifierade värdar/kluster. När identifieringen har startats kan du kontrol lera identifierings statusen mot varje värd/kluster i tabellen.

Detta startar identifieringen. Det tar ungefär 2 minuter per värd för metadata för identifierade servrar som visas i Azure Portal.

## <a name="verify-vms-in-the-portal"></a>Verifiera virtuella datorer i portalen

När identifieringen är klar kan du kontrol lera att de virtuella datorerna visas i portalen.

1. Öppna instrumentpanelen för Azure Migrate.
2. På sidan **Azure Migrate-servrar**  >  **Azure Migrate: Server utvärdering** klickar du på ikonen som visar antalet för **identifierade servrar**.


## <a name="next-steps"></a>Nästa steg

Testa [Hyper-V-utvärdering](tutorial-assess-hyper-v.md) med Azure Migrate Server-utvärdering.