---
title: ABAP för metadata extrahering i SAP R3 – Azure avdelningens kontroll
description: Den här artikeln beskriver stegen för att distribuera ABAP Function-modul i SAP server
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 9bd3c315fcc15317a9fa483289fdc326ca6aa47f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102614368"
---
# <a name="deploy-the-metadata-extraction-abap-function-module-for-the-sap-r3-family-of-bridges"></a>Distribuera ABAP för metadata extrahering för SAP R3-familjen för bryggor

Den här artikeln beskriver stegen för att distribuera ABAP Function-modulen i SAP server.

## <a name="overview"></a>Översikt

SAP Business Suite 4 HANA (S/4HANA), ECC och R/3 ERP-bryggan kan användas för att extrahera metadata från SAP-servern. Detta uppnås genom att placera ABAP Function-modulen på SAP-servern. Den här funktionen kan användas via fjärr anslutning för att fråga och hämta (som en textfil) de metadata som innehåller i SAP-servern.

När den körs gör bryggan något av följande:

1. Importerar metadata från en befintlig fil som redan har laddats ned lokalt från en tidigare brygga-körning.

2. Anropar API: t för ABAP-modulen, väntar på nedladdningen och importerar sedan metadata från filen.

Det här dokumentet beskriver de steg som krävs för att distribuera den här modulen.

> [!Note]
> Följande instruktioner har kompilerats baserat på SAP GUI v. 7.2

## <a name="deployment-of-the-module"></a>Distribution av modulen

### <a name="create-a-package"></a>Skapa ett paket

Det här steget är valfritt och ett befintligt paket kan användas.

1. Logga in på SAP S/4HANA-eller SAP ECC-servern och öppna **objekt navigering** (SE80-transaktion).

2. Välj alternativ **paket** i listan och ange ett namn för det nya paketet (till exempel Z \_ -MITI) och tryck sedan på knappen **Visa**.

3. Välj **Ja** i fönstret **skapa paket** . Därför öppnas ett fönster **paket verktyg: skapa paket** . Ange ett värde i fältet **kort beskrivning** och välj ikonen **Fortsätt** .

4. Välj **egna begär Anden** i fönstret **fråga efter Local Workbench-begäran** . Välj **utvecklings** förfrågan.

### <a name="create-a-function-group"></a>Skapa en funktions grupp

I Object Navigator väljer du **funktions grupp** i listan och skriver dess namn i fältet Indatatyp (till exempel Z \_ MITI \_ FGROUP). Välj ikonen **Visa** .

1. I fönstret **skapa objekt** väljer du **Ja** för att skapa en ny funktions grupp.

2. Ange en lämplig beskrivning i fältet **kort text** och tryck på knappen **Spara**.

3. Välj ett paket som för bereddes i föregående steg **skapa ett paket** och välj **Spara**.

4. Bekräfta en begäran genom att trycka på ikonen **Fortsätt**.

5. Aktivera funktions gruppen.

### <a name="create-the-abap-function-module"></a>Skapa ABAP Function-modulen

1. När du har skapat funktions gruppen väljer du den.

2. Högerklicka på funktions grupp namnet i lagrings platsens webbläsare och välj **skapa** och sedan **Function module**.

3. I fältet **Function-modul** anger du `Z_MITI_DOWNLOAD` . Fyll i **kort text** med rätt beskrivning.

När modulen har skapats anger du följande information:

1. Navigera till fliken **attribut** .

2. Välj **bearbetnings typ** som **fjärraktiverad funktion-modul**.

   :::image type="content" source="media/abap-functions-deployment-guide/processing-type.png" alt-text="Registrera källor-alternativ-Remote-Enabled Function-modul" border="true":::

3. Navigera till fliken **käll kod** . Det finns två sätt att distribuera kod för-funktionen:

   a. Ladda [ \_ \_ ned](https://github.com/Azure/Purview-Samples/tree/master/connectors/sap) filen med text filen Z MITI från huvud menyn. Det gör du genom att välja **verktyg**, **fler verktyg**, sedan **Ladda upp/ladda ned** och sedan **Ladda upp**.

   b. Du kan också öppna filen, kopiera dess innehåll och klistra in i **käll kods** ytan.

4. Gå till fliken **Importera** och skapa följande parametrar:

   a.  P \_ -typ DD02L-TABNAME (valfritt = sant)

   b.  *P \_ \_Typ sträng för lokal sökväg* (valfritt = sant)

   c.  *P \_ språk typ L001TAB-data standard \' E\'*

   d.  *ROWSKIPS-typ så \_ int standard 0*

   e.  *ROWCOUNT-typ, så \_ int standard 0*

   > [!Note]
   > Välj **pass värde** för alla

   :::image type="content" source="media/abap-functions-deployment-guide/import.png" alt-text="Alternativet registrera källor – import parametrar" border="true":::

5. Gå till fliken tabeller och definiera följande:

   `EXPORT_TABLE LIKE TAB512`

   :::image type="content" source="media/abap-functions-deployment-guide/export-table.png" alt-text="Alternativ för att registrera källor – fliken tabeller" border="true":::

6. Gå till fliken **undantag** och definiera följande undantag: `E_EXP_GUI_DOWNLOADFAILED`

   :::image type="content" source="media/abap-functions-deployment-guide/exceptions.png" alt-text="Alternativ för registrering av källor – fliken undantag" border="true":::

7. Spara funktionen (tryck på CTRL + S eller Välj **Function-modul** och **Spara** sedan på huvud menyn).

8. Klicka på **Aktivera** ikon i verktygsfältet (CTRL + F3) och välj knappen  **Fortsätt** i dialog rutan. Om du uppmanas bör du välja det genererade inkluderar för att aktive ras tillsammans med huvudfunktions-modulen.

### <a name="testing-the-function"></a>Testa funktionen

När alla föregående steg har slutförts följer du stegen nedan för att testa funktionen:

1. Öppna Z- \_ MITI \_ Hämta funktions modul.

2. Välj **Function module**, **testa** sedan och **testa Function-modul** från huvud menyn (eller tryck på F8).

3. Ange en sökväg till mappen i det lokala fil systemet i parametern P \_ lokal \_ sökväg och tryck på **Kör** -ikonen i verktygsfältet (eller tryck på F8).

4. Ange namnet på intresse området i fältet P- \_ område om en fil med metadata måste laddas ned eller uppdateras. När funktionen slutförs måste den mapp som har angetts i \_ parametern för lokal sökväg i P \_ innehålla flera filer med metadata i. Namn på filer efterlikna områden som kan anges i \_ fältet P Area.

Funktionen Slutför körningen och metadata kommer att laddas ned mycket snabbare vid start av den på datorn som har snabb nätverks anslutning med SAP S/4HANA eller ECC Server.

## <a name="next-steps"></a>Nästa steg

- [Registrera och skanna SAP ECC-källa](register-scan-sapecc-source.md)
- [Registrera och skanna SAP S/4HANA-källa](register-scan-saps4hana-source.md)
