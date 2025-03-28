# Denial-of-Service (DoS)

This example demonstrates DoS vulnerabilities and how they can be exploited.

## Steps to reproduce

1. Install all dependencies

   `$ npm install`

2. Ignore if you have already done this once. Insert test data in the MongoDB database. Make sure the mongod is up and running by typing the `mongosh` command in the termainal. If mongod process is up then you will see that the connection was successful. Command to insert test data:

   `$ npx ts-node insert-test-users.ts`

This will create a database in MongoDB called **infodisclosure**. Verify its presence by connecting with mongosh and running the command `show dbs;`.

2. Start the **insecure.ts** server

   `$ npx ts-node insecure.ts`

3. In the browser, pretend to be a hacker and type a malicious request

   ```
       http://localhost:3000/userinfo?id[$ne]=
   ```

4. Do you see the server crashing?

## For you to do

Answer the following:

1. Briefly explain the potential vulnerabilities in **insecure.ts** that can lead to a DoS attack.

- The `insecure.ts` version does not have any kind of rate limiting or input validation on the `/userinfo` route. Because of this, a user can make an unlimited number of requests to the server.
- If the request includes a malformed or malicious query (e.g., `id[$ne]=`), MongoDB may try to process it or even throw an error, potentially leading to memory overload or crashing the server.

- This opens up the server to a **Denial-of-Service (DoS)** vulnerability, where an attacker can flood it with many malformed or valid requests to exhaust system resources and make the service unavailable to others.

2. Briefly explain how a malicious attacker can exploit them.

- An attacker can script repeated GET requests to the `/userinfo` endpoint, using either valid IDs or malformed inputs like:

  `/userinfo?id[$ne]=`

  Because the server processes every request without any restriction, this flood of traffic can:

  - Spike CPU and memory usage,
  - Crash the MongoDB query engine,
  - And eventually bring down the server, making it unresponsive for legitimate users.

  This effectively leads to a **DoS condition**, violating the Availability principle.

3. Briefly explain the defensive techniques used in **secure.ts** to prevent the DoS vulnerability?

- The secure version (`secure.ts`) protects against DoS using **rate limiting** with the `express-rate-limit` middleware:

  ```
  windowMs: 5 * 1000, // 5 seconds
  max: 1              // 1 request/window/IP
  ```

- This means a user (based on IP address) can only send one request every 5 seconds. If they exceed this, the server responds with a friendly error message like:

  ```
  Server is busy, please try again later.
  ```

- This simple defense helps:

  - Reduce traffic load,

  - Prevent brute-force or automated attack scripts,

  - And ensures that legitimate users can still access the service.
