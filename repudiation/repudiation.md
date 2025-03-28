# Repudiation

The example demonstrates a vulnerability that can lead to repudiation by malicious users attempting to access the services provided by a server.

## Steps to reproduce

1. Install all dependencies

   `$ npm install`

2. Run the server **insecure.ts**.

3. Pretend to be a malicous user and interact with the services by sending requests from the browser.

4. Do you think your actions can be repudiated?

## For you to do

1. Briefly explain the vulnerability.

- The vulnerability in `insecure.ts` is that the server doesn't keep track of who is performing what actions. There’s no authentication, and there are no logs of incoming requests or user activities. 
- This means a malicious user can send or retrieve messages anonymously and later **deny** their involvement, the system has no way to prove or trace it.

- This kind of situation is called **repudiation**, where users can deny their actions because the system lacks the means to verify or log them. It breaks the principle of **non-repudiation**, which is important in secure systems for accountability and auditing.

2. Briefly explain why the vulnerability is addressed in **secure.ts**.

- The secure version logs every important action with a timestamp, the user (if available), and the requester’s IP address. Whether it’s sending a message, retrieving messages, or encountering an error, every action is logged in a persistent file called `server.log`.

- Even though the current implementation uses simulated authentication, the presence of structured logs provides a strong foundation for **non-repudiation**. With these logs, if a user tries to deny sending a message, the server has clear records to prove otherwise.

- This logging mechanism ensures that malicious actions are traceable, and users can be held accountable.

3. Which design pattern is used in the secure version to address the vulnerability? Briefly explain how it works?

- The secure version uses the **Interceptor** design pattern — specifically through **middleware** in Express.

  - The middleware intercepts every incoming request before it reaches the endpoint.
  - It logs details like request type, URL, timestamp, and IP address.
  - This behavior is reusable and centralized, so it applies across all routes consistently.

- By using middleware as an interceptor, the app can enforce consistent **logging** and security-related tasks like authentication and error tracking. This makes it easier to scale and maintain secure logging practices across the application.