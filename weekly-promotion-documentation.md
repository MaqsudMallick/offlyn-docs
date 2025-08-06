

# ✉️ Send Weekly Reminder Emails via Trigger.dev

This guide will help you send weekly reminder emails to users with event updates using the Offlyn email automation script — all through the [Trigger.dev](https://app.trigger.dev) dashboard.

---

## ✅ Prerequisites

Before you start, make sure:

* You're added to the Offlyn project in [Trigger.dev](https://app.trigger.dev)
* You have at least one email address you want to send to
* You know the basic event details (name, description, and link)

---

## 🚀 How to Send Reminder Emails

### 1. **Log in to Trigger.dev**

* Visit [https://app.trigger.dev](https://app.trigger.dev)
* Sign in with Offlyn's Google account

---

### 2. **Open the Script**

* In the dashboard, navigate to:
  **`Tasks`**
* From the list of tasks look for
  **`offlyn-send-weekly-promotion-email`**. Click on the menu icon then click **Test Task**

---

### 3. **Enter Your Payload**

Paste a JSON payload like this into the **Test Payload** field:

```json
{
  "emailList": ["your.email@example.com", "your.email2@example.com"],
  "events": [
    {
      "name": "Sunset Jam @ Indiranagar Social",
      "description": "A chill evening with acoustic vibes, great food, and better people.",
      "link": "https://offlyn.life/events/sunset-jam"
    },
    {
      "name": "Indie Flea + Music Fest",
      "description": "Shop local and groove to some live indie bands this Sunday!",
      "link": "https://offlyn.life/events/indie-flea-music-fest"
    }
  ]
}
```

> ✅ **Pro tip**: You can test with your own email first to see the final design.

---

### 4. **Run the Test**

* Click **Run Test**
* Wait a few seconds — you'll see the result at the bottom (`sent`, `skipped`, or `failed`)

---

## 🧪 Example Use Cases

* ✅ Weekly email drops
* ✅ Testing new event combinations
* ✅ Checking design in your inbox before public launch

---

## 🛑 Notes

* The script only sends to **subscribed users**.
* Emails are sent using **AWS SES**. If you're not receiving it, check your promotions folder or spam folder.
* You can add up to 10–15 events if needed.

---

If you run into any issues, reach out to the me. I'm happy to help!


