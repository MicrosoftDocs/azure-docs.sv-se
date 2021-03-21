---
title: Aktivera och konfigurera den lokala hanteringskonsolen
description: Genom att aktivera hanterings konsolen ser du till att sensorer registreras med Azure och skickar information till den lokala hanterings konsolen och att den lokala hanterings konsolen utför hanterings aktiviteter på anslutna sensorer.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 3/18/2021
ms.topic: how-to
ms.service: defender-for-iot
ms.openlocfilehash: f659b25abbbad5f2150ed44766d30dc1f64dd3bb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104602764"
---
# <a name="activate-and-set-up-your-on-premises-management-console"></a>Aktivera och konfigurera den lokala hanteringskonsolen 

Aktivering och installation av den lokala hanterings konsolen säkerställer att:

- Nätverks enheter som du övervakar via anslutna sensorer registreras med ett Azure-konto.

- Sensorer skickar information till den lokala hanterings konsolen.

- Den lokala hanterings konsolen utför hanterings uppgifter på anslutna sensorer.

- Du har installerat ett SSL-certifikat.

## <a name="sign-in-for-the-first-time"></a>Logga in första gången

Logga in i hanterings konsolen:

1. Gå till den IP-adress som du fick för den lokala hanterings konsolen under systeminstallationen.
 
1. Ange det användar namn och lösen ord som du fick för den lokala hanterings konsolen under systeminstallationen. 


Om du har glömt ditt lösen ord väljer du alternativet för att **återställa lösen ord**  och se [lösen ords återställning](how-to-manage-the-on-premises-management-console.md#password-recovery) för instruktioner om hur du återställer ditt lösen ord.

## <a name="get-and-upload-an-activation-file"></a>Hämta och ladda upp en aktiverings fil

När du har loggat in för första gången måste du aktivera den lokala hanterings konsolen genom att hämta och ladda upp en aktiverings fil. 

Så här hämtar du en aktiverings fil:

1. Gå till sidan **prissättning** i Azure Defender för IoT-portalen. 
1. Välj den prenumeration som den lokala hanterings konsolen ska kopplas till.
1. Välj fliken **Ladda ned aktiverings filen för hanterings konsolen** . Aktiverings filen har laddats ned.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="Hämta aktiverings filen.":::

Ladda upp en aktiverings fil:

1. Gå till sidan **system inställningar** i den lokala hanterings konsolen.
1. Välj **aktiverings** ikonen :::image type="icon" source="media/how-to-manage-sensors-from-the-on-premises-management-console/activation-icon.png" border="false"::: .
1. Välj **Välj en fil** och välj den fil som hämtades.

Efter den första aktiveringen kan antalet övervakade enheter överstiga antalet allokerade enheter som definierats under onboarding. Detta inträffar om du ansluter fler sensorer till hanterings konsolen. Om det finns en avvikelse mellan antalet övervakade enheter och antalet allokerade enheter visas en varning i hanterings konsolen. Om detta händer laddar du upp en ny aktiverings fil.

## <a name="set-up-a-certificate"></a>Konfigurera ett certifikat

Efter installationen av hanterings konsolen skapas ett lokalt självsignerat certifikat som används för att få åtkomst till-konsolen. När en administratör loggar in i hanterings konsolen för första gången uppmanas användaren att publicera ett SSL/TLS-certifikat. 

Det finns två säkerhets nivåer:

- Uppfylla särskilda certifikat-och krypterings krav som din organisation har begärt genom att ladda upp det CA-signerade certifikatet.
- Tillåt verifiering mellan hanterings konsolen och anslutna sensorer. Verifieringen utvärderas mot en lista över återkallade certifikat och certifikatets förfallo datum. *Om verifieringen Miss lyckas stoppas kommunikationen mellan hanterings konsolen och sensorn och ett verifierings fel visas i-konsolen.* Det här alternativet är aktiverat som standard efter installationen.  

Konsolen stöder följande typer av certifikat:

- Privata och företags nyckel infrastruktur (privat PKI)

- Infrastruktur för offentliga nycklar (offentlig PKI)

- Lokalt genererat på produkten (lokalt självsignerat) 

  > [!IMPORTANT]
  > Vi rekommenderar att du inte använder ett självsignerat certifikat. Certifikatet är inte säkert och bör endast användas för test miljöer. Ägaren av certifikatet kan inte verifieras och säkerheten i systemet kan inte upprätthållas. Använd aldrig det här alternativet för produktions nätverk.

Ladda upp ett certifikat:

1. När du tillfrågas efter inloggningen definierar du ett certifikat namn.
1. Ladda upp CRT-och nyckelfil-filerna.
1. Ange en lösen fras och ladda upp en PEM-fil om det behövs.

Du kan behöva uppdatera din skärm när du har laddat upp det CA-signerade certifikatet.

Så här inaktiverar du verifiering mellan hanterings konsolen och anslutna sensorer:

1. Välj **Nästa**.
1. Inaktivera **Aktivera systemomfattande validerings** växling.

Information om hur du laddar upp ett nytt certifikat, certifikatfiler som stöds och relaterade objekt finns i [hantera den lokala hanterings konsolen](how-to-manage-the-on-premises-management-console.md).

## <a name="connect-sensors-to-the-on-premises-management-console"></a>Ansluta sensorer till den lokala hanterings konsolen

Du måste se till att sensorer skickar information till den lokala hanterings konsolen och att den lokala hanterings konsolen kan utföra säkerhets kopieringar, hantera aviseringar och utföra annan aktivitet på sensorer. Använd följande procedurer för att kontrol lera att du skapar en första anslutning mellan sensorer och den lokala hanterings konsolen.

Det finns två alternativ för att ansluta Azure Defender för IoT-sensorer till den lokala hanterings konsolen:

- Anslut från sensor konsolen

- Anslut med hjälp av tunnlar

När du har anslutit måste du konfigurera en plats med dessa sensorer.

### <a name="connect-sensors-from-the-sensor-console"></a>Ansluta sensorer från sensor konsolen

Så här ansluter du vissa sensorer till den lokala hanterings konsolen från sensor konsolen:

1. I den vänstra rutan i sensor konsolen väljer du **Systeminställningar**.

2. Välj **anslutning till hantering**.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/connection-status-window-not-connected.png" alt-text="Skärm bild av status fönstret för en lokal hanterings konsol som visar frånkopplade.":::

3. I text rutan **adress** anger du IP-adressen för den lokala hanterings konsol som du vill ansluta till.

4. Välj **Anslut**. Status ändras:

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/connection-status-window-connected.png" alt-text="Skärm bild av status fönstret för en lokal hanterings konsol som visar anslutna.":::

### <a name="connect-sensors-by-using-tunneling"></a>Ansluta sensorer genom att använda tunnlar

Aktivera en säker tunnel anslutning mellan organisations sensorer och den lokala hanterings konsolen. Den här inställningen kringgår interaktionen med organisationens brand vägg och minskar risken för angrepp.

Med hjälp av tunnlar kan du ansluta till den lokala hanterings konsolen från dess IP-adress och en enskild port (9000) till en sensor.

Så här konfigurerar du tunnlar i den lokala hanterings konsolen:

- Logga in på den lokala hanterings konsolen och kör följande kommandon:

  ```bash
  cyberx-management-tunnel-enable
  service apache2 reload
  sudo cyberx-management-tunnel-add-xsense --xsenseuid <sensorIPAddress> --xsenseport 9000
  service apache2 reload
  ```

Så här konfigurerar du tunnel trafik på sensorn:

1. Öppna TCP-port 9000 på sensorn (Network. Properties) manuellt. Om porten inte är öppen avvisar sensorn anslutningen från den lokala hanterings konsolen.

2. Logga in på varje sensor och kör följande kommandon:

   ```bash
   sudo cyberx-xsense-management-connect -ip <centralmanagerIPAddress>
   sudo cyberx-xsense-management-tunnel
   sudo vi /var/cyberx/properties/network.properties
   opened_tcp_incoming_ports=22,80,443,102,9000
   sudo cyberx-xsense-network-validation
   sudo /etc/network/if-up.d/iptables-recover
   sudo iptables -nvL
   ```

## <a name="set-up-a-site"></a>Konfigurera en plats

Standard företags kartan ger en övergripande vy över dina enheter i enlighet med flera olika nivåer av geografiska platser.

Du kan behöva visa dina enheter där organisationens struktur och användar behörigheter är komplexa. I dessa fall kan plats installationen bestämmas av en global organisations struktur, förutom standard platsen eller zon strukturen.

För att stödja den här miljön måste du skapa en global affärstopologi som baseras på organisationens affär senheter, regioner, platser och zoner. Du måste också definiera användar åtkomst behörigheter runt dessa entiteter med hjälp av åtkomst grupper.

Med åtkomst grupper får du bättre kontroll över var användarna hanterar och analyserar enheter i-plattformen Defender för IoT.

### <a name="how-it-works"></a>Så här fungerar det

För varje plats kan du definiera en affär senhet och en region. Sedan kan du lägga till zoner som är logiska entiteter i nätverket. 

Du måste tilldela minst en sensor för varje zon. Modellen på fem nivåer ger den flexibilitet och precision som krävs för att leverera det skydds system som motsvarar organisationens struktur.

Du kan redigera dina webbplatser direkt från valfri kart visning. När du öppnar en plats från en Map-vy visas antalet öppna aviseringar bredvid varje zon.

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/console-map-with-data-overlay-v2.png" alt-text="Skärm bild av en lokal hanterings konsol karta med data överlägg i Berlin.":::

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/diagram-of-sensor-showing-relationships.png" alt-text="Diagram som visar sensorer och regionala relationer.":::

Så här konfigurerar du en webbplats:

1. Lägg till nya affär senheter för att återspegla organisationens logiska struktur.

2. Lägg till nya regioner som återspeglar organisationens regioner.

3. Lägg till en webbplats.

4. Lägg till zoner till en webbplats.

5. Anslut sensorerna.

6. Tilldela sensorer till plats zoner.

Så här lägger du till affär senheter:

1. I vyn Enterprise väljer du **alla platser**  >  **Hantera affär senheter**.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/manage-business-unit-screen.png" alt-text="Skärm bild som visar vyn hantera affär Senheter.":::

2. Ange namnet på den nya affär senheten och välj **Lägg till**.

Så här lägger du till en ny region:

1. I vyn Enterprise väljer du **alla regioner**  >  **Hantera regioner**.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/manage-regions-screen.png" alt-text="Skärm bild som visar vyn hantera regioner.":::

2. Ange det nya region namnet och välj **Lägg till**.

Så här lägger du till en ny plats:

1. Välj :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/new-site-icon.png" border="false"::: i det översta fältet i vyn Enterprise. Markören visas som ett plus tecken ( **+** ).

2. Placera på **+** platsen för den nya platsen och markera den. Dialog rutan **Skapa ny webbplats** öppnas.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/create-new-site-screen.png" alt-text="Skärm bild av vyn Skapa ny webbplats.":::

3. Definiera namnet och den fysiska adressen för den nya platsen och välj **Spara**. Den nya platsen visas på webbplats kartan.

Så här tar du bort en webbplats:

1. I fönstret **plats hantering** väljer du :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: från det fält som innehåller plats namnet och väljer sedan **ta bort webbplats**. Rutan bekräftelse visas och kontrollerar att du vill ta bort platsen.

2. I rutan bekräftelse väljer du **Ja**. Bekräftelse rutan stängs och fönstret **plats hantering** visas utan den plats som du har tagit bort.

## <a name="create-enterprise-zones"></a>Skapa företags zoner

Zoner är logiska entiteter som gör att du kan dela upp enheter inom en plats i grupper baserat på olika egenskaper. Du kan till exempel skapa grupper för produktions linjer, understationer, plats områden eller typer av enheter. Du kan definiera zoner utifrån alla egenskaper som är lämpliga för din organisation.

Du konfigurerar zoner som en del av plats konfigurations processen.

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/site-management-zones-screen-v2.png" alt-text="Skärm bild av vyn plats hanterings zoner.":::

I följande tabell beskrivs parametrarna i fönstret **plats hantering** .

| Parameter | Beskrivning |
|--|--|
| Name | Sensorns namn. Du kan bara ändra det här namnet från sensorn. Mer information finns i användar handboken för Defender for IoT. |
| IP-adress | Sensorns IP-adress. |
| Version | Sensor versionen. |
| Anslutningsmöjlighet | Sensor anslutnings status. Statusen kan vara **ansluten** eller **frånkopplad**. |
| Senaste uppgradering | Datumet för den senaste uppgraderingen. |
| Uppgraderings förlopp | Förlopps indikatorn visar status för uppgraderings processen, enligt följande:<br />– Laddar upp paket<br />– Förbereder installation<br />– Stoppa processer<br />-Säkerhetskopiera data<br />-Tar ögonblicks bild<br />– Uppdaterar konfigurationen<br />– Uppdatering av beroenden<br />– Uppdaterar bibliotek<br />– Korrigera databaser<br />– Startar processer<br />-Verifierar systemets Sanity<br />– Verifieringen lyckades<br />-Lyckades<br />-Failure<br />-Uppgraderingen har startat<br />-Startar installationenogress bar shows the status of the upgrade process, as follows:<br />- Uploading package<br />- Preparing to install<br />- Stopping processes<br />- Backing up data<br />- Taking snapshot<br />- Updating configuration<br />- Updating dependencies<br />- Updating libraries<br />- Patching databases<br />- Starting processes<br />- Validating system sanity<br />- Validation succeeded<br />- Success<br />- Failure<br />- Upgrade started<br />- Starting installation<br /></br >Mer information om hur du uppgraderar finns i [Microsoft Support](https://support.microsoft.com/) för hjälp. |
| Enheter | Antalet enhets enheter som sensorn övervakar. |
| Aviseringar | Antalet aviseringar på sensorn. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-icon.png" border="false"::: | Aktiverar tilldelning av en sensor till zoner. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/delete-icon.png" border="false":::| Aktiverar borttagning av en frånkopplad sensor från platsen. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/sensor-icon.png" border="false"::: | Anger hur många sensorer som för närvarande är anslutna till zonen. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/ot-assets-icon.png" border="false"::: | Anger hur många sätts till gångar som för närvarande är anslutna till zonen. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/number-of-alerts-icon.png" border="false"::: | Anger antalet aviseringar som skickats av sensorer som har tilldelats zonen. |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassign-sensor-icon.png" border="false"::: | Avtilldelar sensorer från zoner. |

Så här lägger du till en zon på en plats:

1. I fönstret **plats hantering** väljer du :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: från det fält som innehåller plats namnet och väljer sedan **Lägg till zon**. Dialog rutan **Skapa ny zon** visas.

    :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/create-new-zone-screen.png" alt-text="Skärm bild av vyn Skapa ny zon.":::

2. Ange zon namnet.

3. Ange en beskrivning för den nya zonen som tydligt anger de egenskaper som du använde för att dela upp platsen i zoner.

4. Välj **Spara**. Den nya zonen visas i fönstret **plats hantering** under den plats som den här zonen tillhör.

Så här redigerar du en zon:

1. I fönstret **plats hantering** väljer du :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: från det fält som innehåller zon namnet och väljer sedan **Redigera zon**. Dialog rutan **Redigera zon** visas.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/zone-edit-screen.png" alt-text="Skärm bild som visar dialog rutan Redigera zon.":::

2. Redigera zon parametrarna och välj **Spara**.

Så här tar du bort en zon:

1. I fönstret **plats hantering** väljer du :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: från det fält som innehåller zon namnet och väljer sedan **ta bort zon**.

2. I rutan bekräftelse väljer du **Ja**.

Filtrera efter anslutnings status:

- Välj bredvid anslutning i det övre vänstra hörnet :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/down-pointing-icon.png" border="false"::: och välj sedan ett av följande alternativ:

  - **Alla**: visar alla sensorer som rapporterar till den här lokala hanterings konsolen.

  - **Ansluten**: visar bara anslutna sensorer.

  - **Frånkopplad**: visar bara frånkopplade sensorer.

Filtrera efter uppgraderings status:

- Från det övre vänstra hörnet väljer :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/down-pointing-icon.png" border="false"::: du bredvid **uppgraderings status** och väljer något av följande alternativ:

  - **Alla**: visar alla sensorer som rapporterar till den här lokala hanterings konsolen.

  - **Giltig**: visar sensorer med giltig uppgraderings status.

  - **Pågår**: presenterar sensorer som håller på att uppgraderas.

  - **Misslyckades**: visar sensorer vars uppgraderings process misslyckades.

## <a name="assign-sensors-to-zones"></a>Tilldela sensorer till zoner

För varje zon måste du tilldela sensorer som utför lokal trafik analys och aviseringar. Du kan endast tilldela sensorer som är anslutna till den lokala hanterings konsolen.

Så här tilldelar du en sensor:

1. Välj **plats hantering**. De otilldelade sensorer visas i det övre vänstra hörnet i dialog rutan.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassigned-sensors-view.png" alt-text="Skärm bild av vyn otilldelade sensorer.":::

2. Kontrol lera att **anslutnings** statusen är ansluten. Om inte, se [Anslut sensorer till den lokala hanterings konsolen](#connect-sensors-to-the-on-premises-management-console) för mer information om att ansluta. 

3. Välj :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-icon.png" border="false"::: för den sensor som du vill tilldela.

4. I dialog rutan **tilldela sensor** väljer du den affär senhet, region, plats och zon som ska tilldelas.

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-sensor-screen.png" alt-text="Skärm bild av vyn tilldela sensor.":::

5. Välj **tilldela**.

Ta bort tilldelning och ta bort en sensor:

1. Koppla bort sensorn från den lokala hanterings konsolen. Mer information finns i [ansluta sensorer till den lokala hanterings konsolen](#connect-sensors-to-the-on-premises-management-console) .

2. I fönstret **plats hantering** väljer du sensorn och väljer :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassign-sensor-icon.png" border="false"::: . Sensorn visas i listan över otilldelade sensorer efter en liten stund.

3. Om du vill ta bort den ej tilldelade sensorn från platsen väljer du sensorn från listan över ej tilldelade sensorer och väljer :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/delete-icon.png" border="false"::: .

## <a name="see-also"></a>Se även

[Felsöka sensorn och den lokala hanteringskonsolen](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md)
