The library provide smart helpers for standard operations integrated with PostgreSQL.

# Initialization

```javascript
require('@sharaal/sql-helper-pg')(client)
```

# Examples

## SELECT

The return value is `result.rows` of the pg result object.

### All columns

```javascript
const rows = await client.select(
  'table',
  { columnA: 'valueA', columnB: 'valueB', columnC: 'valueC' }
)

// text: SELECT "*" FROM "table" WHERE "columnA" = $1 AND "columnB" = $2 AND "columnC" = $3
// parameters: ['valueA', 'valueB', 'valueC']
```

### Only the list of the given columns

```javascript
const rows = await client.select(
  'table',
  ['columnA', 'columnB', 'columnC'],
  { column: 'value' }
)

// text: SELECT "columnA", "columnB", "columnC" FROM "table" WHERE "column" = $1
// parameters: ['value']
```

## INSERT

### Single row

The return value is the SERIAL generated by inserting the new row located in `result.rows[0][serialColumn = 'id']` of the pg result object.

```javascript
const id = await client.insert(
  'table',
  { columnA: 'valueA', columnB: 'valueB', columnC: 'valueC' }
)

// text: INSERT INTO "table" ("columnA", "columnB", "columnC") VALUES ($1, $2, $3) RETURNING "id"
// parameters: ['valueA', 'valueB', 'valueC']
```

### Multiple rows

The return value is an array of the SERIALs generated by inserting the new row located in `result.rows[][serialColumn = 'id']` of the pg result object.

```javascript
const ids = await client.insert(
  'table',
  [
    { columnA: 'valueA1', columnB: 'valueB1', columnC: 'valueC1' },
    { columnA: 'valueA2', columnB: 'valueB2', columnC: 'valueC2' },
    { columnA: 'valueA3', columnB: 'valueB3', columnC: 'valueC3' }
  ]
)

// text: INSERT INTO "table" ("columnA", "columnB", "columnC") VALUES ($1, $2, $3), ($4, $5, $6), ($7, $8, $9) RETURNING "id"
// parameters: ['valueA1', 'valueB1', 'valueC1', 'valueA2', 'valueB2', 'valueC2', 'valueA3', 'valueB3', 'valueC3']
```

### Returning another serial column

```javascript
const example = await client.insert(
  'table',
  { columnA: 'valueA', columnB: 'valueB', columnC: 'valueC' },
  'example'
)

// text: INSERT INTO "table" ("columnA", "columnB", "columnC") VALUES ($1, $2, $3) RETURNING "example"
// parameters: ['valueA', 'valueB', 'valueC']
```

## UPDATE

The return value is the count of the rows affected by the update located in `result.rowCount` of the pg result object.

```javascript
const rowCount = await client.update(
  'table',
  { columnA: 'new valueA', columnB: 'new valueB', columnC: 'new valueC' },
  { columnA: 'old valueA', columnB: 'old valueB', columnC: 'old valueC' }
)

// text: UPDATE "table" SET "columnA" = $1, "columnB" = $2, "columnC" = $3 WHERE "columnA" = $4 AND "columnB" = $5 AND "columnC" = $6
// parameters: [ 'new valueA', 'new valueB', 'new valueC', 'old valueA', 'old valueB', 'old valueC']
```

## DELETE

The return value is the count of the rows affected by the delete located in `result.rowCount` of the pg result object.

```javascript
const rowCount = await client.delete(
  'table',
  { columnA: 'valueA', columnB: 'valueB', columnC: 'valueC' }
)

// text: DELETE FROM "table" WHERE "columnA" = $1 AND "columnB" = $2 AND "columnC" = $3
// parameters: ['valueA', 'valueB', 'valueC']
```
