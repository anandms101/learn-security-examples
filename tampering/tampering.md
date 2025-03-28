# Tampering

This example demonstrates tampering through script injection.

## Steps to reproduce

1. Install all dependencies

   `npm install`

2. Start the **insecure.ts** server

   `npx ts-node insecure.ts`

3. In the browser, type a potentially malicious script in the name field of the form

   ```
       <script> document.body.innerHTML = "<a href='https://google.com'> Gotcha </a>"</script>
   ```

4. Do you see the potentially malicious hyperlink being injected into the form?

## For you to do

Answer the following:

1. Briefly explain the potential vulnerabilities in **insecure.ts**

- The main vulnerability in `insecure.ts` is a **Cross-Site Scripting (XSS)** attack due to **unsanitized user input**. When the app accepts a user's name via the form, it directly stores and displays it in the HTML response without any escaping or validation.

- This allows an attacker to inject malicious HTML or JavaScript that gets executed whenever the homepage is rendered, a textbook example of **stored XSS**. This is a classic case of **tampering**, where attackers modify application inputs to cause unintended behavior.

2. Briefly explain how a malicious attacker can exploit them.

- An attacker could submit a script as their name (like the example in answer 3), which gets saved in the session. Then, every time someone visits the homepage, that script executes in their browser.

- This could lead to:

  - Defacing the webpage content
  - Stealing session cookies (`document.cookie`)
  - Redirecting users to phishing pages
  - Running any arbitrary JavaScript in the user’s context

- This attack is effective because the server blindly trusts and reflects user input, violating the **integrity** of the application’s UI.

3. Briefly explain why **secure.ts** does not have the same vulnerabilties?

- The `secure.ts` version fixes this issue by properly sanitizing user input before saving it. It uses a function called `escapeHTML()` that escapes special HTML characters like `<`, `>`, `"` and `'`, ensuring that any scripts entered are rendered as plain text instead of being executed.

- So even if someone enters a `<script>` tag, it appears on the page harmlessly without being interpreted by the browser. This protects the app against XSS attacks and maintains the **integrity** of the system — which is the goal of secure software engineering by design.