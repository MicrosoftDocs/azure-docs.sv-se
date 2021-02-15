---
title: 'ML Studio (klassisk): lokal SQL Server – Azure'
description: Använd data från en SQL Server Database för att utföra avancerad analys med Azure Machine Learning Studio (klassisk).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/13/2017
ms.openlocfilehash: 8cdf1029371e0e11c38616e7800652ca9debbba7
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517407"
---
# <a name="perform-analytics-with-azure-machine-learning-studio-classic-using-a-sql-server-database"></a>Utföra analyser med Azure Machine Learning Studio (klassisk) med hjälp av en SQL Server databas

**gäller för:** ![ Gäller för. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klassisk) ![ gäller inte för.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Företag som arbetar med lokala data skulle till exempel kunna dra nytta av molnets skala och flexibilitet för sina arbets belastningar för Machine Learning. Men de vill inte störa sina aktuella affärs processer och arbets flöden genom att flytta sina lokala data till molnet. Azure Machine Learning Studio (klassisk) har nu stöd för att läsa data från en SQL Server-databas och sedan träna och värdera en modell med dessa data. Du behöver inte längre kopiera och synkronisera data mellan molnet och den lokala servern manuellt. I stället kan modulen **Importera data** i Azure Machine Learning Studio (klassisk) nu läsas direkt från din SQL Server databas för dina utbildnings-och Poäng jobb.

Den här artikeln innehåller en översikt över hur du intränger SQL Server data i Azure Machine Learning Studio (klassisk). Det förutsätter att du är bekant med Studio (klassiska) begrepp som arbets ytor, moduler, data uppsättningar, experiment *osv.*

> [!NOTE]
> Den här funktionen är inte tillgänglig för kostnads fria arbets ytor. Mer information om priser och nivåer för Machine Learning finns [Azure Machine Learning prissättning](https://azure.microsoft.com/pricing/details/machine-learning/).
>
>

<!-- -->



## <a name="install-the-data-factory-self-hosted-integration-runtime"></a>Installera Data Factory egen värd Integration Runtime
Om du vill komma åt en SQL Server databas i Azure Machine Learning Studio (klassisk) måste du ladda ned och installera Data Factory egen värd Integration Runtime, tidigare kallat Data Management Gateway. När du konfigurerar anslutningen i Machine Learning Studio (klassisk) har du möjlighet att ladda ned och installera Integration Runtime (IR) med hjälp av dialog rutan **Hämta och registrera data Gateway** som beskrivs nedan.


Du kan också installera IR i förväg genom att ladda ned och köra MSI-installationspaketet från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). MSI kan också användas för att uppgradera en befintlig IR till den senaste versionen, med alla inställningar bevarade.

Data Factory Self-Hosted Integration Runtime har följande krav:

* Data Factory Self-Hosted-integrering kräver ett 64-bitars operativ system med .NET Framework 4.6.1 eller högre.
* De versioner av Windows operativ system som stöds är Windows 10, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016. 
* Den rekommenderade konfigurationen för IR-datorn är minst 2 GHz, 4 kärnor, 8 GB RAM och 80 GB disk.
* Om värddatorn försätts i vilo läge svarar IR inte på data begär Anden. Konfigurera därför ett lämpligt energi schema på datorn innan du installerar IR. Om datorn är konfigurerad för vilo läge visar IR-installationen ett meddelande.
* Eftersom kopierings aktiviteten sker med en speciell frekvens, följer resursanvändningen (CPU, minne) på datorn även samma mönster med hög belastnings tid. Resursutnyttjande beror också på mängden data som flyttas. När flera kopierings jobb pågår, ser du att resursanvändningen går upp under hög belastnings tider. Även om den minsta konfigurationen som anges ovan är tekniskt tillräcklig, kanske du vill ha en konfiguration med fler resurser än den minsta konfigurationen beroende på din speciella belastning för data förflyttning.

Tänk på följande när du konfigurerar och använder en Data Factory Integration Runtime med egen värd:

* Du kan bara installera en instans av IR på en enda dator.
* Du kan använda en enda IR för flera lokala data källor.
* Du kan ansluta flera IRs på olika datorer till samma lokala data källa.
* Du konfigurerar en IRs för endast en arbets yta i taget. För närvarande kan inte IRs delas mellan arbets ytor.
* Du kan konfigurera flera IRs för en enda arbets yta. Du kanske till exempel vill använda en IR som är ansluten till dina test data källor under utveckling och en produktions-IR när du är redo att operationalisera.
* IR måste inte finnas på samma dator som data källan. Men att hålla närmare på data källan minskar tiden för gatewayen att ansluta till data källan. Vi rekommenderar att du installerar IR på en dator som är en annan än den som är värd för den lokala data källan så att gatewayen och data källan inte konkurrerar om resurser.
* Om du redan har en IR-installation på datorn som hanterar Power BI eller Azure Data Factory scenarier installerar du en separat IR för Azure Machine Learning Studio (klassisk) på en annan dator.

  > [!NOTE]
  > Du kan inte köra Data Factory lokal Integration Runtime och Power BI Gateway på samma dator.
  >
  >
* Du måste använda Data Factory egen värd Integration Runtime för Azure Machine Learning Studio (klassisk) även om du använder Azure-ExpressRoute för andra data. Du bör behandla data källan som en lokal data källa (som ligger bakom en brand vägg) även när du använder ExpressRoute. Använd Data Factory egen värd Integration Runtime för att upprätta en anslutning mellan Machine Learning och data källan.

Du hittar detaljerad information om installations krav, installations steg och fel söknings tips i artikeln [integration runtime i Data Factory](../../data-factory/concepts-integration-runtime.md).

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-id_toc450838866-classanchorspanspaningress-data-from-your-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>Ingress data från SQL Server-databasen till Azure Machine Learning
I den här genom gången ska du konfigurera ett Azure Data Factory Integration Runtime på en Azure Machine Learning arbets yta, konfigurera den och sedan läsa data från en SQL Server databas.

> [!TIP]
> Innan du börjar inaktiverar du webbläsarens blockering av popup-fönster för `studio.azureml.net` . Om du använder Google Chrome-webbläsaren kan du ladda ned och installera en av de många plugin-program som är tillgängliga i Google Chrome WebStore [Klicka en gång till app-tillägget](https://chrome.google.com/webstore/search/clickonce?_category=extensions).
>
> [!NOTE]
> Azure Data Factory egen värd Integration Runtime tidigare kallades tidigare Data Management Gateway. I steg-för-steg-självstudierna kan du fortsätta att referera till den som en gateway.  

### <a name="step-1-create-a-gateway"></a>Steg 1: skapa en gateway
Det första steget är att skapa och konfigurera en gateway för att få åtkomst till din SQL-databas.

1. Logga in på [Azure Machine Learning Studio (klassisk)](https://studio.azureml.net/Home/) och välj den arbets yta som du vill arbeta i.
2. Klicka på bladet **Inställningar** till vänster och klicka sedan på fliken **datagatewayer** överst.
3. Klicka på **ny datagateway** längst ned på skärmen.

    ![Ny datagateway](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)
4. I dialog rutan **ny datagateway** anger du **Gateway-namnet** och lägger eventuellt till en **Beskrivning**. Klicka på pilen i det nedre högra hörnet för att gå till nästa steg i konfigurationen.

    ![Ange namn och beskrivning för gateway](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)
5. I dialog rutan Ladda ned och registrera datagateway kopierar du GATEWAY-registrerings nyckeln till Urklipp.

    ![Hämta och registrera datagateway](./media/use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)
6. <span id="note-1" class="anchor"></span>Om du ännu inte har laddat ned och installerat Microsoft Data Management Gateway klickar du på **Hämta data Management Gateway**. Då går du till Microsoft Download Center där du kan välja den gateway-version du behöver, ladda ned den och installera den. Du hittar detaljerad information om installations krav, installations steg och fel söknings tips i de inledande avsnitten i artikeln [Flytta data mellan lokala källor och molnet med data Management Gateway](../../data-factory/tutorial-hybrid-copy-portal.md).
7. När gatewayen har installerats öppnas Data Management Gateway Configuration Manager och dialog rutan **Registrera Gateway** visas. Klistra in **nyckeln för gateway-registreringen** som du kopierade till Urklipp och klicka på **Registrera**.
8. Om du redan har en gateway installerad, kör Data Management Gateway Configuration Manager. Klicka på **ändra nyckel**, klistra in den **Gateway-registreringsnyckeln** som du kopierade till Urklipp i föregående steg och klicka på **OK**.
9. När installationen är klar visas dialog rutan **Registrera Gateway** för Microsoft Data Management Gateway Configuration Manager. Klistra in nyckeln för GATEWAY-registreringen som du kopierade till Urklipp i föregående steg och klicka på **Registrera**.

    ![Registrera gatewayen](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)
10. Gateway-konfigurationen är slutförd när följande värden anges på fliken **Start** i Microsoft Data Management Gateway Configuration Manager:

    * **Gateway-namn** och **instans namn** anges till namnet på gatewayen.
    * **Registreringen** har angetts till **registrerad**.
    * **Status** är inställt på **startad**.
    * Statusfältet längst ned visar **anslutna till data Management Gateway moln tjänst** tillsammans med en grön bock markering.

      ![Data Management Gateway Manager](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

      Azure Machine Learning Studio (klassisk) uppdateras också när registreringen är klar.

    ![Gateway-Registreringen lyckades](./media/use-data-from-an-on-premises-sql-server/gateway-registered.png)
11. I dialog rutan **Ladda ned och registrera datagateway** klickar du på kryss markeringen för att slutföra installationen. På sidan **Inställningar** visas Gateway-status som "online". I den högra rutan hittar du status och annan användbar information.

    ![Gateway-inställningar](./media/use-data-from-an-on-premises-sql-server/gateway-status.png)
12. I Microsoft Data Management Gateway-Configuration Manager växlar du till fliken **certifikat** . Det certifikat som anges på den här fliken används för att kryptera/dekryptera autentiseringsuppgifter för det lokala data lager som du anger i portalen. Det här certifikatet är standard certifikatet. Microsoft rekommenderar att du ändrar detta till ditt eget certifikat som du säkerhetskopierar i ditt certifikat hanterings system. Klicka på **ändra** om du vill använda ditt eget certifikat i stället.

    ![Ändra Gateway-certifikat](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-certificate.png)
13. valfritt Om du vill aktivera utförlig loggning för att felsöka problem med gatewayen, i Microsoft Data Management Gateway Configuration Manager växlar du till fliken **diagnostik** och markerar alternativet **Aktivera utförlig loggning för fel sökning** . Du hittar loggnings informationen i Windows Loggboken under noden program- **och tjänst loggar**  - &gt; **Data Management Gateway** . Du kan också använda fliken **diagnostik** för att testa anslutningen till en lokal data källa med hjälp av gatewayen.

    ![Aktivera utförlig loggning](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-verbose-logging.png)

Detta slutför installations processen för gateway i Azure Machine Learning Studio (klassisk).
Du är nu redo att använda dina lokala data.

Du kan skapa och konfigurera flera gateways i Studio (klassisk) för varje arbets yta. Du kan till exempel ha en gateway som du vill ansluta till dina test data källor under utvecklingen och en annan gateway för dina produktions data källor. Azure Machine Learning Studio (klassisk) ger dig flexibiliteten att konfigurera flera gateways beroende på din företags miljö. För närvarande kan du inte dela en gateway mellan arbets ytorna och bara en gateway kan installeras på en enda dator. Mer information finns i [Flytta data mellan lokala källor och molnet med data Management Gateway](../../data-factory/tutorial-hybrid-copy-portal.md).

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>Steg 2: Använd gatewayen för att läsa data från en lokal data Källa
När du har konfigurerat gatewayen kan du lägga till en modul för att **Importera data** till ett experiment som indata från den SQL Server databasen.

1. Välj fliken **experiment** i Machine Learning Studio (klassisk), klicka på **+ ny** i det nedre vänstra hörnet och välj **Tom experiment** (eller Välj ett av flera exempel experiment som är tillgängliga).
2. Leta upp och dra modulen **Importera data** till experimentets arbets yta.
3. Klicka på **Spara som** under arbets ytan. Ange "Azure Machine Learning Studio (klassisk) lokal SQL Server självstudie" för experimentets namn, Välj arbets ytan och klicka på kryss markeringen **OK** .

   ![Spara experiment med ett nytt namn](./media/use-data-from-an-on-premises-sql-server/experiment-save-as.png)
4. Klicka på modulen **Importera data** för att markera den. i rutan **Egenskaper** till höger om arbets ytan väljer du lokal SQL Database i list rutan **data källa** .
5. Välj den **datagateway** som du har installerat och registrerat. Du kan konfigurera en annan gateway genom att välja "(Lägg till ny datagateway...)".

   ![Välj data Gateway för modulen importera data](./media/use-data-from-an-on-premises-sql-server/import-data-select-on-premises-data-source.png)
6. Ange SQL **Database-servernamnet** och **databas namnet**, tillsammans med den SQL **Database-fråga** som du vill köra.
7. Klicka på **Ange värden** under **användar namn och lösen ord** och ange autentiseringsuppgifterna för databasen. Du kan använda Windows-integrerad autentisering eller SQL Server autentisering beroende på hur din SQL Server har kon figurer ATS.

   ![Ange autentiseringsuppgifter för databasen](./media/use-data-from-an-on-premises-sql-server/database-credentials.png)

   Meddelandet "värden krävs" ändras till "värden uppsättning" med en grön bock markering. Du behöver bara ange autentiseringsuppgifterna en gång om inte databas informationen eller lösen ordet ändras. Azure Machine Learning Studio (klassisk) använder det certifikat du angav när du installerade gatewayen för att kryptera autentiseringsuppgifterna i molnet. Azure lagrar aldrig lokala autentiseringsuppgifter utan kryptering.

   ![Importera data modul egenskaper](./media/use-data-from-an-on-premises-sql-server/import-data-properties-entered.png)
8. Klicka på **Kör** för att köra experimentet.

När experimentet har körts kan du visualisera data som du har importerat från databasen genom att klicka på utdataporten för modulen **Importera data** och välja **visualisera**.

När du har skapat ditt experiment kan du distribuera och operationalisera din modell. Med batch-körnings tjänsten kommer data från den SQL Server databas som kon figurer ATS i modulen **Importera data** att läsas och användas för poängsättning. Även om du kan använda tjänsten Request Response för att värdera lokala data, rekommenderar Microsoft att du använder [Excel-tillägget](excel-add-in-for-web-services.md) i stället. För närvarande stöds inte att skriva till en SQL Server-databas via **export data** , varken i experimentet eller de publicerade webb tjänsterna.