# Datenbanken

## Datenbanken

Verwendung: Verwaltung großer Datenmengen

## Datenbanken

Beispiele:

- SQL databases
  - MySQL
  - PostgreSQL
  - Microsoft SQL Server
  - SQLite
  - Oracle
- MongoDB
- Redis

[Verbreitung laut Stack Overflow Developer Survey 2020](https://insights.stackoverflow.com/survey/2020#technology-databases)

## Terminologie

- **Tabelle / Collection**: Ansammlung ähnlicher Datenobjekte (z.B. eine für Produkte)
- **Zeile / Eintrag / Dokument**: Einzelner Eintrag in einer Tabelle (z.B. für ein einzelnes Produkt)
- **Feld**: Ein Wert in einem Eintrag (z.B. _Preis_)

# CRUD-Operationen

## CRUD-Operationen

Grundlegende Operationen für Datenbankeinträge:

- **c**reate
- **r**ead / **r**etrieve
- **u**pdate
- **d**elete

## Create

SQL:

```sql
INSERT INTO product (name, category, price)
VALUES ('IPhone', 'electronics', 699);
```

MongoDB shell:

```js
db.products.insertOne({
  name: 'IPhone',
  category: 'electronics',
  price: 699,
});
```

## Read

SQL:

```sql
SELECT * FROM product
WHERE category = 'electronics';
```

MongoDB shell:

```js
db.products.find({ category: 'electronics' });
```

## Read

SQL:

```sql
SELECT name, category FROM product
WHERE category = 'electronics';
```

MongoDB shell:

```js
db.products
  .find({ category: 'electronics' })
  .project({ name: 1, price: 1 });
```

## Update

SQL:

```sql
UPDATE product
SET category = 'phones'
WHERE name = 'IPhone';
```

MongoDB shell:

```js
db.products.updateOne(
  { name: 'IPhone' },
  { $set: { category: 'phones' } }
);
```

## Delete

SQL:

```sql
DELETE FROM product
WHERE name = 'IPhone';
```

MongoDB shell:

```js
db.products.deleteOne({ name: 'IPhone' });
```

## Online Playgrounds

- [SQL Editor von W3Schools](https://www.w3schools.com/sql/trysql.asp?filename=trysql_select_all) (enthält bereits Daten, verwendbar mit Chrome und Safari)
- [MongoDB Web Shell](https://docs.mongodb.com/manual/tutorial/getting-started/)

## Übung

Erstellen / Ändern / Abfragen von Daten in einem Online Playground

# SQL vs MongoDB

## SQL vs MongoDB

SQL (Structured Query Language): entwickelt in den 1970ern, viele verschiedene Varianten

MongoDB: 2009 veröffentlicht

## SQL vs MongoDB

SQL: Einträge werden in Tabellen mit vordefinierten Feldnamen und Feldtypen gespeichert (Datenbankschema)

MongoDB: Einträge (Dokumente) in einer Collection können beliebige Felder haben; optional mit einem _Schema_ validierbar

## SQL vs MongoDB

SQL: standardisierte Sprache (theoretisch), unabhängig von der verwendeten Programmiersprache

MongoDB: direkte Bindings für Programmiersprachen

# MongoDB Datenformat

## Datentypen

- Zahlen
  - int (32 bit) / long (64 bit)
  - double (64 bit floating point)
  - decimal (128 bit)
- bool
- string
- binData
- date (Datum + Uhrzeit)
- array
- object
- null
- objectId

siehe: <https://docs.mongodb.com/manual/reference/bson-types/>

## ids

Dokumente bekommen automatisch ein eindeutiges `_id`-Feld:

```js
entry = {
  _id: ObjectId('5e715e1b31315b0be066db84'),
  name: 'Argentina',
  continent: 'South America',
};
```

## BSON Dateiformat

MongoDB basiert auf dem BSON Dateiformat. Dieses ähnelt JSON, ist aber ein binäres Format und lässt sich effizienter lesen und schreiben.

Der Export bzw Import geschieht mittels der Programme `mongodump` und `mongorestore`

# MongoDB Atlas

## MongoDB Atlas

[Atlas](https://www.mongodb.com/cloud/atlas): gehostete MongoDB-Datenbanken von den Entwicklern von MongoDB (Login via Google möglich)

## Atlas cluster setup

- erstelle eine neue Organisation
- erstelle ein neues Projekt (mit Mitgliedern)
- erstelle ein "shared cluster"
  - wähle einen beliebigen Cloud-Provider (_AWS_, _Google_ oder _Azure_)

## Atlas dataset setup

- klicke auf den Clusternamen, für die Detailansicht
- Klicke auf den Tab _Collections_
- Wähle "Load a Sample Dataset" - erstellt 8 Beispieldatenbanken

## Atlas web interface

versuche:

- Erstellen einer weiteren Datenbank
- Erstellen weiterer Collections
- create / update / delete mehrerer Records / Dokumente

# MongoDB Shell

## MongoDB Shell

**MongoDB Shell** = einfaches Befehlszeileninterface für MongoDB

online ausprobieren:

<https://docs.mongodb.com/manual/tutorial/getting-started/>

eine Untermenge der MongoDB shell in reinem JavaScript verwenden (ohne Installation von MongoDB):

<https://github.com/marko-knoebl/mingodb>

## Befehle

wichtige Befehle:

- `.insertOne`
- `.insertMany`
- `.find`
- `.findOne`
- `.updateOne`
- `.replaceOne`
- `.deleteOne`
- `.deleteMany`

## Create

Erstellen eines Eintrags:

```js
db.countries.insertOne({
  name: 'Argentina',
  continent: 'South America',
});
```

## Create

Erstellen mehrerer Einträge:

```js
db.countries.insertMany([
  { name: 'Finland', continent: 'Europe' },
  { name: 'Greece', continent: 'Europe' },
]);
```

## Read

Auslesen aller Elemente:

```js
db.countries.find();
```

Auslesen bestimmter Elemente:

```js
db.countries.find({ continent: 'Europe' });
```

## Read

Auslesen eines einzelnen Eintrags mittels `findOne`:

```js
db.countries.findOne({ name: 'Greece' });
```

## Update

Abändern eines Dokuments - Setzen des Eintrags "population":

```js
db.countries.updateOne(
  { name: 'Argentina' },
  { $set: { population: 44 } }
);
```

## Update

Ersetzen eines Dokuments:

```js
db.countries.replaceOne(
  { name: 'Brazil' },
  { name: 'Brazil', population: 210 }
);
```

## Delete

Löschen eines Dokuments:

```js
db.countries.deleteOne({ name: 'Finland' });
```

Löschen aller Einträge:

```js
db.countries.deleteMany({});
```

## Übung

Erstellen und Ändern einer Kontaktdatenbank

# MongoDB Shell - Details

## Zählen

```js
db.todos.find({ completed: false }).count();
```

## Query selectors

- `$text`
- `$regex`
- `$gt`, `$gte`, `$lt`, `$lte`
- `$in`

## Query selectors

```js
db.products.find({ name: { $text: 'fairphone' } });
db.products.find({
  category: 'phone',
  price: { $lt: 300 },
});
db.products.find({
  category: { $in: ['laptop', 'tablet'] },
  price: { $lt: 400 },
});
```

siehe: <https://docs.mongodb.com/manual/reference/operator/query/>

## Projektionen

Abfragen von bestimmten Feldern:

```js
db.products
  .find({ category: 'phone' })
  .project({ name: 1, price: 1 });
```

liefert nur _name_ und _price_ (und _\_id_) aller Einträge

# SQL Grundlagen

## SQL

SQL = Structured Query Language

Standardisierte Abfragesprache für tabellarische Datenbanken

## SQL Standardisierung

Standardisiert von _ANSI_ und _ISO_ - allerdings weichen Implementierungen oft vom Standard ab

Die beste Unterstützung für standardisiertes SQL bietet wohl _PostgreSQL_

Alte Entwürfe des Standards (kostenlos):

- [SQL 1992 Entwurf (txt, 1.5 MB)](http://www.contrib.andrew.cmu.edu/~shadow/sql/sql1992.txt)
- [SQL 2011 Entwurf (zip von PDFs, 13 MB)](www.wiscorp.com/sql20nn.zip)

## SQL Implementierungen

open source:

- MySQL
- MariaDB
- PostgreSQL
- SQLite

proprietär:

- Oracle
- SQL Server (Microsoft)

[Popularität laut Stackoverflow Developer Survey](https://insights.stackoverflow.com/survey/2019#technology-_-databases)

## SQL ausprobieren

<https://db-fiddle.com> (PostgreSQL, MySQL, SQLite)

<https://www.w3schools.com/sql/trysql.asp?filename=trysql_select_all> (SQLite)

Desktop-Anwendung:

<https://sqlitebrowser.org/> (SQLite)

## Allgemeine SQL Syntax

SQL Statements werden mit `;` beendet

Kommentare sind auf zwei Arten möglich:

```sql
/* mehrzeiliger
Kommentar */
```

```sql
-- einzeiliger Kommentar
```

## Allgemeine SQL Syntax

SQL ist größtenteils _case-insensitive_

Konvention: Keywords groß geschrieben, Rest meist klein und durch Unterstriche getrennt

Beispiel:

```sql
SELECT first_name, last_name, tel FROM person;
```

## Allgemeine SQL Syntax

Tabellen- und Spaltennamen werden von SQL in Großschreibweise konvertiert, z.B. `first_name` → `FIRST_NAME`, `person` → `PERSON`

Ausnahme: In _PostgreSQL_ werden Namen in Kleinschreibweise konvertiert

## Allgemeine SQL Syntax

Sollen Namen in exakter Schreibweise übernommen werden, müssen sie in Anführungszeichen gesetzt werden. Dies ist eher unüblich.

```sql
SELECT "First_Name", "Last_Name", "Tel" FROM "Person";
```

Ausnahme: In _MySQL_ würden hier Backticks (\`) statt Anführungszeichen verwendet werden; hier kann über den Modus `ANSI_QUOTES` ein Standard-kompatibles Verhalten erreicht werden

## Übungsdaten

<https://github.com/datasets>

## Tabellen erstellen

Befehl: `CREATE TABLE`

```sql
CREATE TABLE person(
    name VARCHAR(50),
    tel VARCHAR(20)
);
```

## SQL Datentypen

ISO / ANSI SQL Standard (Auswahl):

- `BOOLEAN`
- `INT` / `INTEGER`, `SMALLINT`, `BIGINT`
- `NUMERIC` / `DECIMAL`
- `REAL`, `DOUBLE PRECISION`
- `VARCHAR(n)`
- `VARBINARY(n)`
- `DATE`, `TIME`, `TIMESTAMP`

## Daten eintragen

```sql
INSERT INTO person (name, tel)
VALUES ('John Smith', '012345');
```

Kurzschreibweise:

```sql
INSERT INTO person
VALUES ('John Smith', '012345');
```

## Daten auslesen

Daten aller Personen auslesen

```sql
SELECT name, tel FROM person;
```

oder

```sql
SELECT * FROM person;
```

## Bedingte Abfragen (WHERE)

```sql
SELECT tel
FROM person
WHERE name = 'John Smith';
```

```sql
SELECT tel
FROM person
WHERE name LIKE '% Smith'
AND tel LIKE '+49%';
```

## Daten eintragen (UPDATE)

```sql
UPDATE person
SET name = 'John Miller'
WHERE name = 'John Smith';
```

## Daten löschen (DELETE)

```sql
DELETE FROM person
WHERE name = 'John Miller';
```

## Übung

Erstellen und Abändern einer Kontaktdatenbank

# MongoDB Schema

## MongoDB Schema

Validierung mittels JSON schema, z.B.:

```js
const elementSchema = {
  bsonType: 'object',
  required: [
    'atomic_number',
    'symbol',
    'name',
    'atomic_mass',
  ],
  properties: {
    atomic_number: {
      bsonType: 'int',
      minimum: 1,
    },
    symbol: {
      bsonType: 'string',
    },
    name: {
      bsonType: 'string',
    },
    atomic_mass: {
      bsonType: 'double',
    },
  },
};
```

## MongoDB Schema

```js
db.createCollection('elements', {
  validator: { $jsonSchema: elementSchema },
});
```

# SQL Schema und Indizes

## Online Tutorial

<https://www.w3schools.com/sql/default.asp>

## Wiederholung: SQL Datentypen

ISO / ANSI SQL Standard (Auswahl):

- `BOOLEAN`
- `INT` / `INTEGER`, `SMALLINT`, `BIGINT`
- `REAL`, `DOUBLE PRECISION`
- `VARCHAR(n)`
- `VARBINARY(n)`
- `DATE`, `TIME`, `TIMESTAMP`

## Boolean

Werden durch die Ausdrücke `TRUE` und `FALSE` repräsentiert

Abweichungen vom Standard:

- _SQL Server_: `BOOLEAN` → `BIT`
- _MySQL_, _SQLite_, _Oracle_: nicht unterstützt, Alternativen z.B. `0` und `1`

## Zahlen

- `SMALLINT` (üblicherweise 16 Bit)
- `INT` / `INTEGER` (üblicherweise 32 Bit)
- `BIGINT` (üblicherweise 64 Bit)
- `NUMERIC` / `DECIMAL` (Dezimalzahlen mit variabler Genauigkeit)
- `REAL` (üblicherweise 32 Bit)
- `DOUBLE PRECISION` (üblicherweise 64 Bit)

Abweichungen vom Standard:

- _MySQL_: `REAL` → `FLOAT`
- _SQLite_: Erlaubt für alle Typen 64 Bit Genauigkeit

## Zahlen

Dezimalzahl mit 10 Stellen vor und 2 Stellen nach dem Komma:

```sql
DECIMAL(12, 2)
```

## Zahlen

MySQL unterscheidet z.B. zwischen:

- `SMALLINT` (-32768 bis 32767)
- `UNSIGNED SMALLINT` (0 bis 65535)

Dies ist nicht Teil des SQL Standards

## Text

- `VARCHAR(10)`: Text der Maximallänge 10

Üblicherweise wird Unicode unterstützt

Bei _SQL Server_ sollte für Unicodeunterstütztung `NVARCHAR` verwendet werden (benötigt doppelt so viel Speicherplatz)

bei _Oracle_ nennt sich der entsprechende Datentyp `VARCHAR2`

Die Maximallänge hat keine Auswirkung auf den Speicherbedarf auf der Festplatte; allerdings kann sie den RAM-Verbrauch beim Lesen und Schreiben beeinflussen

## Text

Text wird _immer_ mit einfachen Anführungszeichen begrenzt.

```SQL
INSERT INTO test VALUES ('Hello');
```

Escapen von einfachen Anführungszeichen durch Verdoppelung:

```SQL
INSERT INTO test VALUES ('Let''s go');
```

## Binärdaten

`VARBINARY(n)`: Bytesequenz mit Maximallänge _n_

Abweichungen vom Standard:

- _PostgreSQL_: `BYTEA`
- _SQLite_: Intern heißt der Datentyp `BLOB`, aber `VARBINARY` wird akzeptiert

## Date, Time

Typen:

- `DATE`: Datum
- `TIME`: Uhrzeit
- `TIMESTAMP`: Datum und Uhrzeit

Beispiele:

- `'2013-02-14'` (empfohlenes Format)
- `'13:02:17'`, `'13:02:17.232'`
- `'2013-02-14 13:02:17'`, `'2013-02-14T13:02:17'`

## Date, Time

Abweichungen vom Standard:

- nicht von `SQLite` unterstützt
- _SQL Server_: `TIMESTAMP` → `DATETIME`

Einschränkungen:

- in _MySQL_ ist `TIMESTAMP` auf Jahre zwischen 1970 und 2038 beschränkt - eine bessere Alternative ist `DATETIME`

## Resourcen zu Datentypen

- [SQLite Datentypen](https://sqlite.org/datatype3.html)
- [Postgres Datentypen](https://www.postgresql.org/docs/current/datatype.html)
- [Postgres SQL Conformance](https://www.postgresql.org/docs/current/features.html)

## Beispiel: Datenbank chemischer Elemente

Einträge:

- _atomic_number_
- _symbol_
- _name_
- _atomic_mass_

## Beispiel: Datenbank chemischer Elemente

```sql
CREATE TABLE element(
    atomic_number INT,
    symbol VARCHAR(2),
    name VARCHAR(20),
    atomic_mass REAL
);
```

## Constraints

Einschränkungen von Spalten:

- `NOT NULL`
- `UNIQUE`
- `PRIMARY KEY`
- (`FOREIGN KEY`)

## Not null

Eintrag darf nicht leer gelassen werden

```sql
CREATE TABLE element(
    atomic_number INT NOT NULL,
    symbol VARCHAR(3) NOT NULL,
    name VARCHAR(20) NOT NULL,
    atomic_mass REAL NOT NULL
);
```

## Unique

Jeder Eintrag in einer Spalte muss einzigartig sein

```sql
CREATE TABLE element(
    atomic_number INT NOT NULL UNIQUE,
    symbol VARCHAR(2) NOT NULL UNIQUE,
    name VARCHAR(20) NOT NULL UNIQUE,
    atomic_mass REAL NOT NULL
);
```

## Primary key

Ermöglicht eindeutige Identifizierung einer Zeile in einer Tabelle

- Sprechender Schlüssel: von Haus aus in den Daten enthalten
- Surrogatschlüssel: zusätzlich hinzugefügter Schlüssel (meist Integerwert)

Ein sprechender Schlüssel ist nur in besonderen Fällen einsetzbar, ein Surrogatschlüssel ist immer möglich

Ein Primary Key ist automatisch _unique_ und _not null_.

## Primary key

Sprechender Schlüssel:

```sql
CREATE TABLE element(
    atomic_number INT PRIMARY KEY,
    symbol VARCHAR(2) NOT NULL UNIQUE,
    name VARCHAR(20) NOT NULL UNIQUE,
    atomic_mass REAL NOT NULL
);
```

## Primary key

Surrogatschlüssel:

```sql
CREATE TABLE element(
    id INT PRIMARY KEY,
    atomic_numer INT,
    symbol VARCHAR(2) NOT NULL UNIQUE,
    name VARCHAR(20) NOT NULL UNIQUE,
    atomic_mass REAL NOT NULL
);
```

## Auto increment

Automatisches Erstellen eines numerischen Primary Keys beginnend bei 1:

Standard SQL (implementiert in PostgreSQL, Oracle):

```sql
CREATE TABLE element(
    id INT PRIMARY KEY GENERATED ALWAYS AS IDENTITY;
    ...
);
```

## Auto increment

Nicht-standardisierte Varianten:

- MySQL: `AUTO_INCREMENT`
- SQLite: `AUTOINCREMENT`
- PostgreSQL: `SERIAL`

In SQLite wird immer automatisch ein numerischer eindeutiger Schlüssel unter dem Namen `rowid` angelegt.

## Indizes in Datenbanken

Generell: geordnete Listen können viel schneller durchsucht werden als ungeordnete (binäre Suche)

Beispiel: im Telefonbuch kann man schnell nach dem Nachnamen einer Person suchen, aber nicht nach dem Vornamen

Auf eine oder mehrere Spalten kann ein Index angewendet werden: Zusätzliche Datenstruktur, die auf die Daten in bestimmter Ordnung verweist.

## Indizes erstellen

```sql
CREATE INDEX idx_name
ON element (name);
```

Es kann nun nach den Elementnamen schneller gesucht werden

## Code: Periodensystem

```sql
CREATE TABLE element(
    atomic_number INT,
    symbol VARCHAR(2) NOT NULL UNIQUE,
    name VARCHAR(20) NOT NULL UNIQUE,
    atomic_mass REAL NOT NULL
);

CREATE INDEX idx_name
ON element (name);

INSERT INTO element(atomic_number, symbol, name, atomic_mass)
VALUES (1, 'H', 'Hydrogen', 1.008);

INSERT INTO element(atomic_number, symbol, name, atomic_mass)
VALUES (2, 'He', 'Helium', 4.0026);

SELECT *
FROM element
WHERE name='Hydrogen';
```

# SQL vs MongoDB 2

## SQL vs MongoDB 2

SQL: Skalierung hauptsächlich vertikal: Hinzufügen von zusätzlichen Resourcen zu einem vorhandenen Server

MongoDB: Skalierung hauptsächlich horizontal: Hinzufügen zusätzlicher Server (via Sharding)

## SQL vs MongoDB 2

SQL: Verwendet _atomare_ Einträge (und erste Normalform)

MongoDB: Enthält oft zusammengesetzte Einträge (Arrays, Objekte):

```json
{
  "name": "sue",
  "groups": ["news", "sports"]
}
```

# Datenbanken - intermediate

## Relationen zwischen Tabellen

- `1 : 1`
- `1 : n`
- `m : n`

## Relationen zwischen Tabellen: Beispiele

- `0..1 : 1..1`  
  department ←manages→ employee  
  Ein Department hat einen Manager; jeder Angestellte managed entweder 0 oder 1 Department
- `0..1 : 0..n`  
  department ←works in→ person  
  Ein Department kann viele Angestellte haben; ein Angestellter kann 0 oder 1 Department zugeteilt sein
- `0..m : 0..n`  
  project ←works on→ person  
  An einem Projekt können mehrere Angestellte arbeiten; ein Angestellter kann an mehreren Projekten arbeiten

## Entity-Relationship-Model

<https://en.wikipedia.org/wiki/Entity%E2%80%93relationship_model>

## ACID

Qualitätsmerkmale einer Datenbank (Sicherheit gegenüber Fehlern):

- _Atomicity_: Daten werden mittels Transaktionen abgeändert, die entweder erfolgreich sind oder als ganzes Fehlschlagen - es wird nie eine Transaktion nur teilweise angewendet
- _Consistency_: Für Inhalte können bestimmte Gültigkeitskriterien festgelegt sein - diese können nicht durch Operationen auf den Daten verletzt werden
- _Isolation_: Parallel laufende Transaktionen beeinflussen einander nicht
- _Durability_: Wenn eine Transaktion vom Datenbanksystem als erfolgreich ausgewiesen wird, muss deren Resultat garantiert dauerhaft vorhanden sein

# SQL Joins

## Beispiel: Musikdatenbank

Tabellen:

- _artist_
- _album_
- _song_

Vorlage: Chinook Musikdatenbank

[GitHub](https://github.com/lerocha/chinook-database)

[Inspektor auf SchemaSpy](http://schemaspy.org/sample/index.html)

## Beispiel: Musikdatenbank - artist

```sql
CREATE TABLE artist(
    id INT PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
    name VARCHAR(20) NOT NULL,
    country VARCHAR(5) NOT NULL,
    year SMALLINT NOT NULL
);

INSERT INTO artist (name, country, year)
VALUES ('The Beatles', 'UK', 1960);

INSERT INTO artist (name, country, year)
VALUES ('AC/DC', 'AUS', 1973);
```

## Foreign key

Referenz auf jeweils einen Eintrag einer anderen Tabelle

z.B.: Jeder Eintrag in der Tabelle _song_ kann über die Spalte _artist_id_ mit der Tabelle _artist_ verknüpft werden

Der Zusatz `FOREIGN KEY(column) REFERENCES other_table(column)` garantiert, dass ein entsprechender Eintrag in der anderen Tabelle existiert

## Foreign key

```sql
CREATE TABLE song(
    id INT PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
    title VARCHAR(30) NOT NULL,
    artist_id INT,
    FOREIGN KEY(artist_id) REFERENCES artist(id)
);

INSERT INTO song (title, artist_id)
VALUES ('Let it Be', 1);
```

## Foreign key

Ein foreign key garantiert, dass ein entsprechender Eintrag in der zugehörigen anderen Tabelle existiert

```sql
INSERT INTO song (title, artist_id)
VALUES ('Wish You Were Here', 10);
```

→ Fehlermeldung

## Tabellen verknüpfen (INNER JOIN)

```sql
SELECT song.title, artist.name
FROM artist
INNER JOIN song
ON artist.id=song.artist_id;
```

Der obige Code listet alle Kombinationen auf, bei denen `artist.id` und `song.artist_id` übereinstimmen

## Tabellen verknüpfen (LEFT JOIN)

```sql
SELECT song.title, artist.name
FROM song
LEFT JOIN artist
ON artist.id=song.artist_id;
```

Der obige Code listet alle Kombinationen auf und beinhaltet auch Lieder, für die kein Künstler definiert ist

# Datenabfrage - Beispiele

## Alle Daten abfragen

SQL:

```sql
SELECT * FROM iris;
```

SQLAlchemy (Python):

```sql
session.query(Iris)
```

mongo shell (JS):

```js
db.iris.find({});
```

Pandas (Python): N/A

## Bestimmte Spalten / Felder abfragen

SQL:

```sql
SELECT sepal_length, sepal_width FROM iris;
```

SQLAlchemy (Python):

```sql
session.query(Iris.sepal_length, Iris.sepal_width)
```

## Bestimmte Spalten / Felder abfragen

Mongo shell:

```js
db.iris.find({}, { sepal_length: 1, sepal_width: 1 });
```

Pandas:

```py
iris_data.loc[:,["sepal_length", "sepal_width"]]
```

## Bestimmte Einträge finden

SQL:

```sql
SELECT * FROM iris WHERE name='Iris-setosa';
```

SQLAlchemy (Python):

```py
session.query(Iris).filter(Iris.name="Iris-setosa")
```

## Bestimmte Einträge finden

mongo shell:

```js
db.iris.find({ name: 'Iris-setosa' });
```

pandas (Python):

```py
iris_setosa_data = iris_data.loc[
    iris_data["name"] == "Iris-setosa"
]
```

pandas (Python): Eine Reihe von Einträgen auswählen:

```py
iris_data.iloc[10:20]
```

## Einträge und Felder auswählen

SQL:

```sql
SELECT sepal_length, sepal_width
FROM iris
WHERE name='Iris-setosa';
```

## Einträge und Felder auswählen

SQLAlchemy (Python):

```py
session.query(
    Iris.sepal_length, Iris.sepal_width
).filter(Iris.name="Iris-setosa")
```

## Einträge und Felder auswählen

mongo shell:

```js
db.iris.find(
  { name: 'Iris-setosa' },
  { sepal_length: 1, sepal_width: 1 }
);
```

## Einträge und Felder auswählen

pandas (Python):

```py
iris_data.loc[
    [iris_data["name"] == "Iris-setosa"],
    ["sepal_length", "sepal_width"],
]
```

## Einträge sortieren

SQL:

```sql
SELECT sepal_length, sepal_width
FROM iris
ORDER BY sepal_length;
```

## Einträge sortieren

SQLAlchemy:

```py
session.query(
    Iris.sepal_length, Iris.sepal_width
).order_by(Iris.sepal_length)
```

## Einträge sortieren

mongo shell:

```js
db.iris
  .find({}, { sepal_length: 1, sepal_width: 1 })
  .sort({ sepal_length: 1 });
```

## Einträge sortieren

pandas (Python):

```py
iris_data.loc[["sepal_length", "sepal_width"]].sort_values(
    by="sepal_length"
)
```
