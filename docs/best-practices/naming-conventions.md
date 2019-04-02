---
title: Convenzioni di denominazione per le risorse di Azure
titleSuffix: Best practices for cloud applications
description: Indicazioni per la denominazione di macchine virtuali, account di archiviazione, reti, reti virtuali, subnet e altre entità di Azure.
author: telmosampaio
ms.date: 10/19/2018
ms.topic: best-practice
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.custom: seodec18
ms.openlocfilehash: 87869d1b38d2b4a71cd5b604436e04deab993131
ms.sourcegitcommit: 548374a0133f3caed3934fda6a380c76e6eaecea
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58420023"
---
# <a name="naming-conventions-for-azure-resources"></a>Convenzioni di denominazione per le risorse di Azure

Questo articolo è un riepilogo delle regole di denominazione e delle restrizioni per le risorse di Azure e offre un set di indicazioni di base per le convenzioni di denominazione. È possibile usare queste indicazioni come punto di partenza per le convenzioni specifiche per esigenze particolari.

La scelta di un nome per qualsiasi risorsa in Microsoft Azure è importante per i motivi seguenti:

- È difficile modificare un nome in un secondo momento.
- I nomi devono soddisfare i requisiti del tipo di risorsa specifica.

Le convenzioni di denominazione coerenti ne facilitano l'individuazione. Indicano anche il ruolo di una risorsa in una soluzione.

Per ottenere convenzioni di denominazione funzionali è necessario definirle e seguirle nelle applicazioni e nelle organizzazioni.

## <a name="naming-subscriptions"></a>Denominazione delle sottoscrizioni

Nel caso di denominazione delle sottoscrizioni di Azure, i nomi dettagliati rendono più chiaro il contesto e lo scopo di ogni sottoscrizione. Quando si lavora in un ambiente con numerose sottoscrizioni, seguendo una convenzione di denominazione condivisa è possibile migliorarne la chiarezza.

Ecco un modello consigliato per la denominazione delle sottoscrizioni:

`<Company> <Department (optional)> <Product Line (optional)> <Environment>`

- La società in genere sarà la stessa per ogni sottoscrizione. Tuttavia, alcune aziende possono avere società affiliate all'interno della struttura organizzativa. Queste società possono essere gestite da un gruppo IT centrale. In questi casi, potrebbero essere differenziate con il nome della società padre, *Contoso*, e il nome della società affiliata, *Northwind*.
- Reparto (Department) è un nome all'interno dell'organizzazione che include un gruppo di utenti. Questo elemento nello spazio dei nomi è facoltativo.
- Linea di prodotti (Product line) è un nome specifico per un prodotto o una funzione eseguita all'interno del reparto. Generalmente è facoltativo per servizi e applicazioni interni. Tuttavia, è consigliabile usarlo per i servizi pubblici che dovranno essere facilmente separati e identificati, ad esempio per avere una netta separazione dei record di fatturazione.
- Ambiente (Environment) è il nome che descrive il ciclo di vita della distribuzione di applicazioni o servizi, ad esempio Sviluppo, Controllo di qualità o Produzione.

| Azienda | Department | Linea di prodotti o servizio | Environment | Nome completo |
| --- | --- | --- | --- | --- |
| Contoso |SocialGaming |AwesomeService |Produzione |Contoso SocialGaming AwesomeService Produzione |
| Contoso |SocialGaming |AwesomeService |Sviluppo |Contoso SocialGaming AwesomeService Sviluppo |
| Contoso |IT |InternalApps |Produzione |Contoso IT InternalApps Produzione |
| Contoso |IT |InternalApps |Sviluppo |Contoso IT InternalApps Sviluppo |

Per altre informazioni su come organizzare le sottoscrizioni per aziende di grandi dimensioni, consultare [Scaffold Azure enterprise: governance prescrittiva per le sottoscrizioni](/azure/architecture/cloud-adoption/appendix/azure-scaffold).

## <a name="use-affixes-to-avoid-ambiguity"></a>Usare affissi per evitare ambiguità

Nella fase di denominazione delle risorse in Azure è consigliabile usare prefissi o suffissi comuni per identificare il tipo e il contesto della risorsa. Anche se tutte le informazioni su tipo, metadati e contesto sono disponibili a livello di codice, l'uso di affissi comuni semplifica l'identificazione visiva. Quando si incorporano gli affissi nella convenzione di denominazione, è importante specificare chiaramente se l'affisso si troverà all'inizio del nome, prefisso, o alla fine, suffisso.

Ecco ad esempio due nomi possibili per un servizio cloud che ospita un motore di calcolo:

- SvcCalculationEngine (prefisso)
- CalculationEngineSvc (suffisso)

Gli affissi possono fare riferimento ai diversi aspetti che descrivono le specifiche risorse. La tabella seguente illustra alcuni esempi usati comunemente.

| Aspetto | Esempio | Note |
| --- | --- | --- |
| Environment |dev, prod, QA |Identifica l'ambiente per la risorsa. |
| Località |usOcc (Stati Uniti occidentali), usOr (Stati Uniti orientali) |Identifica l'area in cui la risorsa viene distribuita. |
| Istanza |1, 2,... |Per risorse con più istanze denominate, ad esempio VM o schede di interfaccia di rete. |
| Prodotto o servizio |service |Identifica il prodotto, l'applicazione o il servizio supportato dalla risorsa |
| Ruolo |sql, web, messaggistica |Identifica il ruolo della risorsa associata. |

Quando si sviluppa una convenzione di denominazione specifica per l'azienda o i progetti, è importante scegliere un set comune di affissi, nonché la loro posizione, ovvero se si tratta di prefissi o suffissi.

## <a name="naming-rules-and-restrictions"></a>Regole di denominazione e restrizioni

Tutti i tipi di risorsa o servizio in Azure impongono una serie di restrizioni e ambito relativi alla denominazione. Tutte le convenzioni o i modelli di denominazione devono rispettare le regole e l'ambito di denominazione dei requisiti. Ad esempio, se il nome di una macchina virtuale esegue il mapping di un nome DNS, e pertanto deve essere univoco in Azure, il nome di una rete virtuale definisce l'ambito nel gruppo di risorse in cui è stato creato.

In generale, evitare i caratteri speciali, `-` o `_`, come primo o ultimo carattere in qualsiasi nome. Questi caratteri comportano un esito negativo per la maggior parte delle regole di convalida.

### <a name="general"></a>Generale

| Entità | Scope | Length | Maiuscole/minuscole | Caratteri validi | Modello consigliato | Esempio |
| --- | --- | --- | --- | --- | --- | --- |
|Gruppo di risorse |Sottoscrizione |1-90 |Non fa distinzione tra maiuscole e minuscole |Caratteri alfanumerici, di sottolineatura e Unicode, parentesi, trattino e punto (tranne alla fine) che corrispondono alla notazione regex descritta [qui](/rest/api/resources/resourcegroups/createorupdate). |`<service short name>-<environment>-rg` |`profx-prod-rg` |
|Set di disponibilità |Gruppo di risorse |1-80 |Non fa distinzione tra maiuscole e minuscole |Alfanumerico, carattere di sottolineatura e trattino |`<service-short-name>-<context>-as` |`profx-sql-as` |
|Tag |Entità associata |512 (nome), 256 (valore) |Non fa distinzione tra maiuscole e minuscole |Alfanumerico, caratteri speciali ad eccezione di `<`, `>`, `%`, `&`, `\`, `?`, `/`. Vedere le limitazioni [qui](/azure/azure-resource-manager/resource-group-using-tags). |`"key" : "value"` |`"department" : "Central IT"` |
|App Web |Globale |1-60 |Non fa distinzione tra maiuscole e minuscole |Alfanumerico e trattino |`<app_name>-<source-slot-name>` |`contoso-staging` |

### <a name="compute"></a>Calcolo

| Entità | Scope | Length | Maiuscole/minuscole | Caratteri validi | Modello consigliato | Esempio |
| --- | --- | --- | --- | --- | --- | --- |
|Macchina virtuale |Gruppo di risorse |1-15 (Windows), 1-64 (Linux) |Non fa distinzione tra maiuscole e minuscole |Alfanumerico e trattino |`<name>-<role>-vm<number>` |`profx-sql-vm1` |
|App per le funzioni | Globale |1-60 |Non fa distinzione tra maiuscole e minuscole |Alfanumerico e trattino |`<name>-func` |`calcprofit-func` |

> [!NOTE]
> Le macchine virtuali in Azure hanno due nomi distinti: il nome della macchina virtuale e il nome host. Quando si crea una macchina virtuale nel portale, viene usato lo stesso nome per il nome host e per il nome della risorsa della macchina virtuale. Le restrizioni sopra descritte si riferiscono al nome host. Il nome della risorsa effettivo può avere al massimo 64 caratteri.

### <a name="storage"></a>Archiviazione

| Entità | Scope | Length | Maiuscole/minuscole | Caratteri validi | Modello consigliato | Esempio |
| --- | --- | --- | --- | --- | --- | --- |
|Nome account di archiviazione (dati) |Globale |3-24 |Lettere minuscole |Alfanumerico |`<globally unique name><number>` (usare una funzione per calcolare un guid univoco per la denominazione degli account di archiviazione) |`profxdata001` |
|Nome account di archiviazione (dischi) |Globale |3-24 |Lettere minuscole |Alfanumerico |`<vm name without hyphens>st<number>` |`profxsql001st0` |
| Nome contenitore |Account di archiviazione |3-63 |Lettere minuscole |Alfanumerico e trattino |`<context>` |`logs` |
|Nome del BLOB | Contenitore: |1-1024 |Fa distinzione tra maiuscole e minuscole. |Qualsiasi carattere URL |`<variable based on blob usage>` |`<variable based on blob usage>` |
|Nome della coda |Account di archiviazione |3-63 |Lettere minuscole |Alfanumerico e trattino |`<service short name>-<context>-<num>` |`awesomeservice-messages-001` |
|Nome tabella | Account di archiviazione |3-63 |Non fa distinzione tra maiuscole e minuscole |Alfanumerico |`<service short name><context>` |`awesomeservicelogs` |
|Nome file | Account di archiviazione |3-63 |Lettere minuscole | Alfanumerico |`<variable based on blob usage>` |`<variable based on blob usage>` |
|Data Lake Store | Globale |3-24 |Lettere minuscole | Alfanumerico |`<name>dls` |`telemetrydls` |

### <a name="networking"></a>Rete

| Entità | Scope | Length | Maiuscole/minuscole | Caratteri validi | Modello consigliato | Esempio |
| --- | --- | --- | --- | --- | --- | --- |
|Rete virtuale |Gruppo di risorse |2-64 |Non fa distinzione tra maiuscole e minuscole |Alfanumerico, segno meno, carattere di sottolineatura e punto |`<service short name>-vnet` |`profx-vnet` |
|Subnet |Rete virtuale padre |2-80 |Non fa distinzione tra maiuscole e minuscole |Alfanumerico, segno meno, carattere di sottolineatura e punto |`<descriptive context>` |`web` |
|Interfaccia di rete |Gruppo di risorse |1-80 |Non fa distinzione tra maiuscole e minuscole |Alfanumerico, segno meno, carattere di sottolineatura e punto |`<vmname>-nic<num>` |`profx-sql1-vm1-nic1` |
|Gruppo di sicurezza di rete |Gruppo di risorse |1-80 |Non fa distinzione tra maiuscole e minuscole |Alfanumerico, segno meno, carattere di sottolineatura e punto |`<service short name>-<context>-nsg` |`profx-app-nsg` |
|Regola del gruppo di sicurezza di rete |Gruppo di risorse |1-80 |Non fa distinzione tra maiuscole e minuscole |Alfanumerico, segno meno, carattere di sottolineatura e punto |`<descriptive context>` |`sql-allow` |
|Indirizzo IP pubblico |Gruppo di risorse |1-80 |Non fa distinzione tra maiuscole e minuscole |Alfanumerico, segno meno, carattere di sottolineatura e punto |`<vm or service name>-pip` |`profx-sql1-vm1-pip` |
|Load Balancer |Gruppo di risorse |1-80 |Non fa distinzione tra maiuscole e minuscole |Alfanumerico, segno meno, carattere di sottolineatura e punto |`<service or role>-lb` |`profx-lb` |
|Configurazione di regole del servizio di bilanciamento del carico |Load Balancer |1-80 |Non fa distinzione tra maiuscole e minuscole |Alfanumerico, segno meno, carattere di sottolineatura e punto |`<descriptive context>` |`http` |
|Gateway applicazione di Azure |Gruppo di risorse |1-80 |Non fa distinzione tra maiuscole e minuscole |Alfanumerico, segno meno, carattere di sottolineatura e punto |`<service or role>-agw` |`profx-agw` |
|Profilo di Gestione traffico |Gruppo di risorse |1-63 |Non fa distinzione tra maiuscole e minuscole |Alfanumerico, segno meno e punto |`<descriptive context>` |`app1` |

### <a name="containers"></a>Contenitori

| Entità | Scope | Length | Maiuscole/minuscole | Caratteri validi | Modello consigliato | Esempio |
| --- | --- | --- | --- | --- | --- | --- |
|Registro Container | Globale |5-50 |Non fa distinzione tra maiuscole e minuscole | Alfanumerico |`<service short name>registry` |`app1registry` |

### <a name="service-bus"></a>Bus di servizio

| Entità | Scope | Length | Maiuscole/minuscole | Caratteri validi | Modello consigliato | Esempio |
| --- | --- | --- | --- | --- | --- | --- |
|Spazio dei nomi del bus di servizio | Globale |6-50 |Non fa distinzione tra maiuscole e minuscole | Caratteri alfanumerici, trattini, deve iniziare con leter; visualizzare [qui](/rest/api/servicebus/create-namespace) per informazioni dettagliate. |`<service short name>-bus` |`app1-bus` |

## <a name="organize-resources-with-tags"></a>Organizza le risorse con i tag

Azure Resource Manager supporta l'aggiunta di tag alle entità con stringhe di testo arbitrario per identificare il contesto e semplificare l'automazione. Il tag `"sqlVersion"="sql2014ee"`, ad esempio, potrebbe identificare le VM che eseguono SQL Server 2014 Enterprise Edition. I tag devono essere usati per ottimizzare e migliorare il contesto unitamente alle convenzioni di denominazione scelte.

> [!TIP]
> Un altro vantaggio di tag è che si estendono ai gruppi di risorse e quindi consentono di collegare e mettere in correlazione le entità tra le diverse distribuzioni.

Ogni risorsa o gruppo di risorse può avere un massimo di **15** tag. Il nome del tag è limitato a 512 caratteri e il valore del tag è limitato a 256 caratteri.

Per altre informazioni sull'aggiunta di tag alle risorse, fare riferimento a [Uso dei tag per organizzare le risorse di Azure](/azure/azure-resource-manager/resource-group-using-tags/).

Ecco alcuni casi d'uso dei tag comuni:

- **Fatturazione**. Raggruppamento di risorse e relativa associazione a codici di fatturazione o chargeback.
- **Identificazione del contesto del servizio**. Identificazione dei gruppi di risorse tra di essi per raggruppamento e operazioni comuni.
- **Controllo di accesso e contesto di sicurezza**. Identificazione di ruoli amministrativi in base a portfolio, sistema, servizio, app, istanza e così via.

> [!TIP]
> Aggiungere tag preventivamente, aggiungerli spesso. È preferibile avere uno schema di base per l'aggiunta di tag e apportarvi modifiche nel tempo, piuttosto che doverlo fare in seguito.

Ecco un esempio di alcuni approcci comuni all'aggiunta di tag:

| Nome del tag | Chiave | Esempio | Comment |
| --- | --- | --- | --- |
| Fattura a/ID chargeback interno |billTo |`IT-Chargeback-1234` |Codice di fatturazione o I/O interno |
| Operatore o utente direttamente responsabile |managedBy |`joe@contoso.com` |Alias o indirizzo di posta elettronica |
| Nome del progetto |projectName |`myproject` |Nome della linea di prodotti o del progetto |
| Versione del progetto |projectVersion |`3.4` |Versione della linea di prodotti o del progetto |
| Environment |Environment |`<Production, Staging, QA >` |Identificatore dell'ambiente |
| Livello |Livello |`Front End, Back End, Data` |Identificazione di livello o del ruolo/contesto |
| Profilo dei dati |dataProfile |`Public, Confidential, Restricted, Internal` |Riservatezza dei dati archiviati nella risorsa |

## <a name="tips-and-tricks"></a>Suggerimenti e consigli

Alcuni tipi di risorse richiedano maggiore attenzione a livello di denominazione e convenzioni.

### <a name="virtual-machines"></a>Macchine virtuali

In particolare nelle topologie di grandi dimensioni, la denominazione corretta delle macchine virtuali faciliterà l'identificazione del ruolo e dello scopo di ogni computer, consentendo la creazione di script più prevedibili.

### <a name="storage-accounts-and-storage-entities"></a>Account ed entità di archiviazione

Sono disponibili due casi d'uso principali per gli account di archiviazione, ovvero il backup dei dischi per le VM e l'archiviazione dei dati in BLOB, code e tabelle. Gli account di archiviazione usati per i dischi delle macchine virtuali devono seguire la convenzione di denominazione basata sull'associazione al nome della macchina virtuale padre, con la potenziale necessità di avere più account di archiviazione per gli SKU della macchina virtuale di fascia alta e di applicare anche un suffisso numerico.

> [!TIP]
> Gli account di archiviazione, per dati o dischi, devono seguire una convenzione di denominazione che consenta di sfruttare più account di archiviazione, ovvero usando sempre un suffisso numerico.

È possibile configurare un nome di dominio personalizzato per l'accesso ai dati del BLOB nell'account di Archiviazione di Azure. L'endpoint predefinito per il servizio BLOB è `https://<name>.blob.core.windows.net`.

Se si esegue il mapping di un dominio personalizzato, ad esempio `www.contoso.com`, all'endpoint BLOB per l'account di archiviazione, è anche possibile accedere ai dati di BLOB dell'account di archiviazione usando tale dominio. Ad esempio, con un nome di dominio personalizzato, è possibile accedere a `https://mystorage.blob.core.windows.net/mycontainer/myblob` come `https://www.contoso.com/mycontainer/myblob`.

Per altre informazioni sulla configurazione di questa funzionalità, fare riferimento a [Configurare un nome di dominio personalizzato per l'endpoint di archiviazione BLOB](/azure/storage/storage-custom-domain-name/).

Per altre informazioni sulla denominazione di tabelle, contenitori e BLOB fare riferimento all'elenco seguente:

- [Denominazione e riferimento a contenitori, BLOB e metadati](https://msdn.microsoft.com/library/dd135715.aspx)
- [Denominazione di code e metadati](https://msdn.microsoft.com/library/dd179349.aspx)
- [Denominazione di tabelle](https://msdn.microsoft.com/library/azure/dd179338.aspx)

Un nome di BLOB può contenere qualsiasi combinazione di caratteri, ma i caratteri URL riservati devono essere correttamente preceduti da un carattere di escape. Evitare i nomi di BLOB che terminano con un punto (.), una barra (/) o una sequenza o una combinazione di entrambi. Per convenzione, la barra è il separatore di directory *virtuale* . Non usare una barra rovesciata (\\) nel nome di un BLOB. Le API client possono consentirne l'uso, ma non eseguono l'hashing correttamente e le firme non corrisponderanno.

Non è possibile modificare il nome del contenitore o dell'account di archiviazione dopo che è stato creato. È necessario eliminarlo e crearne uno nuovo se si vuole usare un nuovo nome.

> [!TIP]
> È consigliabile definire una convenzione di denominazione per tutti gli account di archiviazione e i tipi prima di avventurarsi nello sviluppo di un nuovo servizio o una nuova applicazione.

