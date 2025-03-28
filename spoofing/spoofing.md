# Spoofing

This example demonstrates spoofind through two ways -- Stealing cookies programmatically and cross site request forgery (CSRF).

## Steps to reproduce the vulnerability

1. Install dependencies

   `$ npx install`

2. Start the **insecure.ts** server

   `npx ts-node insecure.ts`

3. Start the malicious server **mal.ts**

   `npx ts-node mal.ts`

4. Open **http://localhost:8000** in a browser, type a name and Submit.

5. Open the **Application** tab in the Browser's inspect pane. Find the **Cookies** under **Storage**. You should see a **connect.sid** cookie being set.

6. Open the HTML file **mal-steal-cookie.html** file in the same browser (different tab). Open inspect and view the console.

7. Click the link in the HTML file. Do you see the cookie being stolen in the console?

8. Open the HTML file **mal-csrf.html** file in the same browser (different tab). What do you see if the user has not logged out of **insecure.ts**? What do you see if the user has logged out?

## For you to answer

1. Briefly explain the spoofing vulnerability in **insecure.ts**.

    The spoofing vulnerability in `insecure.ts` arises from **insecure session cookie configurations**:

- The session cookie is created without the `HttpOnly` flag (`httpOnly: false`), allowing JavaScript in the browser to access it. This enables malicious scripts (like from a compromised site) to steal session cookies.

- The cookie also lacks `SameSite` protection, leaving it vulnerable to **CSRF attacks**, where attackers can forge requests from the victim's browser.

2. Briefly explain different ways in which vulnerability can be exploited.

- **Session Hijacking**:  
  A malicious site (like `mal-steal-cookie.html`) can read the session cookie using JavaScript and send it to an attacker. This allows the attacker to hijack the session and impersonate the user.

- **Cross-Site Request Forgery (CSRF)**:  
  If a user is logged in and visits a malicious site (e.g., `mal-csrf.html`), it can trigger unauthorized POST requests to the original server (e.g., `/sensitive`) using the victim's session. The server processes these requests as if made by the authenticated user.

3. Briefly explain why **secure.ts** does not have the spoofing vulnerability in **insecure.ts**.
   The `secure.ts` version includes proper security configurations that prevent spoofing:

- `HttpOnly: true`: Prevents JavaScript from accessing the session cookie, protecting against session theft.
- `SameSite: true`: Restricts the browser from sending cookies in cross-site requests, mitigating CSRF.
- Dynamic session secret (`process.argv[2]`): Prevents hardcoded secrets in code, reducing risk of secret leakage.