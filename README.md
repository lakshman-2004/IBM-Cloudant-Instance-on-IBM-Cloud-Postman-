# IBM-Cloudant-Instance-on-IBM-Cloud-Postman-

IBM Cloudant — Step-by-Step (Create instance + CRUD via Postman)
Prerequisites

IBM Cloud account and login credentials.

Postman installed and logged in.

Internet connection. 

IBM Cloudant instance on IBM cl…

1. Log in to IBM Cloud

Open browser → search IBM Cloud login and sign in with your IBMid. 

IBM Cloudant instance on IBM cl…

2. Create a Cloudant instance

From the IBM Cloud dashboard click Catalog → search Databases → choose Cloudant.

Click Create and fill required fields (name, region, plan).

After creation go to Resource List → open your Cloudant instance (you’ll see an instance like cloudant-4q in the example). 

IBM Cloudant instance on IBM cl…

3. Launch Cloudant Dashboard and create a database

Inside Cloudant instance click Databases → then Launch Dashboard.

Create a new database with name e.g. lakshman. (In your lab file the database is named lakshman.) 

IBM Cloudant instance on IBM cl…

4. Create a document (JSON) in the dashboard

In the dashboard click Create Document.

Paste your JSON document (remove _rev if present) and Save. Example:

{
  "_id": "novel_004",
  "name": "The Merry Adventures of Robin Hood",
  "author": "Howard Pyle",
  "year": 1883
}


Note the document _id and _rev values after saving (you’ll need _rev later for updates/deletes). 

IBM Cloudant instance on IBM cl…

5. Create Service Credentials (get URL & API key)

In the Cloudant instance page click Service credentials → Create new → add.

Expand the new credential entry to get the url (base $URL) and apikey (your $APIKEY). Copy these — you’ll use them in Postman requests. 

IBM Cloudant instance on IBM cl…

6. Get an Access Token using Postman (IAM token)

Open Postman → create a POST request to the IBM IAM token URL:
https://iam.cloud.ibm.com/identity/token

In Body select x-www-form-urlencoded and add these keys:

grant_type → urn:ibm:params:oauth:grant-type:apikey

response_type → cloud_iam

apikey → $APIKEY (value from service credentials)

Send the request. In the response find access_token — copy it as $ACCESS_TOKEN. (Your lab file shows this flow.) 

IBM Cloudant instance on IBM cl…

7. Retrieve all documents (GET) in Postman

Create a GET request to:

$URL/$DATABASE/_all_docs?include_docs=true


Example: https://xxxx.cloudantnosqldb.appdomain.cloud/lakshman/_all_docs?include_docs=true
2. In Headers add:

Authorization: Bearer $ACCESS_TOKEN


Send → response will show documents (including the one you created). 

IBM Cloudant instance on IBM cl…

8. Create a new document via Postman (POST)

POST to:

$URL/$DATABASE


In Headers set:

Content-Type: application/json
Authorization: Bearer $ACCESS_TOKEN


In Body → raw (JSON) paste the document:

{
  "_id": "novel_005",
  "name": "Some Book",
  "author": "Author Name",
  "year": 2023
}


Send → Cloudant returns id, ok: true, and _rev for the new doc. 

IBM Cloudant instance on IBM cl…

9. Retrieve a specific document by ID (GET)

GET:

$URL/$DATABASE/<DOCUMENT_ID>


Example: $URL/$DATABASE/novel_004
2. Header:

Authorization: Bearer $ACCESS_TOKEN


Send → you receive the specific document JSON including _rev. 

IBM Cloudant instance on IBM cl…

10. Update a document (PUT)

Important: for updates you must include the latest _rev value.

Set method PUT to:

$URL/$DATABASE/<DOCUMENT_ID>


Headers:

Content-Type: application/json
Authorization: Bearer $ACCESS_TOKEN


Body: full document JSON including _id and the latest _rev:

{
  "_id": "novel_004",
  "_rev": "$REV",
  "name": "The Merry Adventures of Robin Hood (Updated)",
  "author": "Howard Pyle",
  "year": 1883
}


Send → response returns new _rev. Replace $REV with the value you got earlier. 

IBM Cloudant instance on IBM cl…

11. Delete a document (DELETE)

Issue DELETE to:

$URL/$DATABASE/<DOCUMENT_ID>?rev=$REV


Header:

Authorization: Bearer $ACCESS_TOKEN


Send → document is removed (response contains ok: true). Remember to use the current _rev. 

IBM Cloudant instance on IBM cl…

12. Quick curl equivalents (optional)

Get all docs

curl -H "Authorization: Bearer $ACCESS_TOKEN" \
  "$URL/$DATABASE/_all_docs?include_docs=true"


Create doc

curl -X POST -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ACCESS_TOKEN" \
  -d '{"_id":"novel_006","name":"Book","author":"X","year":2024}' \
  "$URL/$DATABASE"


Update doc

curl -X PUT -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ACCESS_TOKEN" \
  -d '{"_id":"novel_006","_rev":"$REV","name":"Book v2"}' \
  "$URL/$DATABASE/novel_006"


Delete doc

curl -X DELETE -H "Authorization: Bearer $ACCESS_TOKEN" \
  "$URL/$DATABASE/novel_006?rev=$REV"


(Replace placeholders with real values.) 

IBM Cloudant instance on IBM cl…

Tips & Notes

Always store the latest _rev after create/update — Cloudant uses it for concurrency. 

IBM Cloudant instance on IBM cl…

If you get authentication errors, re-generate the IAM token with the API key. 

IBM Cloudant instance on IBM cl…

Use include_docs=true when you need full document contents with _all_docs. 

IBM Cloudant instance on IBM cl…
