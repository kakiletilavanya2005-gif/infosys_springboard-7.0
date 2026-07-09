# Infosys_FranciseOps_AI
infosys springboard 7.0 

What is JWT?

JWT stands for JSON Web Token. Think of it like a digital wristband you get at an event:

- When you log in successfully, the server gives you a wristband (token) — it doesn't ask you for your password again and again.
- Every time you go somewhere inside the "event" (like the Dashboard page), you show your wristband instead of proving your identity from scratch.
- The wristband is signed with a secret key, so no one can forge a fake one or tamper with it — if someone tries, the signature won't match, and the app           rejects it.
- The wristband usually expires after some time, just like an event pass is only valid for that day.

In this project:
- A JWT is created only when Login succeeds.
- It is stored in Streamlit's session state (kind of like the app "remembering" you're logged in).
- The Dashboard page checks: *"Does this user have a valid wristband (JWT)?"* If yes → show the Dashboard. If no → send them back to Login.
What is ngrok? 

Normally, a Streamlit app running inside Google Colab only works on Colab's own internal server — nobody outside can open it in their browser, because it doesn't have a real website address.

ngrok solves this by creating a secure tunnel from the internet to your Colab notebook. It gives you a temporary public link (something like `https://randomcode.ngrok.io`) that anyone can open, and it forwards all that traffic straight into your running Colab app — as if the notebook were a real live website.

Think of it like this: your Colab notebook is a shop inside a building with no street entrance. ngrok builds a temporary doorway from the main street directly into that shop, so customers (users) can walk in.


What is a "Secret" and Why Do We Need It?

Instead of typing sensitive information (passwords, tokens) directly into the code — which is unsafe and could leak if the notebook is shared — we store them in Colab Secrets, a secure vault built into Google Colab. The code reads these secrets only while running; they're never visible in the notebook file itself.

How to Create Each Secret Key (Step-by-Step)

 JWT_SECRET — Your Own Random Signing Key

This one you create yourself — it's simply a long, random, hard-to-guess string of characters. It doesn't come from any website.

How to generate one:
- Easiest way: run this in any Python cell —
- python
  import secrets
  print(secrets.token_hex(32))
```
- This prints something like: `f3a9c1e7b2d4...` (a long random string).
- Copy this value — this is your `JWT_SECRET`.
- Keep it private. Anyone who has this key could forge login tokens for your app.
  NGROK_AUTHTOKEN — From Your ngrok Account

1. Go to [ngrok.com](https://ngrok.com) and **sign up for a free account**.
2. Once logged in, go to your **ngrok Dashboard**.
3. On the left sidebar, click **"Your Authtoken"** (under Getting Started).
4. You'll see a long token string on that page — click **Copy**.
5. This copied value is your `NGROK_AUTHTOKEN`.
EMAIL_ADDRESS and EMAIL_PASSWORD — Gmail App Password

EMAIL_ADDRESS is simply the Gmail address you want to send OTPs from (e.g. `yourprojectmail@gmail.com`).

EMAIL_PASSWORD is not your normal Gmail password — Google blocks apps from using your real password for security reasons. Instead, you need to generate a special **App Password**:

Step A — Turn on 2-Step Verification (required first)
1. Go to your [Google Account settings](https://myaccount.google.com/).
2. Click Security in the left menu.
3. Under "How you sign in to Google," turn on **2-Step Verification** and complete the setup (usually via your phone number).

Step B — Generate the App Password
1. Still inside Google Account → Security, use the search bar at the top and type **"App Passwords"**.
2. Click on the App Passwords result to open it.
3. Give it a name you'll recognize, e.g. `Infosys` or `MilestoneOTP`.
4. Click Create (or **Generate**).
5. Google will show you a 16-character password (in a format like `abcd efgh ijkl mnop`).
6. Copy this password immediately — this is your EMAIL_PASSWORD.
   Important Note: Once you click "Done," Google will never show this password again. If you lose it, you'll have to generate a new one.

*  Project Workflow — Step-by-Step Explanation

This section explains **exactly what happens** when you run this project, from opening Google Colab to successfully resetting a password. 


  Step 1: Open Google Colab and Create a New Notebook

1. Go to [Google Colab](https://colab.research.google.com/).
2. Click "New Notebook".
3. Rename the notebook to "Milestone 1" (click on the default name at the top and type the new name).

This notebook is where all our code — the login system, signup system, OTP system, and dashboards — will run.



  Step 2: Create the Four Secret Keys

Before running any code, we need to set up 4 secret keys. These are private values the code needs, but we never write them directly inside the notebook — we store them safely using Colab's built-in Secrets feature (the  key icon on the left sidebar).

Here are the four secrets and what each one means:

 *  Secret Name | What It Is |

 1  EMAIL_PASSWORD --- A special Gmail  App Password
 2  EMAIL_ADDRESS ---- Our own Gmail address, used to send OTP emails 
 3  JWT_SECRET --- Already provided in the code — a random key used to create secure login sessions 
 4  NGROK_AUTHTOKEN --- A token from the ngrok website (explained below) 


 JWT_SECRET --- The random string you generated in Step 1 
 NGROK_AUTHTOKEN ----- The token copied from your ngrok dashboard 
 EMAIL_ADDRESS ---- Your Gmail address 
 EMAIL_PASSWORD ---- The 16-character App Password from Google

For each secret, toggle **"Notebook access"** to **ON** — otherwise your code won't be able to read it.
 In your code, access them like this:
python
   from google.colab import userdata
   jwt_secret = userdata.get('JWT_SECRET')
   ngrok_token = userdata.get('NGROK_AUTHTOKEN')
   email_address = userdata.get('EMAIL_ADDRESS')
   email_password = userdata.get('EMAIL_PASSWORD')

*  How to Create the Gmail App Password (EMAIL_PASSWORD)
here first go to chrome and search mygooglesettings then turn on 2-step verification and then search app passwords in search bar and it create a app password

Gmail does not allow apps to log in using your
real Gmail password for security reasons. So Google gives us a separate, special password just for apps — called an **App Password**.

Steps:
1. Go to your [Google Account](https://myaccount.google.com/).
2. Click on **Security** in the left-side menu.
3. Turn ON **2-Step Verification** first (if it's not already on). This step is compulsory — without it, App Passwords won't even appear as an option.
4. Once 2-Step Verification is enabled, go back to the search bar at the top of the Google Account page and type **"App Passwords"**.
5. Click on it, give it any name you'll recognize (example: InfosysProject).
6. Click **Create**.
7. Google will instantly show a 16-character password (something like: `abcd efgh ijkl mnop`).
8. Copy this password immediately — once you click "Done," Google hides it forever and you can't view it again (you'd have to generate a new one).

This copied password is your EMAIL_PASSWORD.

Your normal Gmail address (example: `yourname@gmail.com`) becomes the EMAIL_ADDRESS.



*  How to Create the ngrok Authtoken (`NGROK_AUTHTOKEN`)

Our Streamlit app runs *inside* Google Colab, but Colab has no public web address — nobody outside can open it. **ngrok** solves this by creating a temporary public link that connects straight into our Colab notebook, so anyone can open the app in their browser.

Steps:
1. Go to [ngrok.com](https://ngrok.com) and click **Sign Up** (it's free).
2. Create your account and log in.
3. Once inside the ngrok dashboard, look at the left sidebar and click **"Your Authtoken"**.
4. You'll see a long string of letters/numbers — this is your personal authtoken.
5. Click Copy.

This copied value is your `NGROK_AUTHTOKEN`.

---

  Adding All 4 Secrets in Colab

1. In your Colab notebook, click the key icon on the left sidebar.
2. Click "Add new secret" and add each one:

Name --- Value 

 EMAIL_PASSWORD  The 16-character App Password 
 EMAIL_ADDRESS  Your Gmail address 
 JWT_SECRET  Already given in the code 
 NGROK_AUTHTOKEN  Copied from the ngrok dashboard 

3. Turn ON the toggle next to each secret so the notebook is allowed to use it.

---

  Step 3: Run All the Cells

 Once all four secrets are added, run every cell in the notebook from top to bottom (Runtime → Run all, or run one by one).

 As the last cell finishes running, it will print out a public link (this is the ngrok link).

---

  Step 4: Click the Link to Open the Infosys Portal

 Clicking this link opens the **Infosys portal** (our Streamlit app) directly in the browser — this is the actual authentication system we built.

---

Step 5: Sign Up to Create an Account

On the portal, we first need to create an account before we can log in.

1. Click on Sign Up.
2. Fill in the required details: Username, Email, Password, Confirm Password, Security Question, and Security Answer.
3. Click Create Account.

Validation rules while creating the account (these were set based on our mentor's requirements):

- Username and Password rules:
  - Password and Confirm Password must match exactly.
  - The password must not be the same as any previous password used.
  - The password must be **alphanumeric** (a mix of letters and numbers) and must contain **at least one capital letter**.
- If any of these rules are **not followed**, a small **error message/popup** appears immediately, telling the user what went wrong — so they can correct it          before submitting again.
- All other fields (Email, Security Question, Security Answer) simply need to be filled in — nothing left blank.

  Once everything is correct, clicking **Create Account** successfully registers the user, and it takes them to the **Home/Login screen**.

---

  Step 6: Logging In

On the Home screen, the user can now log in using their registered Username/Email and Password.

- If correct → user is taken to their **Dashboard**.
- If incorrect → a generic error is shown (it doesn't tell the user whether the username or the password was wrong — this keeps the login more secure).

---

 Step 7: Forgot Password — What Happens If the User Forgets Their Password

If the user forgets their password, they click **Forgot Password**. This opens a page with **two different ways** to reset it:

 Option 1: Reset via Security Question
1. The user enters their **username**.
2. They answer the **security question** they set during signup.
3. If the answer is correct, they can immediately set a **new password**.
4. Password is reset successfully.

 Option 2: Reset via OTP (One-Time Password)
1. The user enters their **registered email address**.
2. The system automatically sends a **6-digit OTP code** to that email (using the Gmail App Password we set up earlier).
3. The user checks their email, copies the OTP, and enters it into the portal.
4. Once the OTP is verified as correct, the user can set a **new password**.
5. Password is reset successfully.

Both options end the same way — the user gets a new password and can now log in normally.

---

 Quick Summary (In One Paragraph)

We open Colab, create a notebook, and set up 4 secret keys — an app password and email for sending OTPs, a JWT key for secure sessions, and an ngrok token to make the app public. Once we run the notebook, it gives us a link to our Infosys portal. There, new users sign up (with strong password rules enforced, showing error popups if rules are broken), then log in. If someone forgets their password, they can reset it either by answering a security question or by verifying an OTP sent to their email — both ending with a fresh, working password.