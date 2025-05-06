# mongo-logger

Reusable request logger and API key authorizer using MongoDB for Node projects.

````markdown
# @ashkiani/mongo-logger

Reusable request logger and API key authorizer using MongoDB.  
This utility provides a structured logging mechanism and optional key-based API access control for Express apps and other Node.js services backed by MongoDB.

---

## 📦 Installation

```bash
npm install @ashkiani/mongo-logger
````

---

## 🛠 Usage

### 1. Set your environment variable:

Make sure your project defines the MongoDB URI as:

```
Mongo_URI=mongodb://username:password@host:port/dbname
```

---

### 2. Import and use in your Node.js code:

```js
const dbClient = require('@ashkiani/mongo-client');  
const mongoLogger = require('@ashkiani/mongo-logger');

(async () => {
    await dbClient.connect();

    app.post("/some/api", async (req, res) => {
        const logEntry = await mongoLogger.createLogEntryObj(req, dbClient.get(), "/some/api");

        await mongoLogger.writeLogObj(logEntry, dbClient.get(), "your-db-name", "your-collection-name");

        if (!logEntry.user.authorized) {
            return res.status(403).json({ error: logEntry.user.issue });
        }

        res.json({ message: "Authorized request processed!" });
    });
})();
```

---

## 📘 API

createLogEntryObj(req, dbClient, route)
Creates a structured log object based on the request, origin, API key, and metadata.

writeLogObj(logEntry, dbClient, dbName, colName)
Writes the log object to the given MongoDB database and collection. Automatically scrubs the API key from storage if the user is authorized.

🔐 Keyless Access Behavior
If no key is provided, the logger checks if the environment (api_env) allows keyless access (dev, test, prod) and verifies if the origin is in the ALLOWED_ORIGINS list.

---

## 💡 Notes

* This module is designed to be used as a singleton across your app.
* Uses bcrypt to hash and match keys securely.
* Keys must be stored in MongoDB with pre-generated bcrypt-hashed values.
* Intended for API backends using Express or similar frameworks.
* API keys are scrubbed from logs for security.
* Connection URI should be stored securely in environment variables (`.env` or your deployment config).


---

## 📄 License

MIT © [Siavash Ashkiani](https://github.com/ashkiani)
