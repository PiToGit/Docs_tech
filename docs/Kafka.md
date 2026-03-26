# Comprendre Apache Kafka : vulgarisation et exemples

## Qu'est-ce qu'Apache Kafka ?

Apache Kafka est une **plateforme de streaming distribuée** conçue pour gérer des flux de données en temps réel. En termes simples, Kafka permet de **lire, écrire, stocker et traiter des événements** (ou messages) de manière rapide, fiable et scalable.

Kafka fonctionne comme un **journal de commit distribué, partitionné et répliqué** : les données sont organisées en séquences (logs), réparties sur plusieurs serveurs (**brokers**), et copiées (**répliquées**) pour garantir la disponibilité.

### Les 3 capacités clés de Kafka

- **Publier et s’abonner à des flux d’événements** (modèle pub/sub)
- **Stocker durablement** ces événements (sur disque)
- **Traiter** ces flux en temps réel (Kafka Streams ou outils externes)

---

## Architecture simplifiée

```mermaid
graph LR
    Producteurs[Producteurs (Producers)] --> Brokers[Brokers (Kafka Cluster)]
    Brokers --> Consommateurs[Consommateurs (Consumers)]
    Producteurs -->|Topics / Partitions| Brokers


!!! info "Les briques principales"
    Producer (producteur) : application qui écrit des événements dans Kafka.
    Broker : serveur Kafka qui stocke et sert les événements.
    Topic : « catégorie » de messages (ex : paiements, clics, logs).
    Partition : sous-division d’un topic pour paralléliser et scaler.
    Consumer (consommateur) : application qui lit les événements d’un topic.

    Fonctionnement basique
    Un producteur envoie un événement dans un topic.
    Kafka écrit l’événement dans une partition (append-only log).
    Un ou plusieurs consommateurs lisent ces événements, chacun à son rythme.
    Point important : Kafka n’est pas seulement un « tuyau » ; c’est aussi un stockage (avec rétention) et un mécanisme de relecture.

# Schéma : une infra typique avec plusieurs usages

```mermaid
graph TD
    subgraph Sources
        WebApp[Web / App]
        BackendAPI[Backend API]
    end

    subgraph KafkaCluster["Kafka Cluster"]
    end

    subgraph Consumers
        StreamJob[(2) analytics<br/>Stream Job]
        Indexer[(3) search<br/>Indexer]
        Monitoring[(4) alerting<br/>Monitoring]
    end

    subgraph Destinations
        DWH[DWH]
        Search[Search]
        PagerEmail[Pager / Email]
    end

    WebApp -->| (1) événements | KafkaCluster
    BackendAPI -->| (1) événements | KafkaCluster

    KafkaCluster --> StreamJob
    KafkaCluster --> Indexer
    KafkaCluster --> Monitoring

    StreamJob --> DWH
    Indexer --> Search
    Monitoring --> PagerEmail


!!! exemple "exemples d’utilisation"
    1) Tracking produit (clics, vues, conversion)
    Les applications envoient page_view, add_to_cart, purchase.
    Un job de streaming calcule des métriques en temps réel (taux de conversion, funnels).
    2) Intégration entre systèmes (event-driven)
    Un service publie order_created.
    D’autres services (facturation, logistique) réagissent sans couplage direct.
    3) Logs, métriques et observabilité
    Les services publient logs/metrics.
    Des consommateurs déclenchent des alertes (erreurs, latence).
    4) Données IoT / capteurs
    Les capteurs publient des mesures.
    Traitement temps réel pour détection d’anomalies.

# Pourquoi Kafka est utilisé ?
Performance et débit élevés
Scalabilité horizontale (ajouter des brokers/partitions)
Durabilité (écrit sur disque + réplication)
Tolérance aux pannes (failover du leader)
Relecture (consumers gèrent leur offset)
Pour comprendre la robustesse, voir la page suivante : Réplication & tolérance aux pannes.
