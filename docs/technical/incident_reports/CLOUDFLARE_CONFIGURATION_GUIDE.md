# Cloudflare Configuration - Step by Step Guide

**Purpose**: Fix SSL/TLS and caching configuration to prevent redirect loops on `/api/*` endpoints.

**Language**: English (Cloudflare Console)

---

## Part 1: SSL/TLS Configuration

### Step 1: Access SSL/TLS Settings

1. **Log in** to Cloudflare Dashboard: https://dash.cloudflare.com
2. **Select** your website: `hectortechno.com`
3. **Click** on **SSL/TLS** in the left sidebar

### Step 2: Set Encryption Mode

1. At the top of the SSL/TLS page, you'll see **"Your SSL/TLS encryption mode is:"**
2. **Check current mode**:
   - ❌ If it says **"Flexible"** → **MUST CHANGE**
   - ❌ If it says **"Full"** → **SHOULD CHANGE**  
   - ✅ If it says **"Full (strict)"** → **CORRECT** (skip to Part 2)

3. **Click** on the current mode to change it
4. **Select**: **"Full (strict)"**
5. **Confirm** the change

**Why**: Vercel always uses HTTPS. If Cloudflare→Vercel connection uses HTTP, Vercel will redirect to HTTPS, causing an infinite loop.

### Step 3: Verify Edge Certificates

1. Still in **SSL/TLS** section
2. **Click** on **"Edge Certificates"** tab
3. **Verify**:
   - ✅ **Status**: Active
   - ✅ **Type**: Universal SSL or Custom
   - ✅ **Hosts**: `hectortechno.com` and `*.hectortechno.com`

**If there are issues**: Wait 15 minutes for certificates to provision, then proceed.

---

## Part 2: Page Rules Configuration

### Step 4: Access Rules

1. **Click** on **"Rules"** in the left sidebar
2. You'll see different rule types. We need **"Page Rules"**
3. **Click** on **"Page Rules"**

### Step 5: Review Existing Rules

**Check if you already have a rule for** `*hectortechno.com/api/*`:

- **If YES**: Click "Edit" on that rule → Go to Step 6
- **If NO**: Click "Create Page Rule" → Go to Step 6

### Step 6: Configure API Page Rule

**URL Pattern**:
```
*hectortechno.com/api/*
```

**Settings to ENABLE**:
- ✅ **Cache Level**: Bypass
- ✅ **Disable Performance** (if available)

**Settings to DISABLE/REMOVE** (if present):
- ❌ **Always Use HTTPS** (remove if exists)
- ❌ **Forwarding URL** (remove if exists)  
- ❌ **Browser Cache TTL** (set to "Respect Existing Headers" or remove)
- ❌ **Edge Cache TTL** (remove if exists)

**Screenshot Example** (what your rule should look like):
```
URL: *hectortechno.com/api/*
Settings:
  • Cache Level: Bypass
```

### Step 7: Save Page Rule

1. **Scroll down**
2. **Click**: **"Save and Deploy"**
3. **Wait** for "Rule saved successfully" message

### Step 8: Verify Rule Order

1. Back in **Page Rules** list
2. **Verify** that the `/api/*` rule is **at the top** or **before** any catch-all rules
3. **If not**: Use the drag handle (≡) to **move it up**

**Why**: Page Rules are processed in order. API rule must come before any general rules.

---

## Part 3: Additional Checks (Optional but Recommended)

### Step 9: Check Origin Rules (If Using New System)

If you're using the newer **"Configuration Rules"** instead of Page Rules:

1. **Click** on **"Rules"** → **"Configuration Rules"**
2. **Check** if there's a rule matching `/api/*`
3. **If YES**: Verify it has:
   - ✅ **Cache eligibility**: Bypass cache
   - ❌ **NO redirect settings**

### Step 10: Turn Off "Always Use HTTPS" for API (if needed)

1. **Go to**: **SSL/TLS** → **Edge Certificates**
2. **Scroll down** to **"Always Use HTTPS"**
3. **If it's ON globally**: You might need a Page Rule to disable it for `/api/*`
   - Add setting: **Automatic HTTPS Rewrites**: Off

---

## Part 4: Purge Cache

### Step 11: Clear Cloudflare Cache

1. **Click** on **"Caching"** in left sidebar
2. **Click** on **"Configuration"**
3. **Click**: **"Purge Everything"**
4. **Confirm** the purge

**Wait**: 30 seconds for cache to clear

---

## Verification Checklist

After completing all steps, verify:

- ✅ **SSL/TLS Mode**: Full (strict)
- ✅ **Page Rule exists**: `*hectortechno.com/api/*` → Cache Level: Bypass
- ✅ **No redirect rules**: For `/api/*`
- ✅ **Cache purged**: Everything cleared
- ✅ **Rules order**: API rule is first

---

## Common Issues

### Issue: "I don't see 'Full (strict)' option"

**Solution**: Your Cloudflare plan might be Free. In that case:
- Use **"Full"** mode (not ideal but acceptable)
- Ensure your Vercel project has a valid SSL certificate

### Issue: "Page Rules limit reached"

**Solution**: Free Cloudflare accounts have 3 Page Rules max
- Delete unused rules, OR
- Use the newer **Configuration Rules** (unlimited on all plans)

### Issue: "Changes not taking effect"

**Solution**:
1. Wait 5 minutes for propagation
2. Purge cache again
3. Test in incognito/private browsing mode

---

## After Cloudflare Configuration

Once you've completed all Cloudflare steps:

1. ✅ **Verify** checkslist above
2. ✅ **Wait** 2-3 minutes for changes to propagate
3. ✅ **Return to this chat** and confirm: "Cloudflare configured"

Then we'll proceed with deploying the `vercel.json` changes to production.

---

**Questions?** Take screenshots of any confusing sections and share them here.
