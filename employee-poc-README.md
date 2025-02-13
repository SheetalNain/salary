# Employee API POC


| **Author**            | **Created on** | **Version** | **Last updated by**       | **Last edited on** | **Reviewer** |**L0 Reviewer** |**L1 Reviewer** |**L2 Reviewer** |
|-----------------------|----------------|-------------|----------------------------|---------------------|-------------------|-------------------|-------------------|-------------------|
| Sheetal      | 13-02-25       | Version 1 | Sheetal         | 13-02-25     | Siddharth Pawar    | Shikha  | Kirti  | Ashwani Singh  |


To run the Employee-API we first need to install the following tools:

- GO
- [Scylla](https://github.com/Snaatak-Skyops/Documentation/tree/SCRUM-16/OT%20MS%20Understanding/Database/Scylla%20DB/POC)

# Steps to set up Employee API

### **1. First of all, we have to install the dependencies. use the following commands for Go installation:**
### **To Update Package List**
```
sudo apt update
```
 **Explanation** - It ensures your system fetches the latest package versions and security updates, preventing installation issues with outdated packages.


```
export PATH=$PATH:/usr/local/go/bin
```
**Explanation** - It allows you to run Go (go) commands from any terminal without specifying the full path to
the Go binary.


```
sudo apt install golang-go
```
**Explanation** - Installs the Go programming language package (golang-go) from the system’s package repository.


```
go version
```
**Explanation** - To check the version.

---

### 2. Add authorization for CQL in Scylla's configuration file.

```
cd /etc/scylla
```
**Explanation** - Changes the working directory to /etc/Scylla, where the Scylla configuration files are stored.


**We have to make some changes in the Scylla configuration files, for this follow these steps:**

```
sudo vi scylla.yaml
```
**Explanation** - Opens the scylla.yaml configuration file in the vi/nano text editor.


**NOTE - Changes to make**

```
rpc_address: <instance private IP address>
```

- Add :
```
authenticator: PasswordAuthenticator
authorizer: CassandraAuthorizer
```

**Why do we need these changes?**
**rpc_address** - defines the IP address on which Scylla will listen for incoming requests from clients. This change ensures that Scylla only listens for requests on the specified internal network interface.

**Adding authenticator and authorizer** - By setting both of these, you're enabling authentication (requiring a password for users) and authorization (controlling what users can do after they log in).

```
sudo /opt/scylladb/scripts/scylla_io_setup
```
**Explanation** - Runs the Scylla I/O setup script, which configures the system for optimal disk performance for ScyllaDB.


```
sudo systemctl restart scylla
```
**Explanation** - Restarts the ScyllaDB service using systemctl, which is a tool for managing services in Linux. This reloads any configuration changes.


```
sudo systemctl start scylla-server
```
**Explanation** - Starts the ScyllaDB server service.


```
sudo systemctl status scylla-server
```
**Explanation** - Checks the status of the ScyllaDB server to see if it is running or if there are any issues.

---
### 3. Now run this command.
```
cqlsh <instance-private-Ip> 9042 -u cassandra -p cassandra
```
**Explanation** - Connects to ScyllaDB on the private IP with the cassandra credentials.

---
### 4. Now run these commands in CQL.
```
CREATE USER scylladb WITH PASSWORD 'password' SUPERUSER;
```
**Explanation** - Creates a user scylladb with superuser privileges and the password password.

```
CREATE KEYSPACE employee_db WITH REPLICATION = { 'class': 'SimpleStrategy', 'replication_factor': 1 };
```
**Explanation** - Creates a new keyspace (database) named employee_db with a replication factor of 1.

```
DESCRIBE KEYSPACES;
```
**Explanation** - Lists all available keyspaces in the database.

**Exit from the CQL.**

---
### 5. Now clone the Git repository.

```
git clone https://github.com/OT-MICROSERVICES/employee-api.git
```

**Explanation** - Clones the employee-Api repository using HTTPS (this typically works even without SSH keys).

---
### 6. Switch to the employee API directory.

```
cd employee-Api/
```

---
### 7. Update the config.yaml

```
sudo vi config.yaml
```
**Changes to be made** - Edit this file to configure settings for the application and replace the IP with your actual private IP of the instance in the host section.

```
host: ["<Private IP>:9042"]
```
---
### 8. Now run the below command.

```
go test $(go list ./... | grep -v docs | grep -v model | grep -v main.go) -coverprofile cover.out
# For HTML report visualization
go tool cover -html=cover.out
```
**Explanation** - It runs tests across all Go packages in the project, except those in the docs, model directories, and main.go file.
It generates a coverage report in a file named cover.out.
It then creates an HTML version of the coverage report that you can visualize in your browser.

---
### 9. Update the main.go file.

```
sudo vi main.go
```
**Changes to be made** - Replace localhost to <public-ip:8080>.
```
url := ginSwagger.URL("http://<Public IP>:8080/swagger/doc.json")
```

---
### 10. Now run this command.

```
go run main.go
```

**Explanation** - This command compiles main.go and all its dependencies (if any) and Runs the compiled program.

---
### 11. Now access the swagger page with the help of the below command.

```
http://public-ip:8080/swagger/index.html
```

---
## Endpoints Information

| **Endpoint**                        | **Method** | **Description**                                                                                   |
|-------------------------------------|------------|---------------------------------------------------------------------------------------------------|
| /metrics                            | GET        | Application healthcheck and performance metrics are available on this endpoint                    |
| /api/v1/employee/health             | GET        | Endpoint for providing shallow healthcheck information about application health and readiness     |
| /api/v1/employee/health/detail      | GET        | Endpoint for providing detailed health check about dependencies as well like - ScyllaDB and Redis |
| /api/v1/employee/create             | POST       | Data creation endpoint which accepts certain JSON body to add employee information in database    |
| /api/v1/employee/search             | GET        | Endpoint for searching data information using the params in the URL                               |
| /api/v1/employee/search/all         | GET        | Endpoint for searching all information across the system                                          |
| /api/v1/employee/search/location    | GET        | Application endpoint for getting the count and information of location                            |
| /api/v1/employee/search/designation | GET        | Application endpoint for getting the count and information of designation                         |
| /swagger/index.html                 | GET        | Swagger endpoint for the application API documentation and their data models                      |

---
## Contact Information 

| Name| Email Address      |
|-----|--------------------------|
| Sheetal | sheetal.nain@mygurukulam.co |

---
## References
| Links | Description |
|-------|-------------|
| [Go installation](https://www.cherryservers.com/blog/install-go-ubuntu) | Go installation on ubuntu |
| [Scylla installation](https://opensource.docs.scylladb.com/stable/getting-started/install-scylla/install-on-linux.html)  | Scylla installation on ubuntu |














