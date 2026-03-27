# Réplication & tolérance aux pannes (Kafka)

Kafka est conçu pour rester disponible et éviter la perte de données même si des serveurs tombent. Deux mécanismes centraux : **réplication** et **élection de leader**.


## 1) La réplication : copier les partitions

Un topic est découpé en **partitions**. Chaque partition est répliquée sur plusieurs brokers selon un **facteur de réplication** (ex : 3).

- **Leader** : la copie « principale » de la partition (reçoit les écritures et sert les lectures).
- **Followers** : copies secondaires qui se synchronisent sur le leader.

### Schéma : une partition répliquée

``` mermaid
graph LR

Topic: commandes
Partition P0, replication factor = 3

   Writes/Reads --> Broker A[Broker A P0 (Leader)];
   Broker A --> | replicate | Broker B[Broker B P0 (Follower)];
   Broker A --> | replicate | Broker C[Broker C P0 (Follower)];
``` 

## À quoi sert la réplication ?

Durabilité : si un disque/serveur meurt, les données existent ailleurs.
Disponibilité : si le leader tombe, un follower peut prendre le relais.
2) Tolérance aux pannes : continuer malgré les crashs
Kafka tolère la panne de brokers grâce à :

Réplication
Élection automatique d’un nouveau leader si le leader actuel disparaît
Stockage persistant (écriture sur disque)
Schéma : failover (changement de leader)

Avant panne:
  Broker A = Leader (P0)
  Broker B = Follower (P0)
  Broker C = Follower (P0)

Panne de Broker A
        |
        v

Après failover:
  Broker B = Leader (P0)
  Broker C = Follower (P0)


Résultat : le cluster peut continuer à servir la partition, à condition qu’un replica à jour soit disponible.

3) Notion clé : ISR (In-Sync Replicas)
Les ISR sont les réplicas « suffisamment à jour » par rapport au leader.

Si un follower prend du retard (latence réseau, surcharge), il peut sortir de l’ISR.
L’élection de leader se fait parmi les réplicas considérés comme sûrs (selon la configuration).
Intuition : l’ISR, c’est la liste des copies « fiables » pour prendre le relais.

4) Accusés de réception (acks) : choisir le niveau de sécurité
Quand un producer envoie un message, il peut décider combien de confirmations attendre.

acks=0 : rapide, mais aucune garantie côté serveur.
acks=1 : confirmé par le leader seulement.
acks=all (ou -1) : confirmé par le leader et les réplicas requis (selon config).
Schéma : acks=all (simplifié)

Producer -> Leader writes message
           Leader -> Followers replicate
           Followers -> Leader ack replication
           Leader -> Producer ack success

En pratique, acks=all + des paramètres adaptés côté topic/cluster améliore la non-perte au prix d’un peu plus de latence.

5) Ce que ça protège (et ce que ça ne protège pas)
✅ Protège contre :

panne d’un broker
panne disque sur un nœud
redémarrages / maintenance
⚠️ Ne protège pas automatiquement contre :

mauvaise configuration (ex : réplication trop faible)
pannes corrélées (ex : toute une zone/rack)
suppression/compaction/rétention mal paramétrées
Résumé
Réplication : copies d’une partition sur plusieurs brokers.
Leader/Follower : le leader gère lectures/écritures, les followers se synchronisent.
Tolérance aux pannes : si le leader tombe, un follower peut devenir leader.
acks + ISR : déterminent le compromis entre performance et sécurité.
Si vous voulez, je peux ajouter une 3e page « Paramètres essentiels » (replication factor, min.insync.replicas, acks, rétention) avec une checklist de bonnes pratiques.