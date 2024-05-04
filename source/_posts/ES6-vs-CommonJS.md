---
title: ES6 vs CommonJS
date: 2024-04-19 14:01:59
categories:
  - [Web Development]
tags:
  - Frontend
---

### Syntax and Module Loading

- CommonJS modules are **synchronously** loaded, meaning that the module files are loaded and parsed during the runtime as the code executes. This approach is well-suited for ***server-side*** environments where files are typically locally available and can be loaded quickly.

```javascript
const moduleA = require('moduleA');
module.exports = function() {
  // some functionality
};
```

In CommonJS, the server should load the module in order and then execute. Since files are stored locally on the server, the process won't take long usually.

```javascript
// server.js
const userModule = require('./user');  // 加载用户模块
const dbModule = require('./database'); // 加载数据库模块

userModule.createUser();
dbModule.connect();
```

- ES6 modules are designed to support **asynchronous** loading, allowing modules to be loaded over the network. This feature is advantageous in ***browser environments***, enabling scripts to be loaded in parallel while the page loads.

```javascript
import moduleA from 'moduleA';
export function someFunction() {
  // some functionality
};
export default someFunction;
```

Imagine you are browsing a webpage with complex frontend, ES6 enables asynchronous loading, which means before some slow modules being loaded thorouly, you can see and interact with modules that are already loaded.

```javascript
// app.js
import { fetchUserData } from './user.js';  // 异步加载用户模块
import { connectToDb } from './database.js'; // 异步加载数据库模块

fetchUserData();
connectToDb();
```

### Design Philosophy

CommonJS was designed with server-side applications in mind, where modules are loaded and parsed as needed.

ES6 modules are designed to allow static analysis at compile time, supporting static optimizations and more complex import/export patterns, such as partial imports (tree shaking) and dynamic imports.

### Interoperability

In modern JavaScript development, Node.js environments have started to support ES6 module syntax, but this typically requires specific configuration (such as using the .mjs file extension or setting "type": "module" in "package.json"). This enables the use of ES6 module syntax in Node.js, while also supporting the import of CommonJS modules.

Nowadays, ES6 has been widely used on server side too. It depends on the circumstances to make choice between them.