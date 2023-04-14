---
title: npm - using a higher version nodejs to run code built by a lower version nodejs
date: 2023-04-14 10:50:56
tags: npm
categories: npm
---

If you're upgrading Node.js to version 18 and your old code repository, built using an earlier version of Node.js, is no longer running properly due to changes in the dependencies within the node_modules folder, don't worry. Let's explore some solutions to fix this problem.

## Can't Install Properly

If you're having trouble running `npm install` properly, and you have already tried running `npm cache clean --force`, deleting `node_modules` folder, `package-lock.json` file and then running `npm install` again, but still encounter issues due to dependencies version conflict in the new peer dependencies' rule. You can try this command instead:

```shell
npm install --lagacy-peer-deps
```

<!-- more -->

## Can't Run Properly

After installing everything, you might still encounter an error when running `npm start` or `npm run serve`: `Error: error:0308010C:digital envelope routines::unsupported`.

### Reason For The Error

In Node.js version 17, the Node.js developers closed a security hole in the SSL provider, which resulted in a breaking change that affected the SSL packages in NPM. If you use SSL in Node.js version 17 or later without upgrading the SSL packages in your package.json, you may encounter this error.

### The Safe Solution (for npm users)

Use an up-to-date version of Node.js and also use packages that are up-to-date with security fixes.

For many people, the following command will fix the issue:

```shell
npm audit fix --force
```

However, for complex builds, the above command may pull in breaking security fixes that could potentially break your build.

### Don't Want To Upgrade All Package Versions?

You can try one of these:

1. Downgrade to Node.js version 16 (Reinstall or use `nvm`).

2. Enable the legacy OpenSSL provider.

On Unix-like systems (Linux, macOS, Git bash, etc.), use:

```shell
export NODE_OPTIONS=--openssl-legacy-provider
```

On Windows command prompt, use:

```shell
set NODE_OPTIONS=--openssl-legacy-provider
```

On PowerShell, use:

```shell
$env:NODE_OPTIONS = "--openssl-legacy-provider"
```

> References
>
> [Error message "error:0308010C:digital envelope routines::unsupported" - stackoverflow](https://stackoverflow.com/questions/69692842/error-message-error0308010cdigital-envelope-routinesunsupported)
