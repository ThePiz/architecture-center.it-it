---
title: Architetture di riferimento di Azure
description: Architetture di riferimento e linee guida per l'implementazione per carichi di lavoro comuni in Azure.
layout: LandingPage
ms.topic: landing-page
ms.date: 04/02/2019
ms.openlocfilehash: 7c307c40a710fff7ff07e24570052411b7edd8bf
ms.sourcegitcommit: 1a3cc91530d56731029ea091db1f15d41ac056af
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58887761"
---
<!-- This file is generated! -->
<!-- See the templates in ./build/reference-architectures  -->
<!-- See data in index.json -->

# <a name="azure-reference-architectures"></a>Architetture di riferimento di Azure

Le architetture di riferimento sono organizzate in base allo scenario. Ogni architettura include le procedure consigliate, insieme a considerazioni per la scalabilità, la disponibilità, la gestibilità e la sicurezza. La maggior parte include anche una soluzione distribuibile o un'implementazione di riferimento.

Passare a: [Intelligenza artificiale](#ai-and-machine-learning) | [Big Data](#big-data-solutions) | [IoT](#internet-of-things) | [Microservizi](#microservices) | [Serverless](#serverless-applications) | [Reti virtuali](#virtual-networks) | [Carichi di lavoro delle macchine virtuali](#vm-workloads) | [SAP](#sap) | [Active Directory](#extend-on-premises-active-directory-to-azure) | [App Web](#web-applications)

<!-- markdownlint-disable MD033 -->

## <a name="ai-and-machine-learning"></a>Intelligenza artificiale e Machine Learning

<ul  class="panelContent cardsF">
<!-- Distributed training of deep learning models -->
<li style="display: flex; flex-direction: column;">
    <a href="./ai/training-deep-learning.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/batch-ai.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Training distribuito di modelli di Deep Learning</h3>
                        <p>Eseguire il training distribuito di modelli di Deep Learning in cluster di macchine virtuali abilitate per GPU.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Batch scoring for deep learning models -->
<li style="display: flex; flex-direction: column;">
    <a href="./ai/batch-scoring-deep-learning.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/batch-ai.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Punteggio batch per i modelli di apprendimento avanzato</h3>
                        <p>Automatizzare i processi batch in esecuzione che applicano uno trasferimento in stile neurale a un video.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Training of Python scikit-learn models -->
<li style="display: flex; flex-direction: column;">
    <a href="./ai/training-python-models.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/python-powered-h.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Training dei modelli scikit-learn Python</h3>
                        <p>Procedure consigliate per ottimizzare gli iperparametri di un modello scikit-learn Python.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Batch scoring of Python models -->
<li style="display: flex; flex-direction: column;">
    <a href="./ai/batch-scoring-python.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/python-powered-h.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Assegnazione dei punteggi in batch per i modelli Python</h3>
                        <p>È possibile usare Azure Machine Learning per assegnare punteggi in batch a numerosi modelli Python in parallelo in base a una pianificazione.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Real-time scoring of Python and deep learning models -->
<li style="display: flex; flex-direction: column;">
    <a href="./ai/realtime-scoring-python.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/python-powered-h.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Assegnazione dei punteggi in tempo reale per modelli Python e di Deep Learning</h3>
                        <p>Distribuire i modelli Python come servizi Web per eseguire stime in tempo reale, usando i modelli normali di Python o i modelli di apprendimento avanzato.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Batch scoring of R machine learning models -->
<li style="display: flex; flex-direction: column;">
    <a href="./ai/batch-scoring-R-models.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/logo-r.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Assegnazione dei punteggi in batch per i modelli R di Machine Learning</h3>
                        <p>Eseguire l'assegnazione dei punteggi in batch per i modelli R con Azure Batch.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Real-time scoring of R machine learning models -->
<li style="display: flex; flex-direction: column;">
    <a href="./ai/realtime-scoring-r.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/logo-r.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Assegnazione dei punteggi in tempo reale per i modelli R di Machine Learning</h3>
                        <p>Implementare un servizio di stima in tempo reale in R con Microsoft Machine Learning Server in esecuzione nel servizio Azure Kubernetes.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Batch scoring of Spark models on Azure Databricks -->
<li style="display: flex; flex-direction: column;">
    <a href="./ai/batch-scoring-databricks.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/databricks.png" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Assegnazione dei punteggi in batch per i modelli Spark in Azure Databricks</h3>
                        <p>Creare una soluzione scalabile per l'assegnazione dei punteggi in batch per un modello di classificazione Apache Spark usando Azure Databricks.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Real-time recommendation API -->
<li style="display: flex; flex-direction: column;">
    <a href="./ai/real-time-recommendation.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/machine-learning.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>API per consigli in tempo reale</h3>
                        <p>È possibile eseguire il training di un modello di consigli con Azure Databricks e distribuirlo come API con Azure Machine Learning.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Enterprise-grade conversational bot -->
<li style="display: flex; flex-direction: column;">
    <a href="./ai/conversational-bot.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/bot-services.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Bot di conversazione di livello aziendale</h3>
                        <p>Come creare un bot di conversazione di livello aziendale con il framework Azure Bot.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

## <a name="big-data-solutions"></a>Soluzioni Big Data

<ul  class="panelContent cardsF">
<!-- Enterprise BI with SQL Data Warehouse -->
<li style="display: flex; flex-direction: column;">
    <a href="./data/enterprise-bi-sqldw.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/data-guide.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Business intelligence aziendale con SQL Data Warehouse</h3>
                        <p>Pipeline ELT (estrazione-caricamento-trasformazione) per il trasferimento di dati da un database locale a SQL Data Warehouse.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Automated enterprise BI with Azure Data Factory -->
<li style="display: flex; flex-direction: column;">
    <a href="./data/enterprise-bi-adf.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/data-guide.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Business intelligence aziendale automatizzata con Azure Data Factory</h3>
                        <p>Automatizzare una pipeline ELT per eseguire il caricamento incrementale da un database locale.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Stream processing with Azure Databricks -->
<li style="display: flex; flex-direction: column;">
    <a href="./data/stream-processing-databricks.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/databricks.png" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Elaborazione di flussi con Azure Databricks</h3>
                        <p>Pipeline di elaborazione di flussi che unisce i record di due flussi, arricchisce i risultati e calcola una media mobile.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Stream processing with Azure Stream Analytics -->
<li style="display: flex; flex-direction: column;">
    <a href="./data/stream-processing-stream-analytics.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/azure-analysis-service.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Elaborazione di flussi con Analisi di flusso di Azure</h3>
                        <p>Pipeline di elaborazione flusso end-to-end che mette in correlazione i record da due flussi dati per calcolare una media mobile.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

## <a name="internet-of-things"></a>Internet delle cose

<ul  class="panelContent cardsF">
<!-- Azure IoT reference architecture -->
<li style="display: flex; flex-direction: column;">
    <a href="./iot/index.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="./iot/_images/iot.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Architettura di riferimento di Azure IoT</h3>
                        <p>Architettura consigliata per le applicazioni IoT in Azure che usano componenti PaaS (piattaforma distribuita come servizio)</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

## <a name="microservices"></a>Microservizi

<ul  class="panelContent cardsF">
<!-- Microservices on Azure Kubernetes Service (AKS) -->
<li style="display: flex; flex-direction: column;">
    <a href="./microservices/aks.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/aks.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Microservizi nel servizio Azure Kubernetes (AKS)</h3>
                        <p>Architettura consigliata per la distribuzione di microservizi in AKS.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Microservices architecture on Azure Service Fabric -->
<li style="display: flex; flex-direction: column;">
    <a href="./microservices/service-fabric.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/sf.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Architettura di microservizi in Azure Service Fabric</h3>
                        <p>Architettura consigliata per i microservizi in Service Fabric.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

## <a name="serverless-applications"></a>Applicazioni serverless

<ul  class="panelContent cardsF">
<!-- Serverless web application -->
<li style="display: flex; flex-direction: column;">
    <a href="./serverless/web-app.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/functions.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Applicazione Web senza server</h3>
                        <p>Applicazione Web serverless che rende disponibile il contenuto statico di Archiviazione BLOB e implementa un'API con Funzioni di Azure.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Event processing using Azure Functions -->
<li style="display: flex; flex-direction: column;">
    <a href="./serverless/event-processing.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/functions.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Elaborazione di eventi con Funzioni di Azure</h3>
                        <p>Architettura guidata dagli eventi che inserisce un flusso di dati e usa Funzioni di Azure per elaborare i dati.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

## <a name="virtual-networks"></a>Reti virtuali

<ul  class="panelContent cardsF">
<!-- Hybrid network using a virtual private network (VPN) -->
<li style="display: flex; flex-direction: column;">
    <a href="./hybrid-networking/vpn.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/vpn.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Rete ibrida tramite una rete privata virtuale (VPN)</h3>
                        <p>Connettere una rete locale a una rete virtuale di Azure.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Hybrid network using ExpressRoute -->
<li style="display: flex; flex-direction: column;">
    <a href="./hybrid-networking/expressroute.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/expressroute.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Rete ibrida tramite ExpressRoute</h3>
                        <p>Usare una connessione privata dedicata per estendere una rete locale ad Azure.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Hybrid network using ExpressRoute with VPN failover -->
<li style="display: flex; flex-direction: column;">
    <a href="./hybrid-networking/expressroute-vpn-failover.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/expressroute.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Rete ibrida tramite ExpressRoute con failover VPN</h3>
                        <p>Usare ExpressRoute con una rete VPN come connessione di failover per la disponibilità elevata.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Hub-spoke network topology -->
<li style="display: flex; flex-direction: column;">
    <a href="./hybrid-networking/hub-spoke.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/gateway.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Topologia di rete hub-spoke</h3>
                        <p>Creare un punto centrale di connettività alla rete locale, isolando i carichi di lavoro.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Hub-spoke topology with shared services -->
<li style="display: flex; flex-direction: column;">
    <a href="./hybrid-networking/shared-services.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/gateway.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Topologia hub-spoke con servizi condivisi</h3>
                        <p>Estendere una topologia hub-spoke includendo servizi condivisi come Active Directory.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- DMZ between Azure and on-premises -->
<li style="display: flex; flex-direction: column;">
    <a href="./dmz/secure-vnet-hybrid.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/vnet.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Rete perimetrale tra Azure e l'ambiente locale</h3>
                        <p>Usare le appliance virtuali di rete per creare una rete ibrida sicura.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- DMZ between Azure and the Internet -->
<li style="display: flex; flex-direction: column;">
    <a href="./dmz/secure-vnet-dmz.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/vnet.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Rete perimetrale tra Azure e Internet</h3>
                        <p>Usare le appliance virtuali di rete per creare una rete sicura che accetti il traffico Internet.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Highly available network virtual appliances -->
<li style="display: flex; flex-direction: column;">
    <a href="./dmz/nva-ha.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/vnet.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Appliance virtuali di rete a disponibilità elevata</h3>
                        <p>Distribuire un set di appliance virtuali di rete per la disponibilità elevata in Azure.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

## <a name="vm-workloads"></a>Carichi di lavoro della VM

<ul  class="panelContent cardsF">
<!-- N-tier application with SQL Server -->
<li style="display: flex; flex-direction: column;">
    <a href="./n-tier/n-tier-sql-server.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/windows.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Applicazione a più livelli con SQL Server</h3>
                        <p>Macchine virtuali configurate per un'applicazione a più livelli con SQL Server in Windows.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Multi-region N-tier application -->
<li style="display: flex; flex-direction: column;">
    <a href="./n-tier/multi-region-sql-server.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/windows.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Applicazione a più livelli per più aree</h3>
                        <p>Applicazione a più livelli in due aree per la disponibilità elevata, con Gruppi di disponibilità AlwaysOn di SQL Server.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- N-tier application with Cassandra -->
<li style="display: flex; flex-direction: column;">
    <a href="./n-tier/n-tier-cassandra.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/linux-penguin.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Applicazione a più livelli con Cassandra</h3>
                        <p>Macchine virtuali configurate per un'applicazione a più livelli con Apache Cassandra in Linux.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Jenkins build server -->
<li style="display: flex; flex-direction: column;">
    <a href="./jenkins/index.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/jenkins.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Server di compilazione Jenkins</h3>
                        <p>Server Jenkins scalabile e di livello aziendale in Azure.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- SharePoint Server 2016 farm -->
<li style="display: flex; flex-direction: column;">
    <a href="./sharepoint/index.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/sharepoint.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Farm di SharePoint Server 2016</h3>
                        <p>Farm di SharePoint Server 2016 a disponibilità elevata in Azure con Gruppi di disponibilità AlwaysOn di SQL Server.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

## <a name="sap"></a>SAP

<ul  class="panelContent cardsF">
<!-- SAP NetWeaver -->
<li style="display: flex; flex-direction: column;">
    <a href="./sap/sap-netweaver.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/sap.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>SAP NetWeaver</h3>
                        <p>SAP NetWeaver in Windows, in un ambiente a disponibilità elevata che supporta il ripristino di emergenza.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- SAP S/4HANA -->
<li style="display: flex; flex-direction: column;">
    <a href="./sap/sap-s4hana.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/sap.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>SAP S/4HANA</h3>
                        <p>SAP S/4HANA in Linux, in un ambiente a disponibilità elevata che supporta il ripristino di emergenza.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- SAP HANA on Azure Large Instances -->
<li style="display: flex; flex-direction: column;">
    <a href="./sap/hana-large-instances.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/sap.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>SAP HANA in istanze Large di Azure</h3>
                        <p>Istanze Large di HANA vengono distribuite in server fisici nelle aree di Azure.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

## <a name="extend-on-premises-active-directory-to-azure"></a>Estendere Active Directory locale ad Azure

<ul  class="panelContent cardsF">
<!-- Integrate with Azure Active Directory -->
<li style="display: flex; flex-direction: column;">
    <a href="./identity/azure-ad.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/azure-active-directory.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Integrazione con Azure Active Directory</h3>
                        <p>Integrare i domini AD locali con Azure Active Directory</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Extend an on-premises Active Directory domain to Azure -->
<li style="display: flex; flex-direction: column;">
    <a href="./identity/adds-extend-domain.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/active-directory-vm.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Estendere un dominio Active Directory locale ad Azure</h3>
                        <p>Distribuire Active Directory Domain Services (AD DS) in Azure per estendere il dominio locale.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Create an AD DS forest in Azure -->
<li style="display: flex; flex-direction: column;">
    <a href="./identity/adds-forest.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/active-directory-vm.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Creare una foresta di Active Directory Domain Services in Azure</h3>
                        <p>Creare un dominio di Active Directory separato in Azure considerato attendibile dalla foresta AD locale.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Extend Active Directory Federation Services (AD FS) to Azure -->
<li style="display: flex; flex-direction: column;">
    <a href="./identity/adfs.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/active-directory-vm.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Estendere Active Directory Federation Services in Azure</h3>
                        <p>Usare AD FS per l'autenticazione e l'autorizzazione federate per i componenti eseguiti in Azure.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

## <a name="web-applications"></a>Applicazioni Web

<ul  class="panelContent cardsF">
<!-- Basic web application -->
<li style="display: flex; flex-direction: column;">
    <a href="./app-service-web-app/basic-web-app.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/app-service.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Applicazione Web di base</h3>
                        <p>Applicazione Web con il servizio app di Azure e il database SQL di Azure.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Highly scalable web application -->
<li style="display: flex; flex-direction: column;">
    <a href="./app-service-web-app/scalable-web-app.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/app-service.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Applicazione Web a scalabilità elevata</h3>
                        <p>Procedure comprovate per migliorare la scalabilità in un'applicazione Web.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Highly available web application -->
<li style="display: flex; flex-direction: column;">
    <a href="./app-service-web-app/multi-region.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/app-service.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Applicazione Web a disponibilità elevata</h3>
                        <p>Eseguire un'app Web del servizio app in più aree per ottenere una disponibilità elevata.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Web application monitoring on Azure -->
<li style="display: flex; flex-direction: column;">
    <a href="./app-service-web-app/app-monitoring.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/app-service.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Monitoraggio delle applicazioni Web in Azure</h3>
                        <p>Monitorare un'applicazione Web ospitata in Servizio app di Azure.</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>