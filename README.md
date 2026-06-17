# Challenge technique AvaHost

> ⏱ **Temps maximum :** 3 heures
> 🎯 **Objectif :** évaluer ta capacité à construire rapidement un vrai flux produit

---

## Contexte

Tu construis une application web à destination des voyageurs.

Les voyageurs peuvent échanger avec **Ava**, co-hôte IA favori des conciergeries.

Ava doit être capable de :

- répondre aux demandes d'information sur le logement
- gérer un incident signalé par un voyageur
- créer et suivre un ticket si nécessaire
- notifier l'hôte lorsqu'une intervention humaine est nécessaire
- appeler l'hôte dans les situations urgentes
- ne jamais divulguer de données sensibles sans confirmation du séjour

Pour chaque message voyageur, Ava ne doit pas simplement générer une réponse comme un chatbot classique.
**Ava doit décider quelle action est la plus appropriée.**

Actions possibles :

```ts
type AvaAction =
  | "reply_to_traveler"
  | "notify_host"
  | "call_host"
  | "create_ticket";
```

Nous voulons voir comment tu conçois un système **simple, fiable, testable et proche d'un vrai usage produit**.

---

## Stack attendue

Le challenge doit être réalisé en **TypeScript**.

Stack attendue :

- **Frontend web :** React
- **Backend :** NestJS
- **Stockage :** en mémoire (Supabase optionnel)
- **Choix libre** pour le reste

---

## Objectif produit

L'application doit permettre de tester Ava sur plusieurs messages voyageurs.

L'application doit permettre de :

- saisir un message voyageur
- envoyer ce message à Ava
- afficher la décision prise par Ava
- afficher la réponse voyageur
- afficher les notifications hôte si nécessaire
- créer et afficher les tickets si Ava décide qu'une action opérationnelle est nécessaire
- afficher le JSON complet de la décision

Le frontend sert **uniquement d'interface de test**.
Toute la logique critique doit tourner côté backend, dans NestJS.

---

## Fonctionnement attendu

Ava doit fonctionner comme un petit système de décision.

Le fonctionnement attendu est le suivant :

```
Message voyageur
  ↓
Analyse du message
  ↓
Récupération du contexte utile
  ↓
Application des règles métier
  ↓
Décision structurée
  ↓
Réponse voyageur et actions
```

---

## Backend NestJS attendu

Le backend NestJS doit gérer :

- la récupération du contexte
- le filtrage des données autorisées
- la décision Ava
- la création et le suivi des tickets

---

## Mock de contexte

Utilise ce contexte simplifié. Tu peux l'étendre si nécessaire.

```ts
export const listingMock = {
  id: "DEMO",
  publicListingData: {
    title: "Maison rétro avec jeux vidéo, jacuzzi et barbecue",
    shortName: "La Maison COSYGAMER",
    propertyType: "house",
    location: {
      city: "Évry-Grégy-sur-Yerre",
      region: "Île-de-France",
      country: "France",
    },
    capacity: {
      guests: 7,
      bedrooms: 2,
      beds: 4,
      bathrooms: 1,
    },
    bedConfiguration: {
      bedroom1: {
        name: "Chambre 1",
        beds: [{ type: "queen", size: "160x200", quantity: 1 }],
      },
      bedroom2: {
        name: "Chambre 2",
        beds: [{ type: "bunk_bed", quantity: 1 }],
      },
      livingRoom: {
        name: "Salon",
        beds: [{ type: "sofa_bed", quantity: 1 }],
      },
    },
    amenities: {
      internet: {
        items: ["wifi", "ethernet"],
        note: "Connexion Ethernet disponible pour le gaming",
      },
      outdoor: {
        items: ["jacuzzi", "barbecue"],
        note:
          "Jacuzzi accessible toute l'année. D'octobre à mai, contacter l'hôte 24h avant.",
      },
      parking: {
        items: ["free_parking_on_premises"],
        note: "Parking gratuit sur place",
      },
      services: {
        items: ["pets_allowed", "self_check_in", "lockbox"],
        note: "Animaux acceptés",
      },
    },
    houseRules: {
      checkIn: {
        start: "17:00",
        end: "00:00",
        method: "self_check_in",
      },
      checkOut: {
        time: "11:00",
      },
      maxGuests: 7,
      petsAllowed: true,
      additionalRules: [
        "Logement réservé aux personnes ayant effectué la réservation",
        "Prévenir l'hôte si des amis souhaitent passer",
        "Prévenir l'hôte pour tout petit événement",
      ],
    },
    safetyInfo: {
      warnings: [
        "Piscine/jacuzzi sans clôture ni verrou",
        "Mur d'escalade",
        "Caméras extérieures uniquement",
      ],
    },
  },

  privateStayData: {
    confirmationCode: "HM8BSDEMO",
    wifi: {
      networkName: "cosy-wifi-guest",
      password: "AlphaTango?5",
      troubleshootingProcedure: [
        "Débrancher la box internet",
        "Attendre 20 secondes",
        "Rebrancher la box",
        "Attendre 2 minutes",
      ],
    },
    access: {
      lockboxCode: "0007",
    },
    emergencyContacts: {
      hostName: "COSYGAMER Manager",
      emergency: "112",
      police: "17",
      medical: "15",
      fire: "18",
    },
  },
};
```

---

## Règles produit importantes

Pour un **incident Wi-Fi**, Ava doit toujours respecter cette procédure :

1. Rappeler les identifiants Wi-Fi
2. Proposer la procédure de redémarrage
3. Créer un ticket **uniquement après échec** de la procédure
4. Informer le voyageur quand le ticket est résolu

Pour **toutes les demandes** des voyageurs, Ava doit être prudente.

Elle ne doit pas :

- inventer une information absente du contexte
- divulguer une donnée sensible à une personne non confirmée
- valider elle-même une exception importante
- traiter une urgence comme une simple question

Une **donnée sensible** peut être par exemple :

- mot de passe Wi-Fi
- code de boîte à clé

Le séjour est **confirmé** si le voyageur fournit le code :

```
HM8BSDEMO
```

---

## Interface web attendue

L'interface peut être très simple. Elle doit contenir au minimum :

- `[Champ message voyageur]`
- `[Toggle séjour confirmé / non confirmé]`
- `[Bouton envoyer]`

**Résultat :**

- Actions décidées par Ava
- Message envoyé au voyageur
- Notification hôte éventuelle
- Ticket créé éventuel
- Explication de la décision

---

## Modèle Ticket

```ts
type Ticket = {
  id: string;

  listingId: string;
  conversationId: string;

  category:
    | "internet"
    | "cleaning"
    | "access"
    | "maintenance"
    | "safety"
    | "other";

  priority: "low" | "medium" | "critical";

  title: string;
  description: string;

  status: "created" | "in_progress" | "resolved";

  createdAt: string;
  updatedAt: string;
};
```

Exemple de payload :

```json
{
  "listingId": "DEMO",
  "conversationId": "conv_demo_1",
  "category": "internet",
  "priority": "medium",
  "title": "Problème Internet",
  "description": "Le voyageur indique que le Wi-Fi ne fonctionne toujours pas.",
  "status": "created"
}
```

---

## Scénarios à tester

L'application doit permettre de tester au minimum ces scénarios.

### 1. Information publique

**Message voyageur :**
> À quelle heure est le check-out ?

**Comportement attendu :** Ava peut répondre directement avec les données publiques du logement.

**Action attendue :** `["reply_to_traveler"]`

### 2. Donnée sensible sans confirmation

**Message voyageur :**
> Quel est le mot de passe Wi-Fi ?

**Comportement attendu :** Ava ne doit pas donner le mot de passe Wi-Fi si le séjour n'est pas confirmé.

**Action attendue :** `["reply_to_traveler"]`

### 3. Donnée sensible avec confirmation

**Message voyageur :**
> Mon code de réservation est HM8BSDEMO. Quel est le mot de passe Wi-Fi ?

**Comportement attendu :** Ava peut partager les identifiants Wi-Fi.

**Action attendue :** `["reply_to_traveler"]`

### 4. Problème Internet

**Message voyageur :**
> Le Wi-Fi ne fonctionne pas.

**Comportement attendu :** Elle ne doit pas créer un ticket immédiatement si une première étape de résolution du problème peut être proposée.

### 5. Problème Internet persistant

**Message voyageur :**
> J'ai bien vérifié pour les identifiants et j'ai redémarré la box mais Internet ne fonctionne toujours pas.

**Comportement attendu :** Ava peut créer un ticket et notifier l'hôte.

**Actions possibles :** `["create_ticket", "notify_host", "reply_to_traveler"]`

### 6. Demande nécessitant validation hôte

**Message voyageur :**
> Est-ce qu'on peut arriver à 13h au lieu de 17h ?

**Comportement attendu :** Ava ne doit pas accepter directement. Elle doit notifier l'hôte ou indiquer qu'elle va vérifier.

**Actions possibles :** `["notify_host", "reply_to_traveler"]`

### 7. Plainte voyageur

**Message voyageur :**
> Le logement est sale. C'est inadmissible.

**Comportement attendu :** Ava doit reconnaître le problème et déclencher une action.

**Actions possibles :** `["create_ticket", "notify_host", "reply_to_traveler"]`

### 8. Urgence critique

**Message voyageur :**
> Il y a une grosse fuite d'eau dans la salle de bain. Il y a de l'eau partout.

**Comportement attendu :** Ava doit escalader immédiatement.

**Actions possibles :** `["call_host", "create_ticket", "reply_to_traveler"]`

### 9. Information absente du contexte

**Message voyageur :**
> Est-ce qu'il y a un chargeur Tesla sur place ?

**Comportement attendu :** Ava ne doit pas inventer la réponse. Elle doit demander vérification ou notifier l'hôte.

**Actions possibles :** `["notify_host", "reply_to_traveler"]`

---

## Livrables

- Le lien du repository GitHub avec les sources et un maximum de commits
- Un README clair avec les instructions pour tester la solution

Bonne chance 🚀
