# Chapitre 6 : Le Protocole HTTP
## Solutions des Travaux Pratiques

---

## TP 1 : Exploration avec les DevTools - Solutions

### 1.2 Observer une requête simple - Réponses

**Question 1 : Quel est le code de statut ?**  
Réponse : **200 OK**

**Question 2 : Quels headers de requête sont envoyés ?**  
Les headers typiques incluent :

```
Host: httpbin.org
User-Agent: (informations sur le navigateur utilisé)
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: fr-FR,fr;q=0.9
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
Cache-Control: max-age=0
```

**Question 3 : Quel est le Content-Type de la réponse ?**  
Réponse : **application/json**

### 1.4 Tableau complété

| URL | Méthode | Code | Content-Type |
|-----|--------|------|--------------|
| httpbin.org/get | GET | 200 | application/json |
| httpbin.org/post | POST | 200 | application/json |
| httpbin.org/status/201 | GET | 201 | text/html |

---

## TP 2 : Maîtrise de cURL - Solutions

### 2.1 Différence entre `-i` et `-v`

**Option -i (include)**
- Affiche les headers de réponse HTTP
- Montre uniquement la réponse du serveur
- Plus simple et concis
- Utile pour voir rapidement les codes de statut

**Option -v (verbose)**
- Mode debug complet
- Affiche les headers de requête ET de réponse
- Montre les détails SSL/TLS
- Utile pour le débogage avancé

**Résumé :**  
Utilisez `-i` pour une vue simple et `-v` pour un débogage complet.

### Exercice avancé

```
curl -i -X POST \
  -H "Content-Type: application/json" \
  -H "X-Custom-Header: MonHeader" \
  -d '{"action": "test", "value": 42}' \
  https://httpbin.org/post
```

**Explication :**
- `-i` : headers réponse
- `-X POST` : méthode
- `-H` : header personnalisé
- `-d` : body

---

## TP 3 : API REST avec JavaScript - Solutions

### Fonction `fetchWithRetry`

```javascript
async function fetchWithRetry(url, options = {}, maxRetries = 3) {
  let lastError;
  
  for (let attempt = 0; attempt <= maxRetries; attempt++) {
    try {
      const response = await fetch(url, options);
      
      if (response.status >= 500 && response.status < 600) {
        if (attempt < maxRetries) {
          await new Promise(r => setTimeout(r, 1000));
          continue;
        }
        throw new Error(`Erreur serveur ${response.status}`);
      }
      
      if (!response.ok) {
        throw new Error(`HTTP ${response.status}`);
      }
      
      return response;
      
    } catch (error) {
      lastError = error;
      
      if (attempt < maxRetries) {
        await new Promise(r => setTimeout(r, 1000));
        continue;
      }
    }
  }
  
  throw lastError;
}
```

---

## TP 4 : Analyse des Headers de Sécurité - Solutions

| Site | HSTS | X-Frame | CSP | Note |
|------|------|--------|-----|------|
| github.com | ✓ | DENY | ✓ | A+ |
| google.com | ✓ | SAMEORIGIN | ✓ | A |
| twitter.com | ✓ | DENY | ✓ | A |

### Exemple GitHub

- **HSTS** : force HTTPS
- **X-Frame-Options** : protège contre clickjacking
- **CSP** : limite les sources autorisées
- **Referrer-Policy** : contrôle les infos envoyées

---

## TP 5 : Cache HTTP - Solutions

### Exemple HTML

```html
<!DOCTYPE html>
<html>
<head>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <img src="image.jpg">
  <script src="script.js"></script>
</body>
</html>
```

### Configuration Express

```javascript
app.use('/style.css', (req, res, next) => {
  res.setHeader('Cache-Control', 'public, max-age=31536000, immutable');
  next();
});
```

### Recommandations

| Type | Cache-Control |
|------|--------------|
| HTML | no-cache |
| CSS/JS | max-age=31536000 |
| Images | max-age=604800 |
| API | no-store |

---

## Exercices Récapitulatifs

### Client HTTP simple

```html
<form>
  <input type="text" placeholder="URL">
  <button>Envoyer</button>
</form>
```

---

## Questions théoriques

### Différence no-cache / no-store

- **no-cache** : validation obligatoire
- **no-store** : aucun stockage

### Pourquoi POST n'est pas idempotent ?

- Chaque requête crée une nouvelle ressource

### Code 301

- Redirection permanente
- Nouvelle URL dans `Location`

### Header Origin

- Utilisé pour CORS
- Définit l'origine

### HttpOnly

- Protège contre XSS
- Empêche accès JS aux cookies
