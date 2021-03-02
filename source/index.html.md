---
title: IBM Planning Analytics Workspace UI API

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

toc_footers:
  - <a href='https://www.ibm.com/support/knowledgecenter/SSD29G_2.0.0/com.ibm.swg.ba.cognos.tm1_prism_gs.2.0.0.doc/pa_paw.html'>Planning Analytics Workspace documentation</a>

includes:
  - endpoints
  - clientcommunication


search: true
---

# Introduction

Use the IBM Planning Analytics Workspace UI API to return generated Planning Analytics Workspace assets. You can return assets such as cube views, the dimension editor, the set editor, and TM1 views.

The Planning Analytics Workspace UI API is developed and built using the <a href='https://github.com/nestjs/nest'>Nest</a> framework.

Some code examples contain `http(s)://<Planning Analytics Workspace URL>`. If you are using these code examples, be sure to replace `<Planning Analytics Workspace URL>` with your own Planning Analytics Workspace URL.

# Logging in

You can access the Planning Analytics Workspace UI API in several different ways. If you are accessing the API from the `/ui` path, you will be prompted to log in using a pop up dialog box. However, there may be other scenarios where you will need to log in using the POST method. For these methods, use `/login` in the POST header.

## Negotiate authentication

To use the Negotiate authentication method, Planning Analytics Workspace must be configured to use a TM1 authentication server that supports Negotiate authentication (mode 2 and 3).

### Body
Include the following in the body of your login POST request:

JSON: `{ “mode”: “negotiate” }`

FORM: `mode=negotiate`

## Cognos CAM passport

To use the Cognos CAM passport for login, Planning Analytics Workspace must be configured to use CAM authentication or use a TM1 authentication server that is configured with CAM (mode 5).

### Body
Include the following in the body of your login POST request:

JSON: `{ “camid”: “CAM-PASSPORT-COOKIE-VALUE” }`

FORM: `camid=CAM-PASSPORT-COOKIE-VALUE`

## Cognos CAM namespace

To use the Cognos CAM namespace for login, Planning Analytics Workspace must be configured to use CAM authentication or use a TM1 authentication server that is configured with CAM (mode 5).

### Body
Include the following in the body of your login POST request:

JSON: `{ “username”: “TM1-OR-COGNOS-USER”, “password”: “PASSWORD” }`

FORM: `username=URLENC-TM1-OR-COGNOS-USER&password=URLENC-PASSWORD`

## Cognos CAM or TM1 native authentication

To use Cognos CAM or TM1 native authentication for login, Planning Analytics Workspace must be configured to use CAM with a preset namespace or configured to use a TM1 authentication server that uses native authentication (mode 1 or 2).

### Body
Include the following in the body of your login POST request:

JSON: `{ “namespace”: “CAM-NAMESPACE-ID”, “username”: “COGNOS-USER”, “password”: “PASSWORD” }`

FORM: `namespace=URLENC-CAM-NAMESPACE-ID&username=URLENC-COGNOS-USER&password=URLENC-PASSWORD`

# Login tokens

The Planning Analytics Workspace UI API accepts `x-csrf-token` and `ba-sso-authenticity` fields in the request header. After you send the login POST request, you will receive a response that contains an `x-csrf-token`. You can use the `x-csrf-token` in the header of future requests.
 