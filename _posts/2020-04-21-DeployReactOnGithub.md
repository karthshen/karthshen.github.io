---
layout:     post
title:      "Deploy React Web App on GitPage"
subtitle:   " \"If the gitpage doesn't refresh with most recent version of the app\""
date:       2020-04-21 15:43:00
author:     "Karth"
header-img: "img/home-bg-geek.jpg"
catalog: true
tags:
    - git
    - react
---

# Tutorial 

## Pre-requisite 
- Install the appropriate version of node, and check the version using `node --version`

- Install the appropriate version of npm, check the version using `npm --version`

## Procedure of Deployment

1. Install the `gh-pages` package as a **`dev-dependency`** of the app

    ```
    $ npm install gh-pages --save-dev
    ```
2. Modify `package.json`

    ```
    //...
    "homepage": "http://gitname.github.io/react-gh-pages"
    ```
    Add follwing under `script` property
    ```
    "scripts": {
        /...
        "predeploy": "npm run build",
        "deploy": "gh-pages -d build"
    }
    ```

3. Generate production build for the app, and deploy it to Github Pages using 

    ```
    $ npm run deploy
    ``