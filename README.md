# Tetrate Trader App

>Desc...

# Tetrate Trader

Microservice version of the Spring Trader app using spring boot and Tetrate Service bridge (on kubernetes)

![Spring Trader](/docs/springtrader2.png)

# Introduction

This repository holds a collection of micro services that work together to present a trading application surfaced though a web UI, but more interfaces can be created that re-utilise the microservices.

## Table of Contents

1. [Architecture](#architecture)
2. [Deploying the application](#deployment)
3. [Workshops](#workshops)
4. [Demos](#demos)
5. [Roadmap](#roadmap)
6. [Contributing to the project](#contributing)


# Architecture
The system is composed of 5 microservices. The relationship between the microservices is illustrated below.

![architecture](/docs/base-architecture-diagram.png)

## 1. Quote Microservice
This service is a spring boot application responsible for providing up to date company and ticker/quote information. It does this by providing a REST api with 2 calls:
* ``/v1/quotes?q={symbol1,symbol2,etc}``
Returns as up to date quote for the given symbol(s).
* ``/v1/company/{search}``
Returns a list of companies that have the search parameter in their names or symbols.

This application has no dependencies apart from an external service - [markitondemand](http://dev.markitondemand.com/) - to retrieve the real time data.

## 2. Account Microservice
This service is a spring boot application responsible for creating and managing bank accounts.

It stores the accounts in a RDBMS store and uses a spring JPA respository to accomplish this. It provides several REST api calls for other services to consume its services.

## 3. Portfolio Microservice
This service is a spring boot application responsible for managing portfolios - these are collections of holdings, which in turn are collection of orders on a particular share.

This service accepts orders (both BUY and SELL) and stores these in a RDBMS store - *it does not have to be the same RDBMS as the Account service, but it can be!* It provides REST api calls for other services to consume its services.

This service is dependent on the Account service above to ensure the logged in user has enough funds to buy stock as well as keeping the account funds up to date. It is also dependent on the Quote service to retrieve up to date quote information and calculate the current value of portfolios.

## 4. Web Microservice
This service is a spring boot application providing the web interface.

The web interface is built using bootstrap and Thymeleaf and uses a Spring controller to delegate calls to the relevant services:
* Account service
* Quote service
* Portfolio service
* User service

## 5. User Microservice
This service is a spring boot application providing the user services.

It integrates with cloud foundry UAA - https://github.com/cloudfoundry/uaa.

# Deployment

To deploy the microservices manually please follow the guides of the [workshop below](#workshops)

Or if you want to quickly deploy all services to Pivotal Cloud Foundry with [Spring Cloud Services for PCF](https://network.pivotal.io/products/p-spring-cloud-services) follow the [scripted instructions](scripts/README.md)

Alternative, you can run most of the functionality locally, by following the [local instructions](docs/lab_local.md)