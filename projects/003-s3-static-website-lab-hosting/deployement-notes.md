# S3 Static Website Hosting - Deployment Notes

## 1. Create the S3 Bucket

Created a new S3 bucket through the AWS Management Console.

The bucket was created specifically for hosting the static website.

**Bucket name:** `YOUR-BUCKET-NAME`

**Region:** `YOUR-AWS-REGION`

---

## 2. Configure Public Access

The bucket's public access configuration was reviewed.

For static website hosting, public access must be configured appropriately so that website visitors can retrieve the objects.

The **Block all public access** setting was already disabled because it had been configured earlier during bucket creation.

No additional change was required at this stage.

---

## 3. Enable Static Website Hosting

The bucket was configured for static website hosting.

The website configuration used:

**Index document:**

```text
index.html
```

If an error document was configured:

```text
error.html
```

---

## 4. Upload Website Files

The website files were uploaded into the S3 bucket.

The primary website file was:

```text
index.html
```

Additional CSS, JavaScript, image, or other static files were uploaded as required by the website.

---

## 5. Configure Bucket Permissions

A bucket policy was configured to allow public read access to the objects required by the static website.

The policy allows visitors to retrieve objects from the bucket using:

```text
s3:GetObject
```

The policy applies to objects within the bucket.

---

## 6. Verify Website Deployment

After the configuration was completed, the S3 website endpoint was opened in a web browser.

**Website Endpoint:**

`YOUR-S3-WEBSITE-ENDPOINT`

The website loaded successfully, confirming that the deployment was working.

---

## 7. Verification Checklist

* [x] S3 bucket created
* [x] Static website hosting enabled
* [x] Index document configured
* [x] Public access configuration reviewed
* [x] Bucket policy configured
* [x] Website files uploaded
* [x] S3 website endpoint located
* [x] Website opened successfully in browser

---

## 8. Screenshots

Screenshots from the deployment process are stored separately in:

```text
screenshots/
```

Recommended screenshots include:

1. S3 bucket created
2. Public access configuration
3. Static website hosting configuration
4. Uploaded website files
5. Bucket policy
6. Website endpoint
7. Successfully deployed website

---

## 9. Troubleshooting Notes

### Website Access Denied

If the website returned an access denied error, the bucket's public access configuration and bucket policy were checked.

The bucket policy must allow the required `s3:GetObject` action.

### Website Does Not Load

The following were checked:

* Website hosting was enabled.
* `index.html` existed in the bucket.
* The correct S3 website endpoint was being used.
* The uploaded files were accessible.
* The bucket policy was correctly configured.

### Public Access Was Already Disabled

The **Block all public access** setting was already unchecked because it had been configured earlier when the bucket was created.

Therefore, this step did not require modification during the deployment.

---

## 10. Final Result

The static website was successfully deployed using Amazon S3.

The deployment demonstrated how S3 can be used to serve static web content without requiring a traditional web server.

The completed deployment can be accessed using the S3 static website endpoint documented above.
