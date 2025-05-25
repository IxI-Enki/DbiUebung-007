
# Part 1

- ### MongoDB-Datenbank erstellen

    Zuerst erstellen wir eine neue Datenbank namens `blogverwaltung`.

    ```javascript
    use blogverwaltung
    ```

- ### Collections erstellen

    Die Collections `BlogUsers` und `BlogEntries` - werden automatisch erzeugt.

- ### BlogUser-Dokumente einfügen

    Wir fügen 5 BlogUser-Dokumente in die `BlogUsers`-Collection ein.
    > Jedes Dokument enthält die Properties `Username`, `Name` (eingebettetes Dokument mit Vor- und Nachname), `Email` und `Passwort`.

    ```javascript
    db.BlogUsers.insertMany([
      {
        Username: "user1",
        Name: { Vorname: "Max", Nachname: "Mustermann" },
        Email: "max.mustermann@example.com",
        Passwort: "password1"
      },
      {
        Username: "user2",
        Name: { Vorname: "Anna", Nachname: "Schmidt" },
        Email: "anna.schmidt@example.com",
        Passwort: "password2"
      },
      {
        Username: "user3",
        Name: { Vorname: "Peter", Nachname: "Müller" },
        Email: "peter.mueller@example.com",
        Passwort: "password3"
      },
      {
        Username: "user4",
        Name: { Vorname: "Lisa", Nachname: "Bauer" },
        Email: "lisa.bauer@example.com",
        Passwort: "password4"
      },
      {
        Username: "user5",
        Name: { Vorname: "Thomas", Nachname: "Weber" },
        Email: "thomas.weber@example.com",
        Passwort: "password5"
      }
    ]);
    ```

- ### BlogEntries-Dokumente einfügen

    Wir fügen 5 BlogEntries-Dokumente in die `BlogEntries`-Collection ein.
    > Jedes Dokument enthält die Properties `title`, `author` (eingebettetes Dokument, kopiert von einem der BlogUser), `description`, `creationDate` und `measurementResults`.

    ```javascript
    db.BlogEntries.insertMany([
      {
        title: "Wasserprobe 1",
        author: {
          Username: "user1",
          Name: { Vorname: "Max", Nachname: "Mustermann" },
          Email: "max.mustermann@example.com"
        },
        description: "Beschreibung der Wasserprobe 1",
        creationDate: new Date(),
        measurementResults: { Wert1: 10, Wert2: 20 }
      },
      {
        title: "Wasserprobe 2",
        author: {
          Username: "user2",
          Name: { Vorname: "Anna", Nachname: "Schmidt" },
          Email: "anna.schmidt@example.com"
        },
        description: "Beschreibung der Wasserprobe 2",
        creationDate: new Date(),
        measurementResults: { Wert1: 15, Wert2: 25 }
      },
      {
        title: "Wasserprobe 3",
        author: {
          Username: "user3",
          Name: { Vorname: "Peter", Nachname: "Müller" },
          Email: "peter.mueller@example.com"
        },
        description: "Beschreibung der Wasserprobe 3",
        creationDate: new Date(),
        measurementResults: { Wert1: 20, Wert2: 30 }
      },
      {
        title: "Wasserprobe 4",
        author: {
          Username: "user4",
          Name: { Vorname: "Lisa", Nachname: "Bauer" },
          Email: "lisa.bauer@example.com"
        },
        description: "Beschreibung der Wasserprobe 4",
        creationDate: new Date(),
        measurementResults: { Wert1: 25, Wert2: 35 }
      },
      {
        title: "Wasserprobe 5",
        author: {
          Username: "user5",
          Name: { Vorname: "Thomas", Nachname: "Weber" },
          Email: "thomas.weber@example.com"
        },
        description: "Beschreibung der Wasserprobe 5",
        creationDate: new Date(),
        measurementResults: { Wert1: 30, Wert2: 40 }
      }
    ]);
    ```

- ### Überprüfen der eingefügten Dokumente

    ```javascript
    db.BlogUsers.find();
    db.BlogEntries.find();
    ```

----

# Part 2

### Abfragen

1. **Alle BlogUser-Dokumente, bei denen der User einen gewissen Nachnamen hat:**

```javascript
db.BlogUsers.find({"Name.Nachname": "Mustermann"});
```

2. **Alle BlogUser-Dokumente, bei denen eine Kombination aus Username + Passwort übereinstimmt:**

```javascript
db.BlogUsers.find({Username: "user1", Passwort: "password1"});
```

3. **Alle BlogUser-Dokumente, deren Username nicht dem Wert "Administrator" entspricht:**

```javascript
db.BlogUsers.find({Username: {$ne: "Administrator"}});
```

4. **Alle BlogEntries-Dokumente, die von einem BlogUser mit einem bestimmten Usernamen verfasst wurden:**

```javascript
db.BlogEntries.find({"author.Username": "user1"});
```

5. **Alle BlogEntries-Dokumente, deren Eigenschaft measurementResults ein eingebettetes Objekt mit der Eigenschaft temperatur enthält:**

```javascript
db.BlogEntries.find({"measurementResults.temperatur": {$exists: true}});
```

6. **Alle BlogEntries-Dokumente, deren Eigenschaft measurementResults ein eingebettetes Objekt mit der Eigenschaft temperatur NICHT enthält:**

```javascript
db.BlogEntries.find({"measurementResults.temperatur": {$exists: false}});
```

7. **Alle BlogEntries-Dokumente, bei denen eine Eigenschaft von measurementResults größer als X UND kleiner als Y ist:**

```javascript
db.BlogEntries.find({"measurementResults.Wert1": {$gt: 10, $lt: 30}});
```

8. **Alle BlogEntries-Dokumente, bei denen eine Eigenschaft von measurementResults größer als X ODER kleiner als Y ist:**

```javascript
db.BlogEntries.find({$or: [{"measurementResults.Wert1": {$gt: 30}}, {"measurementResults.Wert1": {$lt: 10}}]});
```

9. **Alle BlogEntries-Dokumente, deren Author entweder einen bestimmten Nachnamen hat, oder den Wert "Admin", aber nicht "Guest":**

```javascript
db.BlogEntries.find({
  $or: [
    {"author.Name.Nachname": "Mustermann"},
    {"author.Username": "Admin"}
  ],
  "author.Username": {$ne: "Guest"}
});
```

10. **Alle BlogUser-Dokumente sortiert nach dem Benutzernamen:**

```javascript
db.BlogUsers.find().sort({Username: 1});
```

11. **Die neuesten (bezogen auf die Eigenschaft creationDate) 2 BlogEntries-Dokumente:**

```javascript
db.BlogEntries.find().sort({creationDate: -1}).limit(2);
```

12. **Die ältesten 2 BlogEntries-Dokumente:**

```javascript
db.BlogEntries.find().sort({creationDate: 1}).limit(2);
```

13. **Das zweitälteste BlogEntry-Dokument:**

```javascript
db.BlogEntries.find().sort({creationDate: 1}).skip(1).limit(1);
```

### Indizes

1. **Der Wert von Username innerhalb der BlogUser-Collection eindeutig ist:**

```javascript
db.BlogUsers.createIndex({Username: 1}, {unique: true});
```

2. **Die Kombination aus Vorname und Nachname der BlogUser-Collection eindeutig ist:**

```javascript
db.BlogUsers.createIndex({"Name.Vorname": 1, "Name.Nachname": 1}, {unique: true});
```

3. **Der Titel von BlogEntry-Dokumenten eindeutig ist:**

```javascript
db.BlogEntries.createIndex({title: 1}, {unique: true});
```

###### <p align="center"> by Jan Ritt </p>
