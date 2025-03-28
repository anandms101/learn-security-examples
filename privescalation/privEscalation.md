# Privilege Escalation

The example demonstrates a privilege escalation vulnerability and how to exploit it.

## Steps to reproduce

1. Install all dependencies

   `$ npm install`

2. Start the **insecure.ts** server

   `$ npx ts-node insecure.ts`

3. In the browser, send a GET request

   ```
       http://localhost:3000/send-form
   ```

4. Try different UserIds and see which one gives you authorized access to change the role of that user.

## For you to do

Answer the following:

1. Briefly explain the potential vulnerabilities in **insecure.ts**

- The vulnerability in `insecure.ts` comes from the fact that **authorization is based on user input**, not actual authentication. The server checks the `userId` from the incoming request body and uses that to determine whether the user has admin privileges.
- This is insecure because any user can simply send a request with `userId: 1` (the admin user) and the server will trust it without validating the true identity of the requester.

  Since there's no login mechanism or session management, the server has no reliable way to track who the actual requester is, making it very easy to **spoof roles** and escalate privileges.

2. Briefly explain how a malicious attacker can exploit them.

- An attacker can exploit this by crafting a POST request to `/update-role` and setting `userId` to `1` (which corresponds to the admin in the simulated database), and setting `newRole` to any desired value.
- Since the server doesn't verify if the actual user making the request is really the admin, it will accept the request and update the role of any user in the system.

- This allows a normal user to:
  - Impersonate the admin,
  - Change their own role to 'admin',
  - Or escalate privileges for others; all without proper authorization.

3. Briefly explain the defensive techniques used in **secure.ts** to prevent the privilege escalation vulnerability?

- The secure version uses **session-based authentication** via `express-session`. When a user logs in, their ID is stored in the session (`req.session.userId`).
- This ensures that all further actions are tied to the authenticated session rather than trusting data sent in the request body.

- Authorization is then done by checking the logged-in user's role using the `userId` stored in the session — not one provided in the body of the request. Only users with the `'admin'` role are allowed to perform role updates.

- In addition, the session cookie is configured with:
  - `httpOnly: true` to prevent client-side access,
  - `sameSite: 'strict'` to mitigate CSRF attacks.
