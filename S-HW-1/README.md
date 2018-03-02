## Local DB System

### Goal

Implement a simplified mongoDB similar DB system but local only.

### API

``` typescript

type Default = any;

export class Db {
    constructor(databaseName: string, options?: DbCreateOptions);
    collection<TSchema = Default>(name: string): Collection<TSchema>;
    collections(): Promise<Collection<Default>[]>
    dropCollection(name: string, callback: MongoCallback<boolean>): void;;
    dropCollection(name: string): Promise<boolean>;
    dropDatabase(): Promise<any>;
    dropDatabase(callback: MongoCallback<any>): void;
}

export interface DbCreateOptions {
    // Specify if the should ignore undefined fields or not.
    ignoreUndefined?: boolean;
    // ES6 compatible promise constructor
    promiseLibrary?: Object;
    // db size: in MB
    dbSize?: number
}

export interface MongoCallback<T> {
    (error: MongoError, result: T): void;
}

export interface MongoCountPreferences {
    // The limit of documents to count.
    limit?: number;
    // The number of documents to skip for the count.
    skip?: boolean;
}

export interface FindOneOptions {
    limit?: number,
    sort?: Array<any> | Object
}

export interface InsertOneWriteOpResult {
    insertedCount: number;
    insertedId: ObjectID;
    connection: any;
    result: { ok: number, n: number }
}

export interface WriteOpResult {
    connection: any;
    result: any;
}

export interface Collection<TSchema = Default> {
    // Get the collection name.
    collectionName: string;
    // Get the full collection namespace.
    namespace: string;
    count(query: Object, callback: MongoCallback<number>): void;
    count(query: Object, options?: MongoCountPreferences): Promise<number>;

    drop(): Promise<any>;
    drop(callback: MongoCallback<any>): void;

    find<T = TSchema>(query?: Object): void | any[];

    findOne<T = TSchema>(filter: Object, callback: MongoCallback<T | null>): void;
    findOne<T = TSchema>(filter: Object, options?: FindOneOptions): Promise<T | null>;

    insert(docs: Object, callback: MongoCallback<InsertOneWriteOpResult>): void;
    insert(docs: Object): Promise<InsertOneWriteOpResult>;

    remove(selector: Object, callback: MongoCallback<WriteOpResult>): void;
    remove(selector: Object): Promise<WriteOpResult>;

    update(filter: Object, update: Object, callback: MongoCallback<WriteOpResult>): void;
    update(filter: Object, update: Object, options?: { multi?: boolean }): Promise<WriteOpResult>;
}
```

### Usage

``` javascript
const db = new DB("myDB", { ignoreUndefined: true });
const myCollection = db.collection("myCollection");

// insert a doc
myCollection.insert({
    name: "Tao",
    gender: "male"
}, (err, data) => {
    if (err) throw err;
    console.log(data);
    // { insertedCount: 1, insertedId: 1, connection: Object{...}, result: { ok: 1, n: 1 } }
});

// insert a doc and return promise
myCollection.insert({
    name: "Tao",
    gender: "male"
}).then(data => {
    console.log(data);
    // { insertedCount: 1, insertedId: 1, connection: Object{...}, result: { ok: 1, n: 1 } }
}).catch(err => {
    console.log(err);
    throw err;
});
```