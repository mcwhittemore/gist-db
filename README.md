# :cloud: Gist-DB :cloud:

Treat your gist account like a database. Powered by [TaffyDB][taffydb-link]
and [Github][node-github-link].

[![npm downloads](https://img.shields.io/npm/dm/gist-db.svg)](https://www.npmjs.com/package/gist-db)
[![npm version](https://img.shields.io/npm/v/gist-db.svg)](https://www.npmjs.com/package/gist-db)
[![js-standard-style](https://img.shields.io/badge/code%20style-standard-brightgreen.svg)](http://standardjs.com/)

## Install

```sh
npm install --save gist-db
```

## Usage

```js
var config = {
  github: {
    username: "mcwhittemore"
  }
}

var db = require("gist-db")(config);

db.event.on('refreshing', function(){
  //MIGHT WANT TO LOCK DOWN THINGS FOR A BIT
  console.log("LETS DO THIS");
});

db.event.on('refreshed', function(err){
  db().each(function(file){
    console.log(file);
  });
});
```

## File Object structure

```js
{
  id = gist_id + "_" + filename,
  filename = filename,
  gist_id = gist_id,
  error: undefined,
  raw: "THE RAW VALUE OF THE FILE",
  type: "mime type",
  language: "language the file is written in",
  raw_url: "https path to the raw text version of the file",
  size: numeric size of the file,
  gist: {
    id: gist_id
    public: boolean,
    created_at: date object,
    updated_at: date object,
    description: "the gist description"
  }
}
```

## Demo

```sh
# Clone the repository
git clone https://github.com/pinn3/gist-db

# Run the demo! This will also install dependencies and run the build script
npm run demo
```

## API

Please refer to the [TaffyDB docs](http://www.taffydb.com/workingwithdata) for 
more details

### GISTDB(config, fileInit, fileSave)

Create a new gist-db.

**Parameters**

*   config: A settings object. 

```js
Required: {
  github:{
    username:"SOME_USER_NAME"
  }
}

Defaults: {
  refreshMin: 10,
    github: {
      per_page: 100,
      timeout: 5000,
      version: "3.0.0"
    },
    local: {
      save: "NEVER", //NEVER, ON_REFRESH, ALWAYS 
      location: undefined
    }
}

Available: {
  refreshMin: 10,
  github: {
    per_page: 100,
    timeout: 5000
    version: "3.0.0",
    authenticate: {
      type: "basic or oauth",
      username: "your_username_if_basic",
      password: "your_password_if_basic",
      token: "your_oauth_token_if_oauth"
    },
    local: {
      save: "NEVER OR NEVER OR ON_REFRESH OR ALWAYS"
      location: "path and filename"
    }
  }
}
```

*   fileInit: function(file). A function that returns the file obj if it should
    be added to the DB and undefined if it should be excluded.

*   fileSave: function(file, callback). A function that allows for further
    parameter work on files after the raw data has been received. A functioning
    implementation of this MUST pass the file object as a parameter to callback
    to save changes to the DB. Note: this will perform an update to the 
    database.

### db({field:value})

*   TaffyDB: Yes

*   Returns: All rows that meet the passed criteria. Not passing an object,
    will return all rows.

### db.insert({})

Inserts records into the database.

*   TaffyDB: Yes
*   Returns: A query pointing to the inserted records

### db.github

Full use of the github module passed the github subsection of your config file.

### db.event

An implementation of require("events").EventEmitter

### db.event.on('refreshing', function(){})

Use to be notified when gist-db is connecting gist for a refresh.

### db.event.on('refreshed', function(err){})

Use to be notified when gist-db is done its current refresh. If err is set,
this refresh was ended due to error.

### db.event.on('file_error', function(err, file){})

Use to be notified of errors in gathering data on the gist files.

**Parameters**

*   err: The error object that triggered this event
*   file: The file object that was being gathered when the error occurred

### db.event.on('github_error', function(err, res){})

Use to be notified of errors when connecting with github.

**Parameters**

*   err: the github module error object that triggered this event

*   res: The github module response object. Might contain good data about the
    error.

## Things to be done

### 0.1.10

*   Add tests

### 0.2.0

*   Add Update gist.github
*   Add Insert gist.github
*   Add Delete gist.github

## Licenses

All code not otherwise specified is released under the MIT License.

All code found in the node_modules directory is Copyrighted by its creators. 
Please see each module for further details.

[taffydb-link]: http://www.taffydb.com/
[node-github-link]: https://github.com/mikedeboer/node-github
