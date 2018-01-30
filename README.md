# Webph.one server

This is a WebApp for connecting internet enabled devices to community cellular networks.

This repository will easen newcomers the setup of the whole infrastructure;

The system is composed of a set of server modules and a client app.

The server modules use several Open Source tools to achieve the desired result, namely:
* Kamailio for SIP signaling
* RTPEngine as RTP Server (for audio tunneling between clients)
* nginx as Web Server
* webpush-server: Push notifications to announce incoming calls
* number-request-server: Server that manages new number assignments
* MySQL: to manage all data
* LetsEncrypt SSL certificates

The client module is a Progressive-web-app (PWA) that is served by nginx.

# Architecture

This describes the main relations between the modules of the system:
* The nginx web server, using the LetsEncrypt SSL Certificate, exposes a public secure web service that serves the webph.one PWA in a certain DNS domain (for example https://example.webph.one/).
* When a user gets to the url https://example.webph.one/ using his web browser, he installs the PWA as an app in his phone.
* If it is the PWA's first run, it will request the number-request-server a phone number for that device, so he can use that to call and be called.
* The number-request-server will save in MySQL Database all information regarding new numbers registered and the credentials the phone uses to identify them.
* The PWA will connect to the Kamailio SIP Server and register with Google Push Notifications Service to receive notifications of incoming calls.
* The Kamailio SIP Server will talk with the MySQL Database to check the credentials of the connected user.
* When the user triggers a call, a message is sent to Kamailio to notify the other callee that is being called.
* If the call goes to a phone that is connected to a web client, webpush-server is used to send the push notification through Google's push notification service.
* If the call goes to a gsm phone belonging to a Kamalio configured community, Kamailio will forward the call to the Community's SIP Server.
* If the callee accepts the call, Kamailio coordinates with the clients and RTPEngine to create an RTP Media Tunnel between the clients to pass the audio through it.

# Requirements

In order to run the system, you will need docker-compose installed.

# Installation

Clone the repository by doing:

```bash
git clone --recursive https://github.com/saycel/Saycel.Phone.git
```

You will need to modify the following files with your Domain specific information.
All these files will contain variables for ease of use.

A dot-env file is provided as example, please copy it to .env and customize before running.

```bash
cp dot-env .env
```

To start the system run:

```bash
docker-compose up
```
