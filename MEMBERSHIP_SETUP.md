# Equity Mantra - Membership & Paywall Setup Guide

## ✅ What's Installed
- **Source Theme** - Optimized for membership and newsletters
- **Ghost 5** - Built-in member system with paywalls
- **Resend SMTP** - Email delivery for member signup/notifications
- **Caddy SSL** - Automatic HTTPS

---

## 🎯 How to Configure Paywalls & Member Content

### Step 1: Enable Members (one-time setup)
1. Go to **https://equitymantraa.com/ghost** (admin panel)
2. **Settings → Members** → Toggle "Enable Members" ON
3. Configure:
   - **Portal name**: "Equity Mantra Members"
   - **Portal button color**: Your brand color
   - Save

### Step 2: Set Up Paid Subscriptions
1. **Settings → Subscriptions**
2. Create tiers:
   - **Free** (default)
   - **Premium** ($9.99/mo or your price)
3. Add tier descriptions and benefits
4. Save

### Step 3: Configure Payment (Stripe)
To accept payments:
1. **Settings → Billing & Plan**
2. Connect your **Stripe account**
3. Choose monthly/annual billing
4. Verify payment settings

---

## 📝 Creating Gated Content

### **Preview + Paywall (Popular)**
1. Create a new post
2. Add your **teaser/preview text** (visible to everyone)
3. Click **Post Settings** (gear icon)
   - Click **Visibility** dropdown
   - Select **"Members only"** or **"Paid members only"**
4. Publish

**What happens:**
- Non-members see preview + "Subscribe to read more" button
- Free members see full post
- Paid members see full post (if "Paid only")

### **Members-Only Post**
1. Create post
2. **Visibility** → **"Members only"**
3. Full post hidden from non-members

### **Paid-Only Post**
1. Create post
2. **Visibility** → **"Paid members only"**
3. Only paid subscribers can read

---

## 🎨 Customizing Paywall Styling

The theme includes built-in paywall CSS classes. To customize:

1. Go to **Settings → Design & branding → Code injection**
2. **Site Header** → Add custom CSS:

```css
/* Change paywall button color */
.gh-content-paywall a {
  background: #your-color !important;
  color: white !important;
}

/* Change paywall background */
.gh-content-paywall {
  background: linear-gradient(135deg, #color1 0%, #color2 100%) !important;
}

/* Customize member label */
.post-access-label.members-only {
  background: #e8f5e9 !important;
  color: #2e7d32 !important;
}
```

---

## 📧 Member Email Notifications

Ghost automatically sends emails:
- **Welcome email** when new member signs up
- **Magic link** for passwordless login
- **Newsletter emails** to subscribers
- **Payment receipts** for Stripe payments

All configured to send from: `noreply@equitymantraa.com` via Resend

---

## 📊 Viewing Member Analytics

1. **Settings → Members**
   - See member count
   - View signup trends
   - Export member list

2. **Settings → Subscriptions**
   - MRR (Monthly Recurring Revenue)
   - Subscription count by tier
   - Revenue breakdown

---

## 🔗 Member Portal Features

Members see at: **https://equitymantraa.com/#/portal/**

They can:
- Sign in with email (magic link)
- Manage subscription
- Change payment method
- View newsletter preferences
- Download receipts

---

## 🔧 Advanced: Custom Portal Button

To customize the member portal button on your site:

**Settings → Design & branding → Code injection → Site Header:**

```html
<script>
  const portal = document.querySelector('[data-ghost-portal]');
  if (portal) {
    portal.style.background = '#your-color';
  }
</script>
```

---

## 📱 Email Branding

All member emails are sent from Resend with your domain. To customize:

1. Go to **Settings → Email newsletter**
2. Configure:
   - **From address**: noreply@equitymantraa.com
   - **Logo**: Add your logo
   - **Accent color**: Your brand color
   - **Footer text**: Add copyright/unsubscribe info

---

## 💡 Content Strategy Tips

1. **Hook with preview**: Use first 100-200 words as teaser
2. **Clear paywall message**: "This is exclusive member content"
3. **Value prop**: Show what they get with membership
4. **Multiple tiers**: Free + Premium encourages upgrades
5. **Newsletter**: Build email list to convert to paid

---

## 🚀 Testing Paywalls

1. **Incognito window**: See non-member view
2. **Sign up test**: Create test email account
3. **Check paywall**: Verify "Subscribe" button appears
4. **Stripe test**: Use test card `4242 4242 4242 4242`

---

## ❓ Common Issues

**Paywall not showing?**
- Check post visibility (Settings → Visibility)
- Clear browser cache
- Verify Ghost restart: `docker-compose logs ghost | tail -20`

**Emails not sending?**
- Check Resend dashboard for delivery
- Verify domain is verified on Resend
- Check Ghost logs: `docker-compose logs ghost | grep -i email`

**Payment not working?**
- Ensure Stripe account connected
- Test with Stripe test mode enabled
- Check Stripe dashboard for errors

---

## 📞 Next Steps

1. Enable Members in Ghost admin ✅
2. Create test post with paywall
3. Sign up with test email
4. Try subscribing (if using Stripe)
5. Customize colors in Design settings
6. Launch publicly!

Enjoy your membership business! 🎉
