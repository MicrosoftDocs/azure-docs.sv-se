---
title: Kör Micro Focus Enterprise Server 5,0 i en Docker-behållare på Azure | Microsoft Docs
description: I den här artikeln lär du dig att köra Micro Focus Enterprise Server 5,0 i en Docker-behållare på Microsoft Azure.
services: virtual-machines
documentationcenter: ''
author: maggsl
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 06/29/2020
tags: ''
keywords: ''
ms.service: multiple
ms.openlocfilehash: 5c436eae53b16c980e9725cfef0573367d144842
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102548384"
---
# <a name="run-micro-focus-enterprise-server-50-in-a-docker-container-on-azure"></a>Kör Micro Focus Enterprise Server 5,0 i en Docker-behållare på Azure

Du kan köra Micro Focus Enterprise Server 5,0 i en Docker-behållare på Azure. Den här själv studie kursen visar hur du gör. Den använder Windows-CICS (Customer information Control system) acctdemo demonstration for Enterprise Server.

Docker lägger till portabilitet och isolering för program. Du kan till exempel exportera en Docker-avbildning från en virtuell Windows-dator (VM) så att den körs på en annan eller från en lagrings plats till en Windows-Server med Docker. Docker-avbildningen körs på den nya platsen med samma konfiguration – utan att du behöver installera Enterprise Server. Den är en del av bilden. Licens överväganden gäller fortfarande.

Den här självstudien installerar **Windows 2016-datacenter med behållare** VM från Azure Marketplace. Den här virtuella datorn innehåller **Docker 18.09.0**. Stegen nedan visar hur du distribuerar behållaren, kör den och sedan ansluter till den med en 3270-emulator.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar bör du gå igenom följande krav:

-   En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

-   Micro Focus-programvaran och en giltig licens (eller prov licens). Om du är en befintlig Micro Focus-kund kontaktar du din Micro Focus-representant. Annars kan du [begära en utvärderings version](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

    > [!Note] 
    > Docker-demonstrations filerna ingår i Enterprise Server 5,0. I den här självstudien används överordnad \_ Server \_ dockerfiles \_ 5,0 \_windows.zip. Kom igång genom att komma åt den från samma plats som du har åtkomst till företags serverns installations fil eller till *Micro Focus* .

-   Dokumentationen för [Enterprise Server och Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/#%22).

## <a name="create-a-vm"></a>Skapa en virtuell dator

1.  Skydda mediet från överordnad \_ Server \_ dockerfiles \_ 5,0 \_windows.zip-fil. Säkra licens filen ES-Docker-Prod-XXXXXXXX. mflic (krävs för att bygga Docker-avbildningarna).

2.  Skapa den virtuella datorn. Det gör du genom att öppna Azure Portal, välja **skapa en resurs** på menyn längst upp till vänster och filtrera efter *Windows Server-operativsystem*. I resultaten väljer du **Windows Server.** På nästa skärm väljer du **Windows Server 2016 Data Center – med behållare**.

    ![Skärm bild av Azure Portal Sök Resultat](./media/run-image-1.png)

3.  Om du vill konfigurera egenskaperna för den virtuella datorn väljer du instans information:

    1.  Välj storlek på den virtuella datorn. I den här självstudien bör du överväga att använda en **standard virtuell DS2 \_ v3** -dator med 2 virtuella processorer och 16 GB minne.

    2.  Välj den **region** och **resurs grupp** som du vill distribuera till.

    3.  Använd standardinställningen för **tillgänglighets alternativ**.

    4.  För **användar namn** anger du det administratörs konto som du vill använda och lösen ordet.

    5.  Kontrol lera att **port 3389 RDP** är öppen. Endast den här porten måste vara offentligt exponerad, så du kan logga in på den virtuella datorn. Godkänn sedan alla standardvärden och klicka på **Granska + skapa**.

    ![Skärm bild av fönstret Skapa en virtuell dator](./media/run-image-2.png)

4.  Vänta tills distributionen har slutförts (ett par minuter). Ett meddelande anger att den virtuella datorn har skapats.

5.  Välj **gå till resurs** för att gå till **översikts** BLADet för den virtuella datorn.

6.  Välj **Anslut** till höger. Alternativen **Anslut till virtuell dator** visas till höger.

7.  Välj knappen **Ladda ned RDP-fil** för att ladda ned RDP-filen (Remote Desktop Protocol) som gör att du kan ansluta till den virtuella datorn.

8.  När filen har hämtats öppnar du den och anger det användar namn och lösen ord som du skapade för den virtuella datorn.

    > [!Note]    
    > Använd inte företagets autentiseringsuppgifter för att logga in. (RDP-klienten förutsätter att du kanske vill använda dessa. Du inte gör det.)

9.  Välj **fler alternativ** och välj sedan dina autentiseringsuppgifter för virtuella datorer.

Den virtuella datorn körs i det här läget via RDP. Du är inloggad och redo för nästa steg.

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>Skapa en sandbox-katalog och ladda upp zip-filen

1.  Skapa en katalog på den virtuella datorn där du kan ladda upp demonstrationen och licensens filer. Till exempel **C: \\ sandbox**.

2.  Överför överordnad **\_ server \_ dockerfiles \_ 5,0 \_windows.zip** och **es-Docker-Prod-xxxxxxxx. mflic-** filen till den katalog som du skapade.

3.  Extrahera innehållet i zip-filen till den överordnande **\_ servern \_ dockerfiles \_ 5,0 \_ Windows** -katalogen som skapats av extraherings processen. Den här katalogen innehåller en README-fil (som HTML-och. txt-fil) och två under kataloger, **EnterpriseServer** och **exempel**.

4.  Kopiera **es – Docker-Prod-xxxxxxxx. mflic** till mappen C: Sandbox-överordnad \\ \\ \_ Server \_ dockerfiles \_ 5,0 \_ Windows \\ EnterpriseServer och C: \\ sandbox- \\ \_ Server \_ dockerfiles \_ 5,0 \_ Windows- \\ exempel \\ CICS kataloger.  
      
    > [!Note]
    > Se till att du kopierar licens filen till båda katalogerna. De krävs för Docker build-steget för att se till att avbildningarna är korrekt licensierade.

## <a name="check-docker-version-and-create-base-image"></a>Kontrol lera Docker-versionen och skapa en bas avbildning

> [!Important]  
> Att skapa rätt Docker-avbildning är en två stegs process. Skapa först Enterprise Server 5,0-bas avbildningen. Skapa sedan en annan avbildning för x64-plattformen. Även om du kan skapa en x86-avbildning (32-bitars) använder du 64-bitars avbildningen.

1.  Öppna en kommandotolk.

2.  Kontrol lera att Docker är installerat. Skriv följande i kommando tolken: **Docker version**  
    Till exempel var versionen 18.09.0 när den skrevs.

3.  Om du vill ändra katalogen skriver du:  
    **CD \\ Sandbox-överordnad \\ \_ server \_ dockerfiles \_ 5,0 \_ Windows- \\ EnterpriseServer**.

4.  Skriv **bld.bat IacceptEULA** för att börja bygga processen för den inledande bas avbildningen. Vänta några minuter för den här processen att köras. Observera i resultaten de två avbildningar som har skapats – en för x64 och en för x86:

    ![Fönstret Kommando visar bilder](./media/run-image-3.png)

5.  Om du vill skapa den slutliga avbildningen för CICS-demonstrationen växlar du till CICS-katalogen genom att skriva **CD \\ sandbox \\ \_ Server \_ dockerfiles \_ 5,0 \_ Windows- \\ exempel \\ CICS**.

6.  Skapa avbildningen genom att skriva **bld.bat x64**. Vänta några minuter på att processen ska köras och meddelandet som anger att avbildningen har skapats.

7.  Skriv **Docker-avbildningar** för att visa en lista över alla Docker-avbildningar som är installerade på den virtuella datorn. Se till att **mikrofocus/es-acctdemo** är en av dem.

    ![Fönstret Kommando som visar es-acctdemo-avbildning](./media/run-image-4.png)

## <a name="run-the-image"></a>Kör avbildningen

1.  Starta Enterprise Server 5,0 och acctdemo-programmet genom att skriva följande i kommando tolken:

    ```
    **docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win\_5.0\_x64
    ```

1.  Installera en 3270-termin Ale mula Tor till exempel [x3270](http://x3270.bgp.nu/) och Använd den för att ansluta via port 9040 till den avbildning som körs.

2.  Hämta IP-adressen för acctdemo-behållaren så att Docker kan fungera som en Dynamic Host Configuration Protocol (DHCP)-Server för de behållare som den hanterar:

    1.  Hämta ID: t för den behållare som körs. Skriv **Docker PS** i kommando tolken och anteckna ID (**22a0fe3159d0** i det här exemplet). Spara den för nästa steg.

    2.  Om du vill hämta IP-adressen för acctdemo-behållaren använder du behållar-ID: t från föregående steg enligt följande:

    ```
    docker inspect \<containerID\> --format="{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

    Exempel:

    ```
    docker inspect 22a0fe3159d0 --format="{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

4. Anteckna IP-adressen för acctdemo-avbildningen. Till exempel är adressen i följande utdata 172.19.202.52.

    ![Skärm bild av Fönstret Kommando visar IP-adress](./media/run-image-5.png)

5. Montera avbildningen med emulatorn. Konfigurera emulatorn för att använda adressen för acctdemo-avbildningen och port 9040. Här är det **172.19.202.52:9040**. Ditt är liknande. Skärmen **Logga** in på CICS öppnas.

    ![Skärm bild av inloggning till CICS](./media/run-image-6.png)

6. Logga in i CICS-regionen genom att ange **SYSAD** för **användar-ID** och **SYSAD** för **lösen ordet**.

7. Ta bort skärmen med emulatorns Keymap. För x3270 väljer du meny alternativet **Keymap** .

8. Starta acctdemo-programmet genom att skriva **acct**. Den första skärmen för programmet visas.

     ![Skärm bild visar ett konsol fönster som visar programmet.](./media/run-image-7.png)

9. Experimentera med konto typer för visning. Skriv till exempel **D** för begäran och **11111** för **kontot**. Andra konto nummer att testa är 22222, 33333 och så vidare.

    ![Skärm bild som visar redigering av olika värden i programmet.](./media/run-image-8.png)

10. Om du vill visa administrations konsolen för företags servern går du till kommando tolken och skriver **Start http: 172.19.202.52:86**.

    ![Administrations konsol för Enterprise Server](media/run-image-9.png)

Klart! Nu kör vi och hanterar ett CICS-program i en Docker-behållare.

## <a name="next-steps"></a>Nästa steg

-   [Installera Micro Focus Enterprise Server 5,0 och Enterprise Developer 5,0 på Azure](./set-up-micro-focus-azure.md)

-   [Migrering av program från stordatorer](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
