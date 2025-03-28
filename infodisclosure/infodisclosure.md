# Tampering

This example demonstrates information disclosure by injecting malicious query objects to a NoSQL database.

## Steps to reproduce

1. Install all dependencies

   `$ npm install`

2. Insert test data in the MongoDB database. Make sure the mongod is up and running by typing the `mongosh` command in the termainal. If mongod process is up then you will see that the connection was successful. Command to insert test data:

   `$ npx ts-node insert-test-users.ts`

This will create a database in MongoDB called **infodisclosure**. Verify its presence by connecting with mongosh and running the command `show dbs;`.

2. Start the **insecure.ts** server

   `$ npx ts-node insecure.ts`

3. In the browser, pretend to be a hacker and type a malicious request

   ```
       http://localhost:3000/userinfo?username[$ne]=
   ```

4. Do you see user information being displayed despite the malicious request not having a valid username in the request?

## For you to do

Answer the following:

1. Briefly explain the potential vulnerabilities in **insecure.ts**

- The vulnerability in `insecure.ts` stems from using **unsanitized user input** directly in a MongoDB query. Specifically, the code executes:

  ```
  User.findOne({ username: req.query.username })
  ```

- This makes the app vulnerable to NoSQL injection, where attackers can craft a query object like `username[$ne]=` to bypass normal authentication or access control logic.
- The full User object, including sensitive fields like passwords is returned in the HTTP response, which leads to information disclosure.

2. Briefly explain how a malicious attacker can exploit them.

- A malicious actor can manipulate query parameters by injecting MongoDB operators (`$ne`, `$gt`, `$regex`, etc.) to:

  - Retrieve user records without knowing actual usernames.

  - Bypass authentication logic entirely.

  - Leak full user documents (including passwords, if unencrypted).

  - Chain this with other exploits like account takeovers or privilege escalation.

- This violates the Confidentiality principle of secure systems by exposing internal database structure and sensitive user data.

3. Briefly explain the defensive techniques used in **secure.ts** to prevent the information disclosure vulnerability?

   The `secure.ts` version applies two key defensive strategies:

   - Input validation:
     It ensures username is a string using:

   ```
   if (typeof username !== 'string')
   ```

   This stops query object injections like `username[$ne]=`.

   - Input sanitization:
     It removes any non-alphanumeric characters with:

   ```
   username.replace(/[^\w\s]/gi, '')
   ```

   This ensures that the query remains a plain string, neutralizing NoSQL injection vectors.

- Error handling:
  - Any server errors are logged internally and a generic message is sent to the client.
  - This avoids leaking implementation details or stack traces.