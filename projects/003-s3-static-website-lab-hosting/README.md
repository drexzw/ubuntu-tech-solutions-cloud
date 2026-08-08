# AWS S3 Static Website Hosting

## Overview

This lab demonstrates how to host a static website using **Amazon Simple Storage Service (Amazon S3)**.

The objective was to create an S3 bucket, configure it for static website hosting, upload website files, configure public access, and verify that the website could be accessed through its S3 website endpoint.

This project was completed as part of my AWS Cloud learning journey and is intended to demonstrate practical experience with AWS storage, permissions, and static website deployment.

---

## Objectives

* Create an Amazon S3 bucket.
* Configure the bucket for static website hosting.
* Configure public access permissions.
* Upload static website files.
* Configure an index document.
* Access and verify the deployed website.
* Document the deployment process and troubleshooting steps.

---

## Technologies Used

* Amazon Web Services (AWS)
* Amazon S3
* HTML
* Web Browser
* AWS Management Console

---

## Architecture

The deployment uses a simple static hosting architecture:

```text
User
  |
  v
Web Browser
  |
  v
Amazon S3 Static Website Endpoint
  |
  v
S3 Bucket
  |
  +-- index.html
  +-- CSS / Images / Other Static Files
```

---

## Deployment Summary

1. Created an S3 bucket.
2. Configured the bucket for static website hosting.
3. Configured public access settings.
4. Added the required bucket policy.
5. Uploaded the website files.
6. Configured `index.html` as the index document.
7. Accessed the S3 website endpoint.
8. Verified that the website loaded successfully.

---

## Screenshots

Screenshots taken during the lab are stored separately.

**Screenshots:**
[View Screenshots](./screenshots/)

---

## Key AWS Concepts Demonstrated

### Amazon S3

S3 provides object storage that can also be used to host static websites.

### Static Website Hosting

S3 can serve static files such as:

* HTML
* CSS
* JavaScript
* Images

### Bucket Permissions

Public access and bucket policies determine whether users can access objects stored in the bucket.

### Website Endpoint

When static website hosting is enabled, S3 provides a website endpoint that can be used to access the deployed site.

---

## Result

The static website was successfully deployed to Amazon S3 and accessed through the S3 website endpoint.

This lab provided hands-on experience with AWS S3 configuration, object storage, permissions, and basic cloud-based website deployment.

---

## Documentation

* [Commands](./commands.md)
* [Deployment Notes](./deployment-notes.md)
* [Screenshots](./screenshots/)
