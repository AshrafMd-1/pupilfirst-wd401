# **Environment Variable Configuration:**

## Introduction
Environment variables are variables outside of a program but accessible by it. They are part of the environment in which a process runs. In many operating systems, including Unix-like systems (e.g., Linux) and Windows, environment variables are used to store configuration settings and information that can be accessed by various applications.

In programming, you can access environment variables through code to retrieve information like paths, user settings, or other configurations. The names and conventions for setting and accessing environment variables may vary between operating systems and programming languages.

## Why Do We Use Them
- **Configuration Settings**
- **Security**
- **Portability**
- **Dynamic Information**
- **Ease of Deployment**
- **Separation of Concerns**
- **Compatibility**
## Usage
In node js we use a file called `.env` to store all the environment variables.

`.env`

```env
; Description: Environment variables for the application  

PORT=8080  
COOKIE_SECRET=h4}A~yesA]?8f7F/zc!  
CSRF_SECRET=2r~h8B_]{eb8n!CQ6-m~I>E:9B,r43XE  
SESSION_SECRET=3fKFqTZJHxABh@M|95nc0u./zR%^qC_j/
```

Then we download `dontenv` package to export the environment variables to the main file
`npm install dotenv`

Finally we import them into the main file

`index.js`

```js
require('dotenv').config();  
const app = require("./app");  
  
app.listen(process.env.PORT, () => {  
  console.log(`Started express server at port http://localhost:${process.env.PORT || 3000}`);  
});
```

Where
- `require('dotenv').config();`: This line imports the `dotenv` module and invokes its `config` method. The `dotenv` module is used to load environment variables from a `.env` file into `process.env`.
- `app.listen(process.env.PORT, () => {`: This line starts the Express.js server and listens for incoming requests. It uses the value of the `PORT` environment variable as the port number. The `process.env.PORT` is replaced by the actual value set in the environment variables, which is configured in the `.env` file.

## Details of the Variables
The variables stored in the `.env` file are:
- `PORT=8080`: This variable specifies the port number on which the Express.js server will listen for incoming connections. In this case, the server will listen on port 8080. The actual port can be accessed in the code using `process.env.PORT`.
- `COOKIE_SECRET=h4}A~yesA]?8f7F/zc!`: This variable is used as a secret key for encrypting and signing cookies.
- `CSRF_SECRET=2r~h8B_]{eb8n!CQ6-m~I>E:9B,r43XE`: This variable is used as a secret key for generating and validating CSRF (Cross-Site Request Forgery) tokens.
- `SESSION_SECRET=3fKFqTZJHxABh@M|95nc0u./zR%^qC_j/`: This variable is used as a secret key for session management.

## Starting
As now if we run the file we will start at port 8080
![image](https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/ac43f38a-f3f1-43fa-9cc1-cb731dd0488b)

# **PM2 Cluster Mode Deployment:**
## Introduction
PM2 (Process Manager 2) is a popular and feature-rich process manager for Node.js applications. It is designed to simplify the deployment, management, and monitoring of Node.js processes in a production environment.

## Why Do We Use Them

- **Process Management**
- **Automatic Restart**
- **Logging**
- **Load Balancing**
- **Startup Scripts**
- **Monitoring**
- **Environment Variable Management**

## Usage

First we install the pm2
`npm install -g pm2`

Then we create an `ecosystem.config.js` file using the pm2 CLI command
`pm2 ecosystem`

A new file is created called as `ecosystem.config.js`

```js
module.exports = {  
  apps: [  
    {  
      name: "Todo-App",  
      script: "index.js",  
      instances: "6",  
      exec_mode: "cluster",  
      watch: true,  
      max_memory_restart: "1G",  
      log_date_format: "YYYY-MM-DD HH:mm Z",  
      env: {  
        NODE_ENV: "development",  
      },  
      env_production: {  
        NODE_ENV: "production",  
      },  
    },  
  ],  
};
```

Where
- **`apps` Array:**
    - Application-specific configurations are defined within an array under the `apps` property.
- **Application Configuration:**
    - **`name: "Todo-App"`:** Sets the name of the application to "Todo-App."
    - **`script: "index.js"`:** Specifies the entry point script as "index.js."
    - **`instances: "6"`:** Runs the application with 6 instances in cluster mode.
    - **`exec_mode: "cluster"`:** Sets the execution mode to "cluster" for running in a clustered environment.
    - **`watch: true`:** Enables the watch mode for automatic restart on file changes.
    - **`max_memory_restart: "1G"`:** Sets the maximum memory threshold for automatic restart to 1 gigabyte.
    - **`log_date_format: "YYYY-MM-DD HH:mm Z"`:** Configures the date format for the logs.
- **Environment Configuration:**
    - **`env: { NODE_ENV: "development" }`:** Sets the `NODE_ENV` environment variable to "development" for the development environment.
    - **`env_production: { NODE_ENV: "production" }`:** Sets `NODE_ENV` to "production" for the production environment.

## Start
Now we just go to the terminal and type
`pm2 start ecosystem.config.js`

This command will run the ecosystem.config.js file and create 6 instances of the same application while also using the environment variables
<img width="509" alt="image" src="https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/6f3283e8-9b11-474d-aea3-d92754327997">

As you can see instead of a single application pm2 make a cluster of 6 apps
![image](https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/ac43f38a-f3f1-43fa-9cc1-cb731dd0488b)

Now to stop all the clusters we use the command
`pm2 stop all`
<img width="500" alt="image" src="https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/89c763f1-04b4-4f37-b491-949a64759757">

If we want to delete them all we use the command
`pm2 delete all`
<img width="451" alt="image" src="https://github.com/AshrafMd-1/pupilfirst-wd401/assets/98876115/42b764c0-d6f2-4732-ab10-91bd098c9706">

So, This is how we configure a pm2 environment
# **Security Measures:**

## Introduction
Security measures refer to a set of practices, protocols, and safeguards implemented to protect systems, applications, and data from unauthorized access, attacks, or breaches. These measures are crucial in maintaining the confidentiality, integrity, and availability of information.

## Why Do We Need Security Measures
Security measures are essential for several reasons, each contributing to the overall protection and stability of systems, data, and individuals.

## How Do We Do it
- **Keep Dependencies Updated**
- **Use HTTPS**
- **Validate Input**
- **Escape Output**
- **Implement Authentication**
- **Set Secure HTTP Headers**
- **Session Management**
- **Avoid Sensitive Data in URLs**
- **Rate Limiting**
- **Cross-Site Request Forgery (CSRF) Protection**
- **Content Security Policy (CSP)**
- **File Upload Security**
- **Dependency Scanning**
- **Security Headers**
- **Error Handling**
- **Logging and Monitoring**
- **Database Security**
- **Regular Security Audits**

## Security Measures Used in My App

### 1. CSRF Token

**Explanation:**
- **Cross-Site Request Forgery (CSRF) Protection:**
    - CSRF tokens are a security measure to prevent attackers from performing unauthorized actions on behalf of a user.
    - Each user session is associated with a unique CSRF token, which is included in forms or headers.
    - When a request is made, the server checks if the CSRF token is valid, ensuring that the request originated from the legitimate user and not from a malicious site.

**Implementation:**
- Included CSRF tokens in forms or headers
- Validates CSRF tokens on the server-side for every non-idempotent action.
### 2. Environment Variables

**Explanation:**
- **Sensitive Configuration Management:**
    - Environment variables are used to store sensitive configuration data, such as API keys, database credentials, or secret keys.
    - Storing configuration information in environment variables keeps sensitive data out of version control and reduces the risk of exposure.

**Implementation:**
- Using environment variables to store sensitive information.
- Accesses environment variables within your application code to configure services securely.

`.env`

```env
; Description: Environment variables for the application  

PORT=8080  
COOKIE_SECRET=h4}A~yesA]?8f7F/zc!  
CSRF_SECRET=2r~h8B_]{eb8n!CQ6-m~I>E:9B,r43XE  
SESSION_SECRET=3fKFqTZJHxABh@M|95nc0u./zR%^qC_j/
```

### 3. Password Hashing

**Explanation:**
- **User Authentication Security:**
    - Password hashing is a crucial measure for securing user authentication data.
    - Instead of storing plain-text passwords, the application stores a hashed and salted version of the password.
    - Hashing algorithms, such as bcrypt, are designed to be slow and computationally expensive, making it difficult for attackers to perform brute-force or rainbow table attacks.

**Implementation:**
- Uses a strong and adaptive hashing algorithm lcalled bcrypt.
- Includes a unique salt for each user to prevent rainbow table attacks.
- Hash and verifies passwords during user registration and authentication processes.
