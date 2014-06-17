ndm
===

ndm makes it easy to deploy a complex service-oriented-architecture, by allowing you to create
daemon processes directly from npm packages.

What It's Not
-------------

ndm doesn't aim to replace Puppet, Chef, or Ansible. I'd recommend using one of these tools to
prime your servers for ndm deployment:

* installing your services dependent technologies, e.g., ImageMagick, nginx, nagios.
* installing the version of Node.js that your service requires.
* distributing the SSH keys necessary for deployment.

What It Is
----------

* ndm makes it easy to generate process daemons from npm packages.
* ndm helps you setup the environment for these daemons.

Making a Service ndm Deployable
-------------------------------

* add an **environment** stanza to your _package.json_, and specify environment variables
that your service requires, and their default values: When someone deploying your service
runs `ndm install my-dependency --save`, these default values will be populated in config.json.

```json
{
  "name": "ndm",
  "version": "0.0.0",
  "description": "An npm-backed deployment tool.",
  "main": "index.js",
  "scripts": {
    "start": "node ./bin/awesome.js"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "ssh2": "^0.2.25"
  },
  "devDependencies": {
    "mocha": "^1.20.0"
  },
  "environment": {
    "PORT": 80,
    "USERNAME": "foo",
    "PASSWOR": "bar"
  }
}
```

* make sure you have a **start** script in your __package.json__, this is what will be run by ndm.

Anatomy of an ndm Deployment Directory
--------------------------------------

An ndm deployment directory consists of:

* a **package.json** file (generated using npm), describing the set of services that you intend to deploy.
* an **service.json** file, used to specify meta-information about the service being deployed.
  * **environment variables**
  * **process counts**, how many copies of the service should be run?

service.json
-----------

A typical **service.json** file looks something like this:


```json
{
  "npm-www-1": {
    "module": "npm-www",
    "bin": "./test.js",
    "env": {
      "PORT": "5000",
    },
    "args": {
      "--verbose": "true"
    }
  },
  "npm-www-2": {
    "bin": "./test.js",
    "module": "npm-www",
    "env": {
      "PORT": "5001",
    }
  },
  "env": {
    "COUCH": "http://registry.example.com"
  }
}
```

* **service-name:** name to associate with the daemon script:
  * **module:** npm-module the daemon executes.
  * **env:** process-specific environment variables.
  * **bin:** the script to execute with *node*, e.g., `./bin/my-app.js`.
  * **args:** command line arguments to pass into the script being executed.
* **env:** environment variables shared across services.
