---
layout: post
title: "Intro: Django - From Heroku To Azure"
categories: django, azure
---

Recently had to switch hosting for a Django app from Heroku to Azure. This is a log of what the move involved.

What I started with:

- Django 1.10
- Database: PostgreSQL
- Email functionality: Mailgun
- Search: Websolr
- Cronjobs: Heroku Scheduler
- File Storage: Amazon S3
- Deployment: Heroku's GIT workflow

The final Azure setup was composed out of:

- Django 1.10 as Azure Web Application
- Database: Azure SQL
- Email functionality: Outlook365 via Microsoft's Graph
- Search: Azure Search
- Cronjobs: Azure Web Application Webjobs
- File Storage: Azure File Storage
- Authentication: Azure Active Directory
- Deployment: Azure's Web Application GIT workflow, along with Azure Resource Manager for deploying the infrastrucure

This will be a series of posts, each detailing one aspect of hosting move.


