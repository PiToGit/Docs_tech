# Les Infrastrusture de base

Plusieurs éléments sont nécessaire pour permettre à une application de fonctionner correctement et en toute sécurité.

Nous allons voir ici deux type d'infrastructure : Le On premise et le Cloud.

Même si la finalité reste la même, nous allons voir qu'il y a certaines différences importantes dans leur construction.

## Les infrastructures On Premise

### Tout d'abord que veut dire On Premise ?

C'est ce que l'on peut appeler une infrastructure classique.<br>
Littérallement, ce terme veut dire "sur site" mais plus exactement "dans son propre environnement".<br>
Ce qui signifie que l'infra et les applications sont hébergés et maintenus par le propre service informatique de l'entreprise.<br>
Les entreprise dispose généralement d'une **Salle blanche** où sont entreposer les baies de serveurs et de stockage.<br>
Ces machines sont, dans la plupart des cas, des Machines Virtuelles (VM). Ce qui permet un gain de place dans ces salles.<br>
Une autre alternative est de passé par une prestation.<br>
Les serveurs constituant l'infra de l'application sont alors hébergés dans des DataCenter. (1)<br>
{ .annotate }

1.   Voici quelques exemple de Datacenters :<br> OVH,<br> IBM,<br> CIV<br>

La maintenance matérielle est donc à la charge du fournisseurs.<br>
Celui-ci assure le bon fonctionnement mais surtout la résillience de l'infra à sa charge.<br>

### Un Infra On-premise type ça donne quoi ?

Afin de constituer une infra classique plusieurs éléments sont essentiels.<br>
Il faut évidement un serveur ou plusieurs de composant applicatifs : Le backend et le frontend.<br>
Un serveur de Données qui gérera les différentes BDD. (1)<br>
{ .annotate }

1. 
    SQL : Oracle, PostGres... <br>
    NoSQL : <br>
        + MongoDB, Elasticsearch, MySql... pour celles orientées Document<br>
        + Cassandra, AWS DynamoDB, Hbase... pour celles orientées Collonnes<br>
        + Redis, Memcachee... pour celles orientées Clé/valeur (plus utilisées pour le cache de l'application)<br>

Voici une exemple d'architecture de base

[![infra_1](./Schema/infra_1.drawio)](./Schema/infra_1.drawio)

Nous pouvons ajouter à cette base plusieurs choses pour constituer une infra solide et sécurisé.<br>
Ces composants peuvent varier selon la politique de sécurité, le contexte et l'accessibilité.<br>

Ajoutons un peu de sécurité interne en intégrant un serveur type Active directories pour gérer nos collaborateurs.

[![infra_2](./Schema/infra_2.drawio)](./Schema/infra_2.drawio)

Afin de stocker et partager les fichiers à travers l'entreprise nous pouvons y mettre un serveur de fichiers.<br>
Ces fichiers peuvent être de différentes nature et donc servir pour différentes choses : <br>
  - Fichiers de configuration<br>
  - Documents de la société<br>
  - Code applicatif<br>
  - ...<br>

[![infra_3](./Schema/infra_3.drawio)](./Schema/infra_3.drawio)

Enfin, si notre application est ouverte au publique, ou un partenaire externe, il nous faut la placer derrière un parefeu et un proxy sécuriser son accès.<br>
Ces derniers composants ne seront pas placés directement dans notre réseau interne.<br>
Ils seront palcé dans une zone de réseau publique que l'on appelle DMZ.<br>
Ils font office de porte d'entrée pour toutes personnes ne faisant pas partie de l'organisation.<br>

[![Infra_4](./Schema/infra_4.drawio)](./Schema/infra_4.drawio)

L'exemple que je vous donne reste basique. Les composants vont variés selon le besoin et la stratégie d'entreprise.

## Les infrastructures Cloud
