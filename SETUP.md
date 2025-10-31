# Email Storage and Notification Setup Guide

This guide will help you set up the email storage and notification system for the contact form.

## Prerequisites

You'll need to create free accounts for:
1. **Supabase** (for database storage)
2. **EmailJS** (for email notifications)

---

## Part 1: Supabase Setup (Database Storage)

### Step 1: Create a Supabase Account
1. Go to [https://supabase.com](https://supabase.com)
2. Click "Start your project" and sign up (free)
3. Create a new project
   - Choose a project name
   - Set a database password (save this!)
   - Select a region close to your users

### Step 2: Create the Database Table
1. In your Supabase dashboard, click "SQL Editor"
2. Click "New query"
3. Copy and paste this SQL:

```sql
-- Create the contact_submissions table
CREATE TABLE contact_submissions (
    id BIGSERIAL PRIMARY KEY,
    name TEXT NOT NULL,
    email TEXT NOT NULL,
    phone TEXT NOT NULL,
    message TEXT NOT NULL,
    submitted_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Enable Row Level Security
ALTER TABLE contact_submissions ENABLE ROW LEVEL SECURITY;

-- Create a policy to allow inserts from anyone
CREATE POLICY "Allow public inserts" 
ON contact_submissions 
FOR INSERT 
TO anon 
WITH CHECK (true);

-- Create a policy to allow reading only for authenticated users
CREATE POLICY "Allow authenticated reads" 
ON contact_submissions 
FOR SELECT 
TO authenticated 
USING (true);
```

4. Click "Run" to execute the SQL

### Step 3: Get Your Supabase Credentials
1. Go to "Project Settings" (gear icon in the left sidebar)
2. Click "API"
3. Copy these values:
   - **Project URL** (looks like: `https://xxxxx.supabase.co`)
   - **anon/public key** (a long string starting with `eyJ...`)

---

## Part 2: EmailJS Setup (Email Notifications)

### Step 1: Create an EmailJS Account
1. Go to [https://www.emailjs.com](https://www.emailjs.com)
2. Sign up for free (100 emails/month free)
3. Verify your email address

### Step 2: Connect Your Email Service
1. In the EmailJS dashboard, go to "Email Services"
2. Click "Add New Service"
3. Choose your email provider (Gmail recommended)
4. Follow the connection wizard
5. Note your **Service ID** (e.g., `service_xxxxx`)

### Step 3: Create an Email Template
1. Go to "Email Templates"
2. Click "Create New Template"
3. Set up the template:

**Template Name**: Contact Form Notification

**Subject**: New Contact Form Submission from {{from_name}}

**Content**:
```
Hello {{to_name}},

You have received a new contact form submission:

Name: {{from_name}}
Email: {{from_email}}
Phone: {{phone}}

Message:
{{message}}

---
This is an automated notification from your website contact form.
```

4. Save the template and note the **Template ID** (e.g., `template_xxxxx`)

### Step 4: Get Your EmailJS Public Key
1. Go to "Account" in the left sidebar
2. Find your **Public Key** (looks like: `xxxxx-xxxxxxxxx`)

---

## Part 3: Configure the Website

### Update index.html with Your Credentials

Open `index.html` and find these lines (around line 148):

```javascript
const SUPABASE_URL = 'YOUR_SUPABASE_URL';
const SUPABASE_ANON_KEY = 'YOUR_SUPABASE_ANON_KEY';
const EMAILJS_PUBLIC_KEY = 'YOUR_EMAILJS_PUBLIC_KEY';
const EMAILJS_SERVICE_ID = 'YOUR_EMAILJS_SERVICE_ID';
const EMAILJS_TEMPLATE_ID = 'YOUR_EMAILJS_TEMPLATE_ID';
```

Replace with your actual values:

```javascript
const SUPABASE_URL = 'https://xxxxx.supabase.co';
const SUPABASE_ANON_KEY = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...';
const EMAILJS_PUBLIC_KEY = 'your_public_key';
const EMAILJS_SERVICE_ID = 'service_xxxxx';
const EMAILJS_TEMPLATE_ID = 'template_xxxxx';
```

---

## Part 4: Test the Setup

1. Open `index.html` in a web browser
2. Scroll to the contact form
3. Fill out all fields (name, email, phone, message)
4. Click "Enviar Mensaje"
5. You should see "Enviando mensaje..." then "¡Gracias por tu mensaje!"

### Verify It Works:

**Check Database:**
1. Go to your Supabase dashboard
2. Click "Table Editor"
3. Select `contact_submissions`
4. You should see your test submission

**Check Email:**
1. Check the email inbox you connected to EmailJS
2. You should receive an email with the form submission details

---

## Troubleshooting

### "Error al enviar tu mensaje"
- Check browser console (F12) for error messages
- Verify all credentials are correct
- Make sure you ran the SQL in Supabase correctly

### Email not received
- Check EmailJS dashboard to see if email was sent
- Check spam folder
- Verify email service is properly connected in EmailJS

### Database not saving
- Check Supabase dashboard for errors
- Verify the table was created correctly
- Check that Row Level Security policies are set up

---

## Security Notes

⚠️ **Important Security Information:**

1. **Public Keys are Safe**: The Supabase anon key and EmailJS public key can be safely exposed in client-side code. They have limited permissions.

2. **Row Level Security**: The Supabase setup includes RLS policies that:
   - Allow anyone to INSERT new submissions
   - Only allow authenticated users to READ submissions
   - This means visitors can submit forms but can't view others' submissions

3. **Email Rate Limiting**: EmailJS free tier has a limit of 100 emails/month. For higher volume, upgrade your plan.

---

## Viewing Stored Submissions

To view form submissions:

1. Log into your Supabase dashboard
2. Go to "Table Editor"
3. Click on `contact_submissions`
4. You'll see all submissions with timestamps

You can also:
- Export data to CSV
- Create additional views/queries
- Set up email alerts for new submissions (requires Supabase Pro)

---

## Optional Enhancements

### Add reCAPTCHA to prevent spam
1. Sign up at [https://www.google.com/recaptcha](https://www.google.com/recaptcha)
2. Add the reCAPTCHA widget to the form
3. Verify the token before submitting

### Add form validation
- Phone number format validation
- Email format validation
- Minimum message length

### Set up automated responses
- Use EmailJS to send a confirmation email to the person who submitted the form

---

## Support

If you encounter issues:
- **Supabase**: [https://supabase.com/docs](https://supabase.com/docs)
- **EmailJS**: [https://www.emailjs.com/docs](https://www.emailjs.com/docs)
