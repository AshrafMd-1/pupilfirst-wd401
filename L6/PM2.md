# Node.js Application Deployment Documentation

## Environment Variable Configuration

1. **NODE_ENV:**
   - **Purpose:** Specifies the environment in which the application is running.
   - **Expected Values:** 'development', 'production'

2. **PORT:**
   - **Purpose:** Defines the port on which the Node.js application will listen.
   - **Expected Values:** Any valid port number.

3. **DB_URL:**
   - **Purpose:** Database connection URL.
   - **Expected Values:** Connection URL for the database.

4. **API_KEY:**
   - **Purpose:** API key for third-party services.
   - **Expected Values:** Secure, unique API key.

5. **LOG_LEVEL:**
   - **Purpose:** Defines the logging level for the application.
   - **Expected Values:** 'info', 'debug', 'error', etc.

6. **JWT_SECRET:**
   - **Purpose:** Secret key for JSON Web Token (JWT) authentication.
   - **Expected Values:** A strong, unique secret key.

7. **MAIL_API_KEY:**
   - **Purpose:** API key for sending emails.
   - **Expected Values:** Secure API key.

8. **SESSION_SECRET:**
   - **Purpose:** Secret key for managing user sessions.
   - **Expected Values:** A strong, unique secret key.

Ensure that sensitive information, such as API keys or database credentials, is not hardcoded in the application code but is instead loaded from these environment variables.

## PM2 Cluster Mode Deployment

PM2 is used to deploy the Node.js application in cluster mode. The configuration file (`pm2.config.js`) should include:

```javascript
module.exports = {
  apps: [
    {
      name: 'your-app-name',
      script: 'app.js',
      instances: 'max',
      exec_mode: 'cluster',
      autorestart: true,
      watch: true,
      max_memory_restart: '1G',
      log_date_format: 'YYYY-MM-DD HH:mm Z',
      env: {
        NODE_ENV: 'development',
        PORT: 3000,
        LOG_LEVEL: 'debug'
      },
      env_production: {
        NODE_ENV: 'production',
        PORT: 80,
        LOG_LEVEL: 'info'
      }
    }
  ]
};
```

## Logging

Ensure that the application logs are saved properly by setting up the appropriate log configuration in the PM2 file. In the example above, logs are stored in the default location with a specific date format.

## Security Measures

1. **Securing Environment Variables:**
   - Store sensitive information, like API keys or database credentials, in environment variables.
   - Do not expose environment variables unintentionally. Keep them private and only share with authorized personnel.

2. **HTTPS Encryption (Optional):**
   - Obtain a valid SSL certificate for your domain.
   - Enable HTTPS in your Node.js application.

3. **Additional Security Measures (as per your application requirements):**
   - Implement input validation to prevent injection attacks.
   - Keep dependencies up to date to patch security vulnerabilities.
   - Employ rate limiting and other relevant security middleware.
# Conclusion

This documentation provides a comprehensive guide for deploying a Node.js application using PM2 in cluster mode, managing environment variables, and implementing security measures to fortify the application.
