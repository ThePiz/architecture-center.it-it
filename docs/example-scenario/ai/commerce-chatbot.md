---
title: Chatbot di conversazione per prenotazioni di hotel in Azure
description: Creare un chatbot di conversazione per applicazioni commerciali con il servizio Azure Bot.
author: iainfoulds
ms.date: 07/05/2018
ms.openlocfilehash: a922a75d621672fcac95296b1d99112d68c91107
ms.sourcegitcommit: 0a31fad9b68d54e2858314ca5fe6cba6c6b95ae4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2018
ms.locfileid: "51610771"
---
# <a name="conversational-chatbot-for-hotel-reservations-on-azure"></a>Chatbot di conversazione per prenotazioni di hotel in Azure

Questo scenario di esempio è applicabile ad aziende che devono integrare un chatbot di conversazione nelle applicazioni. In questo scenario, un chatbot C# viene usato per una catena di hotel che consente ai clienti di controllare la disponibilità e prenotare l'alloggio tramite un'applicazione Web o per dispositivi mobili.

Gli usi potenziali includono la possibilità per i clienti di visualizzare la disponibilità in hotel e prenotare le camere, esaminare il menu da asporto di un ristorante e ordinare cibo oppure cercare fotografie e ordinarne le stampe. Generalmente le aziende devono assumere e formare rappresentanti del servizio clienti per soddisfare queste richieste, mentre i clienti devono attendere che ne sia disponibile uno per ottenere assistenza.

Con servizi di Azure come il servizio Bot e Language Understanding o Speech API, le aziende possono assistere i clienti ed elaborare gli ordini o le prenotazioni con bot scalabili e automatizzati.

## <a name="relevant-use-cases"></a>Casi d'uso pertinenti

Gli altri casi d'uso pertinenti includono:

* Visualizzazione del menu da asporto di un ristorante e ordinazione di cibo
* Controllo della disponibilità in hotel e prenotazione di una camera
* Ricerca nelle foto disponibili e ordinazione di stampe

## <a name="architecture"></a>Architettura

![Panoramica dell'architettura dei componenti di Azure coinvolti in un chatbot di conversazione][architecture]

Questo scenario include un bot di conversazione che svolge le funzioni di un receptionist di un hotel. Il flusso dei dati nello scenario avviene come segue:

1. Il cliente accede al chatbot con un'app Web o per dispositivi mobili.
2. L'utente viene autenticato con Azure Active Directory B2C (Business to Consumer).
3. Interagendo con il servizio Bot, l'utente richiede informazioni sulla disponibilità in hotel.
4. Servizi cognitivi elabora la richiesta in linguaggio naturale per comprendere la comunicazione del cliente.
5. Quando l'utente è soddisfatto del risultato, il bot aggiunge o aggiorna la prenotazione del cliente in un database SQL.
6. Application Insights raccoglie dati di telemetria in fase di esecuzione per offrire al team DevOps informazioni sulle prestazioni e l'utilizzo del bot.

### <a name="components"></a>Componenti

* [Azure Active Directory ][aad-docs] è il servizio directory e di gestione delle identità multi-tenant basato sul cloud di Microsoft. Azure AD supporta un connettore B2C che consente di identificare gli utenti con ID esterni come Google, Facebook o un account Microsoft.
* Il [servizio app][appservice-docs] consente di creare e ospitare applicazioni Web nel linguaggio di programmazione preferito senza gestire l'infrastruttura.
* Il [servizio Bot][botservice-docs] offre gli strumenti per creare, testare, distribuire e gestire bot intelligenti.
* [Servizi cognitivi][cognitive-docs] consente di usare algoritmi intelligenti per vedere, ascoltare, parlare, comprendere e interpretare le esigenze degli utenti tramite metodi di comunicazione naturali.
* Il [database SQL][sqldatabase-docs] è un servizio di database cloud relazionale completamente gestito compatibile con il motore di SQL Server.
* [Application Insights][appinsights-docs] è un servizio di gestione delle prestazioni applicative (APM, Application Performance Management) estendibile che consente di monitorare le prestazioni di applicazioni come il chatbot.

### <a name="alternatives"></a>Alternative

* [Microsoft Speech API][speech-api] consente di modificare le modalità di interazione dei clienti con il bot.
* [QnA Maker][qna-maker] consente di aggiungere rapidamente informazioni al bot da contenuto semistrutturato come un documento di domande frequenti.
* [Traduzione testuale][translator] è un servizio che può essere preso in considerazione per aggiungere facilmente il supporto multilingue al bot.

## <a name="considerations"></a>Considerazioni

### <a name="availability"></a>Disponibilità

Questo scenario usa il database SQL di Azure per archiviare le prenotazioni dei clienti. Il servizio database SQL include database con ridondanza della zona, gruppi di failover e replica geografica. Per altre informazioni, vedere la sezione relativa alle [funzionalità per la disponibilità del database SQL di Azure][sqlavailability-docs].

Per altri argomenti relativi alla disponibilità, vedere l'[elenco di controllo per la disponibilità][availability] in Centro architetture Azure.

### <a name="scalability"></a>Scalabilità

Questo scenario usa il servizio app di Azure. Con il servizio app, è possibile ridimensionare automaticamente il numero di istanze in cui viene eseguito il bot. Questa funzionalità consente di far fronte alla domanda dei clienti per l'applicazione Web e il chatbot. Per altre informazioni sul ridimensionamento automatico, vedere le [procedure consigliate per la scalabilità automatica][autoscaling] in Centro architetture di Azure.

Per altri argomenti relativi alla scalabilità, vedere l'[elenco di controllo per la scalabilità][scalability] in Centro architetture Azure.

### <a name="security"></a>Sicurezza

Per l'autenticazione degli utenti, questo scenario usa Azure Active Directory B2C (Business to Consumer). Con AAD B2C, il chatbot non archivia credenziali o informazioni riservate degli account dei clienti. Per altre informazioni, vedere la [panoramica di Azure Active Directory B2C][aadb2c-docs].

Alle informazioni archiviate nel database SQL di Azure viene applicata la crittografia dei dati inattivi con Transparent Data Encryption (TDE). Il database SQL offre anche Always Encrypted, che crittografa i dati durante l'esecuzione di query e l'elaborazione. Per altre informazioni sulla sicurezza del database SQL, vedere la sezione relativa a [sicurezza e conformità del database SQL di Azure][sqlsecurity-docs].

Per indicazioni generali sulla progettazione di soluzioni sicure, vedere la [documentazione sulla sicurezza di Azure][security].

### <a name="resiliency"></a>Resilienza

Questo scenario usa il database SQL di Azure per archiviare le prenotazioni dei clienti. Il servizio database SQL include database con ridondanza della zona, gruppi di failover, replica geografica e backup automatici. Queste funzionalità consentono all'applicazione di rimanere in esecuzione in caso di interruzioni o eventi di manutenzione. Per altre informazioni, vedere la sezione relativa alle [funzionalità per la disponibilità del database SQL di Azure][sqlavailability-docs].

Per monitorare l'integrità dell'applicazione, questo scenario usa Application Insights. Con Application Insights, è possibile generare avvisi e rispondere a problemi di prestazioni che influirebbero sull'esperienza dei clienti e sulla disponibilità del chatbot. Per altre informazioni, vedere [Informazioni su Application Insights][appinsights-docs].

Per indicazioni generali sulla progettazione di soluzioni resilienti, vedere [Progettazione di applicazioni resilienti per Azure][resiliency].

## <a name="deploy-the-scenario"></a>Distribuire lo scenario

Questo scenario è suddiviso in tre componenti per consentire l'esplorazione delle aree di maggiore interesse:

* [Componenti dell'infrastruttura](#deploy-infrastructure-components). Usare un modello di Azure Resource Manager per distribuire i componenti dell'infrastruttura di base di un servizio app, dell'app Web, di Application Insights, dell'account di archiviazione, di SQL Server e del database SQL.
* [Chatbot per l'app Web](#deploy-web-app-chatbot). Usare l'interfaccia della riga di comando di Azure per distribuire un bot con il servizio Bot e l'app LUIS (Language Understanding Intelligent Service).
* [Applicazione del chatbot C# di esempio](#deploy-chatbot-c-application-code). Usare Visual Studio per esaminare il codice dell'applicazione C# di esempio per prenotazioni di hotel ed eseguire la distribuzione in un bot in Azure.

**Prerequisiti.** È necessario un account Azure esistente. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

### <a name="deploy-infrastructure-components"></a>Distribuire i componenti dell'infrastruttura

Per distribuire i componenti dell'infrastruttura con un modello di Resource Manager, seguire questa procedura.

1. Fare clic sul pulsante **Distribuisci in Azure**:<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Fsolution-architectures%2Fmaster%2Fapps%2Fcommerce-chatbot.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>
2. Attendere l'apertura della distribuzione del modello nel portale di Azure e quindi completare la procedura seguente:
   * Scegliere **Crea nuovo** per creare un nuovo gruppo di risorse e quindi specificare un nome, ad esempio *myCommerceChatBotInfrastructure*, nella casella di testo.
   * Selezionare un'area nella casella a discesa **Località**.
   * Specificare un nome utente e una password sicura per l'account amministratore di SQL Server.
   * Esaminare le condizioni e quindi selezionare **Accetto le condizioni riportate sopra**.
   * Selezionare il pulsante **Acquista**.

Il completamento della distribuzione richiede alcuni minuti.

### <a name="deploy-web-app-chatbot"></a>Distribuire il chatbot per l'app Web

Per creare il chatbot, usare l'interfaccia della riga di comando di Azure. L'esempio seguente installa l'estensione dell'interfaccia della riga di comando per il servizio Bot, crea un gruppo di risorse e quindi distribuisce un bot che usa Application Insights. Quando richiesto, eseguire l'autenticazione dell'account Microsoft e consentire al bot di registrarsi nel servizio Bot e nell'app LUIS (Language Understanding Intelligent Service).

```azurecli-interactive
# Install the Azure CLI extension for the Bot Service
az extension add --name botservice --yes

# Create a resource group
az group create --name myCommerceChatbot --location eastus

# Create a Web App Chatbot that uses Application Insights
az bot create \
    --resource-group myCommerceChatbot \
    --name commerceChatbot \
    --location eastus \
    --kind webapp \
    --sku S1 \
    --insights eastus
```

### <a name="deploy-chatbot-c-application-code"></a>Distribuire il codice dell'applicazione C# del chatbot

In GitHub è disponibile un'applicazione C# di esempio: 

* [Esempio C# Commerce Bot](https://github.com/Microsoft/AzureBotServices-scenarios/tree/master/CSharp/Commerce/src)

L'applicazione di esempio include i componenti di autenticazione di Azure Active Directory e l'integrazione con il componente LUIS (Language Understanding Intelligent Service) di Servizi cognitivi. L'applicazione richiede Visual Studio per la creazione e la distribuzione dello scenario. Altre informazioni sulla configurazione di AAD B2C e dell'app LUIS sono disponibili nella documentazione del repository GitHub.

## <a name="pricing"></a>Prezzi

Per esaminare il costo di esecuzione dello scenario, nel calcolatore dei costi sono preconfigurati tutti i servizi. Per verificare la variazione dei prezzi per un determinato caso d'uso, modificare le variabili appropriate in base

Sono stati definiti tre profili di costo di esempio in base al numero di messaggi che si prevede venga elaborato dal chatbot:

* [Small][small-pricing]: questo esempio di prezzi è correlato all'elaborazione di meno di 10.000 messaggi al mese.
* [Medium][medium-pricing]: questo esempio di prezzi è correlato all'elaborazione di meno di 500.000 messaggi al mese.
* [Large][large-pricing]: questo esempio di prezzi è correlato all'elaborazione di meno di 10 milioni di messaggi al mese.

## <a name="related-resources"></a>Risorse correlate

Per un set di esercitazioni guidate sul servizio Azure Bot, vedere la [sezione delle esercitazioni][botservice-docs] nella relativa documentazione.


<!-- links -->
[aadb2c-docs]: /azure/active-directory-b2c/active-directory-b2c-overview
[aad-docs]: /azure/active-directory/
[appinsights-docs]: /azure/application-insights/app-insights-overview
[appservice-docs]: /azure/app-service/
[architecture]: ./media/architecture-commerce-chatbot.png
[autoscaling]: ../../best-practices/auto-scaling.md
[availability]: ../../checklist/availability.md
[botservice-docs]: /azure/bot-service/
[cognitive-docs]: /azure/cognitive-services/
[resiliency]: ../../resiliency/index.md
[resource-groups]: /azure/azure-resource-manager/resource-group-overview
[security]: /azure/security/
[scalability]: ../../checklist/scalability.md
[sqlavailability-docs]: /azure/sql-database/sql-database-technical-overview#availability-capabilities
[sqldatabase-docs]: /azure/sql-database/
[sqlsecurity-docs]: /azure/sql-database/sql-database-technical-overview#advanced-security-and-compliance
[qna-maker]: /azure/cognitive-services/QnAMaker/Overview/overview
[speech-api]: /azure/cognitive-services/speech/home
[translator]: /azure/cognitive-services/translator/translator-info-overview

[small-pricing]: https://azure.com/e/dce05b6184904c50b38e1a8654f726b6
[medium-pricing]: https://azure.com/e/304d17106afc480dbc414f9726078a03
[large-pricing]: https://azure.com/e/8319dd5e5e3d4f118f9029e32a80e887