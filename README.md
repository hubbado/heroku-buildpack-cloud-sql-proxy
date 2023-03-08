# Google Cloud SQL Proxy v2.0.0 buildpack
Connect to Google Cloud SQL Proxy V2 from an external application without a static (or public) IP address.  

The buildpack will install Google's V2 Proxy service to the localhost and configure it with credentials supplied via the environment variables. After starting the proxy service, your application will have access to the Cloud SQL instance by connecting and querying the proxy running on the localhost.  

The proxy establishes the actual connection to the Cloud SQL instance and acts as a 'proxy' that communicates requests and responses between your application and the Cloud SQL instance. Learn more: [Google Docs](https://cloud.google.com/sql/docs/postgres/connect-external-app)


# Setup and installation

* **Enable the Cloud SQL Admin API:** [here](https://cloud.google.com/sql/docs/postgres/connect-external-app#enable-api)

* **Create credential key file:** Go to Google Cloud Console -> Menu -> API & Services -> Credentials -> Create "Service account key" -> Follow instructions to generate a json credential file. If required, [create a service account](https://cloud.google.com/sql/docs/postgres/connect-external-app#4_if_required_by_your_authentication_method_create_a_service_account).

* **Setup a user that the proxy will use for authorizing with the Cloud SQL instance**: Go to Google Cloud console -> Menu -> SQL -> Select instance -> Go to users -> create a user and remember the username + password for later.

* **Encode credential key file into a base64 string:** In the terminal run: `base64 <key-filename.json>`

* **Add envs to server:**  
`GCLOUD_CREDENTIALS=<base64 string from step before>`  
`GCLOUD_INSTANCE=<instance-connection-name>` *instance-connection-name* is found in the overview on the Cloud SQL instance  
`DB_CONNECTION_URI=mysql://<username>:<password>@localhost:3306/<database-name>` *username* and *password* for the user created in the steps before. Here's a javascript example of connecting to the db using this URI.

```
var connection = mysql.createConnection(process.env.DB_CONNECTION_URI);
```

* **Add buildpack to server and run `bin/run_cloud_sql_proxy` on startup:**  
For heroku run: `heroku buildpacks:add https://github.com/DanielZambelli/heroku-buildpack-cloud-sql-proxy -a <name-of-your-application>`  
And to run the proxy when starting a Node.js web process add this to the .procfile `web: bin/run_cloud_sql_proxy && npm start`.

Credit to [Daniel Zambelli](https://danielzambelli.dk/) for the original buildpack. Forked repo can be found [here](https://github.com/DanielZambelli/heroku-buildpack-cloud-sql-proxy).
