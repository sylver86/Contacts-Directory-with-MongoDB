# DigitalConnect: MongoDB Contacts Directory

🎯 **Project Objective:**  
Build a **versatile and scalable contacts directory** leveraging MongoDB’s document-based architecture. By embracing a NoSQL approach, the system efficiently handles heterogeneous contact data (multiple phone numbers, social tags, addresses, etc.)—all while offering advanced querying capabilities to derive meaningful insights for business operations.

---

## Key Achievements

🔍 **Data Mastery:**  
- Incorporated a JSON-based dataset (contatti.json) to represent contacts with multiple fields (phone numbers, addresses, etc.).  
- Embraced MongoDB’s _document-based_ model to store and manage diverse contact information, enabling seamless updates and expansions.

🌟 **Challenging Task Conquered:**  
- Designed a **dynamic** contacts directory that can easily scale from small to large datasets without compromising performance.  
- Streamlined **updates** for existing contacts, including converting single phone numbers into arrays and adding new fields without altering an existing schema.

💡 **Innovative Approaches:**  
- Employed **advanced MongoDB queries** (e.g., `find()`, `aggregate()`) to enable powerful filtering, grouping, and aggregations on contact data.  
- Utilized **embedding** (sub-documents) to store addresses, emails, and optional social profile links in the same record, maximizing flexibility.

---

## Detailed Project Workflow

🔍 **Database & Collection Creation:**  
- Set up the `Rubrica_Utenti` database and the `Rubrica` collection in MongoDB.  
  ```js
  use Rubrica_Utenti
  db.createCollection("Rubrica")
  ```
- Ensured a smooth environment for inserting and querying JSON documents.

📊 **Data Import and Document Structure:**  
- Imported the JSON dataset containing varied contact details:
  ```shell
  mongoimport --db Rubrica_Utenti \
              --collection Rubrica \
              --file /path/to/contatti.json \
              --jsonArray
  ```
- Employed flexible document structures including fields for:
  - **Nome**, **Cognome**, **Numero_di_cellulare** (string or array),  
  - **Società**, **Data_di_nascita**,  
  - **Tag**, **Altri_contatti** (email, address, social links),  
  - **Chiamate_ultimo_mese**, and **Amici_stretti** (boolean).

⚙️ **Contact Updates and Data Expansion:**  
- Demonstrated how to **update existing contacts**, such as converting a single phone number field into an array for storing multiple numbers.  
- Inserted **new documents** with minimal effort due to MongoDB’s flexible schema design.

---

## Query Implementation

Below is a comprehensive list of queries that illustrate how to retrieve and manipulate data in the `Rubrica` collection. These examples highlight the versatility of MongoDB for both **basic** and **advanced** operations.

### 1. Find all contacts from “WebCorp”
```js
use Rubrica_Utenti
db.Rubrica.find({ Società: "WebCorp" })
```
- **What it does**: Searches the `Rubrica` collection for documents where the `Società` field is `"WebCorp"`.

---

### 2. Identify contacts with more than one phone number
```js
db.Rubrica.find({ "Numero_di_cellulare.1": { $exists: true } })
```
- **What it does**: Checks if there is a second element (`index 1`) in the `Numero_di_cellulare` array, meaning the contact has multiple phone numbers.

---

### 3. Extract only the phone numbers of contacts with the “lavoro” tag
```js
db.Rubrica.find(
  { Tag: "lavoro" },
  { Numero_di_cellulare: 1, _id: 0 }
)
```
- **What it does**: Filters for documents where `Tag` includes `"lavoro"` and returns only the `Numero_di_cellulare` field, excluding `_id`.

---

### 4. Get names of contacts without social profiles
```js
db.Rubrica.find(
  { "Altri_contatti.Profilo_social": { $exists: false } },
  { Nome: 1, Cognome: 1, _id: 0 }
)
```
- **What it does**: Filters for documents where `Profilo_social` does not exist under `Altri_contatti`, returning only the `Nome` and `Cognome`.

---

### 5. Count how many are marked “amici stretti” vs. not
```js
db.Rubrica.aggregate([
  { $group: { _id: "$Amici_stretti", count: { $sum: 1 } } }
])
```
- **What it does**: Groups documents by the `Amici_stretti` field (true/false) and counts them.

---

### 6. Calculate the average monthly calls for “amici stretti”
```js
db.Rubrica.aggregate([
  { $match: { Amici_stretti: true } },
  { $group: { _id: null, media_chiamate_ultimo_mese: { $avg: "$Chiamate_ultimo_mese" } } }
])
```
- **What it does**: Filters documents where `Amici_stretti` is `true` and then computes the average value of `Chiamate_ultimo_mese`.

---

### 7. Add a new phone number to an existing contact (Simone Azzurri)
```js
// Step 1: Convert the field to an array if it’s a string
db.Rubrica.updateMany(
  {
    $and: [
      { Nome: "Simone" },
      { Cognome: "Azzurri" },
      { Numero_di_cellulare: { $type: "string" } }
    ]
  },
  {
    $set: {
      Numero_di_cellulare: [ "$Numero_di_cellulare" ]
    }
  }
)

// Step 2: Push the new number to the array
db.Rubrica.updateMany(
  {
    $and: [
      { Nome: "Simone" },
      { Cognome: "Azzurri" }
    ]
  },
  {
    $push: {
      Numero_di_cellulare: "345678902"
    }
  }
)
```
- **What it does**:
  - **First command**: Detects if `Numero_di_cellulare` is a string, then converts it into an array.  
  - **Second command**: Adds `"345678902"` to that array.

---

### 8. Insert a new contact (Mary Salgado)
```js
db.Rubrica.insertOne({
  Nome: "Mary Salgado",
  Numero_di_cellulare: "346679933",
  Altri_contatti: {
    Indirizzo: "Via 25 Aprile 3, Firenze"
  }
})
```
- **What it does**: Adds a new document to `Rubrica` with the specified fields for Mary Salgado.

