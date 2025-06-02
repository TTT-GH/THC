# THC Java

## Installation
[Voir le processus d'installation](https://github.com/TTT-GH/TModeler/tree/main/java/README.md)

## 🔐 Secure Communication Test Suite

Ce dépôt démontre, à travers une série de tests concrets, les capacités d'un système de communication sécurisé entre utilisateurs, incluant la gestion des clés, la messagerie privée et publique, l'authentification des données, le transfert conditionnel de contenu, la communication en groupe, la gestion des droits d'accès, et la traçabilité horodatée.

---

### 🧪 Objectif du Test

La méthode `test()` simule plusieurs scénarios de communication numérique sécurisée entre cinq utilisateurs (`u1` à `u5`). Elle permet de valider les fonctionnalités critiques suivantes :

- Cryptographie asymétrique utilisateur
- Chiffrement bilatéral des messages
- Gestion de jetons et de paires de clés
- Vérification d’intégrité et d’origine des données
- Transfert conditionnel de contenu
- Communication de groupe sécurisée
- Accusés de réception horodatés
- Contrôle d'accès avancé basé sur les permissions et le niveau de sécurité

---

### 👤 Utilisateurs

Cinq utilisateurs sont extraits depuis la base de données pour participer aux échanges :

```java
User u1 = User.tms.all().first();
User u2 = User.tms.all().second();
User u3 = User.tms.all().get(2);
User u4 = User.tms.all().get(3);
User u5 = User.tms.all().get(4);
```

---

### 🔐 1. Message privé (u1 ➡️ u2)

Un message chiffré est envoyé de u1 à u2. Il est stocké de manière sécurisée dans la base.

```java
Message m1 = new Message();
m1.content.from(u1).to(u2).set("Bonjour User U2");
m1.save();
```

---

### 🔁 2. Renouvellement de jeton (u1)

Changement de paire de clés de u1, sans perturber la lecture des messages déjà envoyés.

```java
TNodeToken newToken = new TNodeToken();
newToken.generate(u1.token.get());
u1.token.set(newToken);
```

---

### 👁️ 3. Lecture par le destinataire

u2 lit correctement le message grâce à son accès bilatéral sécurisé :

```java
String readContent = received.content.from(u1).to(u2).get();
assert readContent.equals("Bonjour User U2");
```

---

### 🔁 4. Transfert du message (u2 ➡️ u3)

Le message original est transféré à u3, si la politique de sécurité le permet :

```java
String contentForU3 = m1.content.from(u1).to(u2).transfert(u3);
```

---

### ✅ 5. Donnée publique authentifiée

u1 publie une donnée publique (âge) signée numériquement.

```java
u1.age.securityLevel(THC.SECURE_LOW);
u1.age.from(u1).set(24);
```

---

### ⚠️ 6. Usurpation et rejet

Tentative d’écriture d’un âge par u3 rejetée, car la donnée n’est pas signée par u1.

```java
u1.age.from(u3).set(99);
assert u1.age.from(u3).get() == null;
```

---

### 💬 7. Message public sans destinataire

Un message public est envoyé par u4 sans chiffrement spécifique. Mais authentique de l'expéditeur

```java
publicComment.content.from(u4).set("Bonjour User U1");
```

---

### 👥 8. Création d’un groupe

Un groupe nommé "Group 1" est créé avec 5 utilisateurs.

```java
group.name.set("Group 1");
group.members.add(u1, u2, u3, u4, u5);
```

---

### 📢 9. Message de groupe (u3 ➡️ Group 1)

Un message sécurisé est diffusé à tous les membres du groupe.

```java
groupMsg.content.securityLevel(THC.SECURE_MEDIUM);
groupMsg.content.from(u3).to(group).set("Hi everyone!!");
```

---

### 👀 10. Lecture du message de groupe

Chaque membre valide son droit d'accès au message :

```java
if (groupMsg.content.from(u3).to(group).granted(user)) {
    String content = groupMsg.content.from(u3).to(group).get();
}
```

---

### 🕒 11. Lecture horodatée et état du message

Les lectures sont enregistrées avec horodatage via un objet TState :

```java
TState state = groupMsg.state.get();
state.received(LocalDateTime.now());
state.read(LocalDateTime.now());
```

---

### ⛔ 12. Tentative de lecture interdite

Un utilisateur non autorisé tente d’accéder à un message privé : l’accès est bloqué.

```java
String leakTry = m1.content.from(u1).to(u5).get();
assert leakTry == null;
```

---

### 🧠 13. Transfert bloqué (niveau de sécurité élevé)

Un message marqué comme non transférable empêche sa diffusion à un tiers.

```java
strictMessage.content.transferable(false);
String attemptTransfer = strictMessage.content.from(u1).to(u2).transfert(u3);
assert attemptTransfer == null;
```

---

### 📌 Enjeux couverts par ce test

- Authenticité : toute donnée est associée à son auteur via une signature numérique.
- Confidentialité : les messages sont chiffrés asymétriquement.
- Contrôle d’accès : chaque message définit qui peut le lire.
- Traçabilité : chaque lecture est enregistrée avec horodatage.
- Flexibilité : les règles de transfert et de visibilité sont configurables par niveau de sécurité.

---