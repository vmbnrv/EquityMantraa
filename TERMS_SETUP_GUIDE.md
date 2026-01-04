# Adding Terms & Conditions with Required Checkbox

## Step 1: Create Terms & Conditions Page in Ghost

1. Go to **https://equitymantraa.com/ghost** (admin panel)
2. Click **Pages** → **New Page**
3. Title: "Terms and Conditions"
4. Copy content from `terms-and-conditions-template.md`
5. Customize the content:
   - Update [Your Jurisdiction]
   - Update [Your email]
   - Update [Your business address]
   - Modify sections as needed
6. **Page Settings** (gear icon):
   - URL: `terms-and-conditions`
   - Make it a **static page** (not a post)
7. **Publish** the page

## Step 2: Add Terms Checkbox to Member Portal

1. In Ghost admin, go to **Settings → Design & branding**
2. Click **Code injection**
3. In **Site Header** section, paste the entire content from `ghost-terms-script.html`
4. Click **Save**

## Step 3: Test the Implementation

### Test Signup Flow:
1. Open incognito window
2. Go to your site and click "Subscribe" or "Sign in"
3. You should see:
   - Terms checkbox appears
   - "I agree to the Terms & Conditions" text with clickable link
   - Cannot submit without checking the box
   - Error message if trying to submit unchecked

### Test Signin Flow:
1. Existing users should also see the checkbox
2. Must check to proceed with login

## Step 4: Verify Terms Page

1. Click the "Terms & Conditions" link in the checkbox
2. Should open in new tab
3. Page should be accessible at: https://equitymantraa.com/terms-and-conditions/

## How It Works

### The Custom Script:
- Detects when Ghost member portal opens
- Injects a checkbox before the submit button
- Validates checkbox is checked before allowing submission
- Shows error message if unchecked
- Links to your Terms page

### Features:
✅ Required checkbox for both signup AND signin
✅ Clickable link to Terms page (opens in new tab)
✅ Error validation before form submission
✅ Works with Ghost's member portal iframe
✅ Styled to match Ghost's default portal design

## Customization Options

### Change Checkbox Text:
In the script, find:
```javascript
I agree to the <a href="/terms-and-conditions/" target="_blank">Terms & Conditions</a>
```
Change the text or link as needed.

### Change Link Color:
In the `<style>` section:
```css
.gh-portal-terms a {
  color: #667eea;  /* Change this color */
}
```

### Change Error Message:
Find:
```javascript
errorDiv.textContent = 'You must accept the Terms & Conditions to continue';
```

### Add Privacy Policy:
Modify the label to:
```javascript
I agree to the <a href="/terms-and-conditions/" target="_blank">Terms & Conditions</a> 
and <a href="/privacy-policy/" target="_blank">Privacy Policy</a>
```

## Troubleshooting

**Checkbox not appearing:**
- Clear browser cache and reload
- Check code injection was saved
- Open browser console to check for errors
- Verify the script is in **Site Header** (not footer)

**Terms page not found:**
- Verify page URL is exactly `terms-and-conditions`
- Make sure page is published (not draft)
- Check page visibility is "Public"

**Checkbox not validating:**
- Check browser console for JavaScript errors
- Ensure Ghost portal is fully loaded
- Try in incognito/private window

**Link not opening:**
- Verify the href path matches your page URL
- Check `target="_blank"` is present for new tab

## Legal Compliance Tips

1. **Update regularly**: Review terms annually
2. **Notify users**: Email subscribers if terms change materially
3. **Version history**: Keep old versions with dates
4. **Professional review**: Have a lawyer review your terms
5. **GDPR compliance**: Add data protection clauses if serving EU users
6. **Age verification**: Consider adding age confirmation
7. **State-specific**: Add state-specific disclosures if required

## Additional Pages to Consider

After Terms, consider adding:
- **Privacy Policy** (often required by law)
- **Cookie Policy** (for GDPR)
- **Disclaimer** (investment/financial content)
- **Refund Policy** (for transparency)

## Footer Links

Add these pages to your site footer for easy access:

**Settings → Design & branding → Site navigation:**
- Add "Terms & Conditions"
- Add "Privacy Policy"
- Add "Contact"

---

**You're done!** Users must now accept Terms & Conditions before signing up or signing in.
