# Contact Module Setup Guide (Web3Forms)

## Overview

The Contact Module (`ContactHT.jsx`) provides integrated contact and scheduling functionality using:
- **Web3Forms** for contact form email delivery (**100% FREE, unlimited emails**)
- **Calendly** for appointment scheduling

---

## Prerequisites

- HEKTEK City project running locally
- Email address to receive form submissions
- (Optional) Calendly account for scheduling

---

## Setup Instructions

### 1. Web3Forms Configuration (2 minutes!)

#### Step 1.1: Get Free Access Key
1. Go to https://web3forms.com/
2. Click **"Get Started - It's Free"**
3. Enter your email address (`hmosqueraturner@gmail.com`)
4. You'll receive an **Access Key** instantly via email
5. **Copy the Access Key** (format: `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`)

That's it! No account creation, no templates, no complicated setup.

---

### 2. Calendly Configuration

#### Step 2.1: Create Calendly Account
1. Go to https://calendly.com/
2. Sign up for free account
3. Connect your Google Calendar for auto-sync

#### Step 2.2: Create Event Type
1. Dashboard → **Event Types** → **Create**
2. Configure:
   - **Name**: "30 Min Call" (or your preference)
   - **Duration**: 30 minutes
   - **Location**: Phone/Zoom/Google Meet
   - **Availability**: Set your available hours

3. Click **Save & Close**

#### Step 2.3: Get Scheduling URL
1. Find your new event in Event Types list
2. Click **Copy Link**
3. URL format: `https://calendly.com/USERNAME/event-name`
4. Example: `https://calendly.com/hmosqueraturner/30min`

---

### 3. Environment Variables

#### Step 3.1: Create .env File
In project root:
```bash
# Create .env if it doesn't exist
touch .env
```

#### Step 3.2: Add Web3Forms Access Key
Edit `.env`:
```env
# Web3Forms Configuration
VITE_WEB3FORMS_ACCESS_KEY=your_access_key_here

# Optional rate limiting (already configured)
VITE_CONTACT_RATE_LIMIT_HOURS=1
VITE_CONTACT_MAX_ATTEMPTS=3
```

**IMPORTANT**: Never commit `.env` to git! It's already in `.gitignore`.

#### Step 3.3: Verify about.json
File: `src/config/about.json`

Ensure calendlyUrl is set:
```json
{
  "profile": {
    "email": "hmosqueraturner@gmail.com",
    "calendlyUrl": "https://calendly.com/hmosqueraturner/30min"
  }
}
```

---

### 4. Testing

#### Test Contact Form
1. Restart dev server: `npm run dev`
2. Navigate to About section
3. Scroll to "Get in Touch"
4. Fill out contact form:
   - Name: Test User
   - Email: test@example.com
   - Subject: Test message
   - Message: This is a test from HEKTEK City

5. Click **SEND MESSAGE**
6. **Verify**:
   - Success message appears: "✅ Message sent successfully!"
   - Email received at `hmosqueraturner@gmail.com`
   - Check spam folder if not in inbox

#### Test Calendly
1. Scroll to "Schedule a Call" section
2. **Verify**:
   - Calendly iframe loads
   - Available time slots visible
   - Can select date/time
   - Booking flow works

#### Test Rate Limiting
1. Send 3 messages quickly
2. Try 4th message
3. **Verify**: Error message "⏱️ Too many attempts. Please wait..."

---

## Troubleshooting

### Contact Form Issues

**"Web3Forms access key not configured"**
- Check `.env` file exists in project root
- Verify `VITE_WEB3FORMS_ACCESS_KEY` is set
- Restart dev server after changing `.env`

**Email not received**
- Check spam folder  
- Verify Access Key is correct (check email from Web3Forms)
- Check browser console for errors
- Try sending test directly from web3forms.com dashboard

**Rate limiting not working**
- Check browser localStorage (DevTools → Application)
- Clear localStorage: `localStorage.clear()`

### Calendly Issues

**Iframe not loading**
- Verify `calendlyUrl` in `about.json`
- Check URL format (must be full URL with `https://`)
- Check browser console for CORS errors

**Wrong timezone**
- Calendly automatically uses user's browser timezone
- Can be changed in Calendly account settings

---

## Customization

### Form Fields
To add/remove fields, edit `ContactHT.jsx`:

```javascript
<Form.Item
  name="company"  // Add new field
  label={<span style={labelStyle}>Company</span>}
>
  <Input placeholder="Your company" />
</Form.Item>
```

Web3Forms will automatically include all form fields in the email!

### Email Notifications
Web3Forms options (add to formData in ContactHT.jsx):

```javascript
const formData = {
  access_key: accessKey,
  // ... other fields
  from_name: "HEKTEK City", // Custom sender name
  subject: `New contact from ${values.name}`, // Custom subject
  replyto: values.email, // Reply-to address
};
```

### Styling
Colors defined in `ContactHT.jsx`:
```javascript
const COLORS = {
  neonBlue: "#00F0FF",
  neonRed: "#BB1111",
  // ... modify as needed
};
```

---

## Costs & Limits

### Web3Forms Free (Forever)
- **Unlimited emails** ✅
- **No monthly limits** ✅
- **Spam protection included** ✅
- **No credit card required** ✅
- **No account needed** ✅

### Calendly Free Tier
- **1 event type**
- **Unlimited bookings**
- **1 calendar connection**

Upgrade (optional): $10/month for unlimited event types

---

## Security Best Practices

✅ **Implemented**:
- Web3Forms Access Key (client-safe for contact forms)
- Client-side rate limiting (3 sends/hour)
- Email format validation
- Honeypot field support (can be added)

---

## Web3Forms vs EmailJS

| Feature | Web3Forms | EmailJS |
|---------|-----------|---------|
| Cost | **FREE forever** | $4/month after 200 emails |
| Email Limit | **Unlimited** | 200/month free |
| Setup Time | **2 minutes** | 15 minutes |
| Configuration | **1 Access Key** | 3 keys + template |
| Account Required | **No** | Yes |
| Spam Protection | **Included** | Manual |

---

## Support

- **Web3Forms Docs**: https://docs.web3forms.com/
- **Calendly Docs**: https://developer.calendly.com/
- **Component Issues**: Check browser console for errors

---

## Advanced Features (Optional)

### Auto-Reply
Add to formData:
```javascript
botcheck: false, // Disable bot check if needed
autoresponse: {
  enabled: true,
  subject: "Thanks for contacting HEKTEK City!",
  message: "I received your message and will respond soon."
}
```

### Webhooks
Configure in Web3Forms dashboard to trigger actions on form submission.

### Custom Redirect
```javascript
redirect: "https://hektek-city.vercel.app/thank-you"
```

---

**Status**: ✅ Setup guide complete - Web3Forms is way simpler!
