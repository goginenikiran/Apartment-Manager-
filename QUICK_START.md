# ⚡ Quick Start Guide

Get your AptNest up and running in 5 minutes!

## 🎯 What You Have

A complete apartment maintenance management system that works on:
- ✅ Web browsers (Chrome, Safari, Firefox, Edge)
- ✅ Android phones and tablets
- ✅ iOS (iPhone and iPad)
- ✅ Desktop computers

## 🚀 Step 1: Start the App (Already Running!)

Your app is currently running at:
- **Local:** http://localhost:3000
- **Network:** http://10.0.0.226:3000

To stop: Press `Ctrl+C` in the terminal
To restart: Run `npm run dev` in the apartment-app folder

## 📱 Step 2: Test on Your Phone

### From Your Computer:
1. Make sure your phone is on the **same WiFi** as your computer
2. Open your phone's browser (Chrome or Safari)
3. Go to: **http://10.0.0.226:3000**
4. The app should load!

### Install as App (Android):
1. Open the app in Chrome
2. Tap the menu (⋮) → "Add to Home Screen"
3. Tap "Add" or "Install"
4. App icon appears on your home screen!

### Install as App (iOS):
1. Open the app in Safari
2. Tap the Share button (□↑)
3. Scroll and tap "Add to Home Screen"
4. Tap "Add"
5. App icon appears on your home screen!

## 👤 Step 3: Create Your First User (Multi-Apartment Support)

1. **Open the app** (http://localhost:3000)
2. Click **"Create Account"**
3. **Choose Your Building Mode**:
   - **Create New**: If you are the first committee member, name your apartment (e.g. "Skyline Residency").
   - **Join Existing**: If your building is already set up, enter the **Building ID** provided by your committee.
4. Fill in the personal form:
   - Full Name: Your name
   - Email: your@email.com
   - Phone: Your phone number
   - **Role**: 
     - **Committee Member**: Can add, edit, and delete everything (Full Access)
     - **Regular Member**: Can only view data (View Only)
     - **Administrator**: Full system access (Full Access)
   - Password: Choose a secure password
   - Confirm Password: Same password
4. Click **"Create Account"**
5. You're in! 🎉

> **Note**: If you want to manage expenses and tasks, make sure to select **Committee Member** or **Administrator**. Regular members can see everything but won't see "Add" or "Edit" buttons.

## 🏢 Step 4: Add Your First Owner

1. Click **"Owners"** in the navigation
2. Click **"+ Add Owner"**
3. Fill in the details:
   - Name: John Doe
   - Email: john@example.com
   - Phone: +1 (555) 123-4567
   - Apartment Number: 301
   - Floor Number: 3
   - Ownership Type: Owner
   - Move-in Date: Select a date
   - Notes: (optional)
4. Click **"Add Owner"**
5. Your first owner is added! ✅

## ✅ Step 5: Create Your First Task

1. Click **"Tasks"** in the navigation
2. Click **"+ Create Task"**
3. Fill in the details:
   - Task Title: Fix leaking faucet
   - Description: Kitchen faucet is dripping
   - Assign To: Select the owner you just added
   - Category: Maintenance
   - Priority: Medium
   - Status: Pending
   - Due Date: Select a date
4. Click **"Create Task"**
5. Your first task is created! ✅

## 💰 Step 6: Record Your First Expense

1. Click **"Expenses"** in the navigation
2. Click **"+ Add Expense"**
3. Fill in the details:
   - Description: Plumber service call
   - Amount: 150.00
   - Date: Today's date
   - Category: Repair
   - Payment Method: Cash
   - Related Task: Select the task you created
   - Related Owner: Select the owner
   - Receipt/Notes: Invoice #12345
4. Click **"Add Expense"**
5. Your first expense is recorded! ✅

## 📊 Step 7: View Your Reports

1. Click **"Reports"** in the navigation
2. See your data visualized:
   - Monthly expenses
   - Yearly trends
   - Category breakdown
   - Task completion stats
3. Use the month/year filters to explore different time periods

## 🎨 Step 8: Explore the Dashboard

1. Click **"Dashboard"** in the navigation
2. See your overview:
   - Total owners count
   - Pending tasks
   - Monthly expenses
   - Recent activity
   - Quick statistics

## 🌐 Step 9: Deploy to the Internet (Optional)

Want to access from anywhere? Deploy to Vercel (free!):

```bash
# Install Vercel CLI
npm install -g vercel

# Deploy
cd apartment-app
vercel
```

Follow the prompts, and you'll get a public URL like:
`https://apartment-app-xyz.vercel.app`

See `DEPLOYMENT.md` for more deployment options.

## 💡 Pro Tips

### Data Persistence
- Your data is saved automatically in the browser
- Works offline after first load
- Clearing browser data will delete everything
- For production, consider adding a backend (see README.md)

### Mobile Experience
- Install as PWA for best experience
- Works offline once installed
- Feels like a native app
- Fast and responsive

### Navigation
- Use the top menu to switch between sections
- Your profile shows in the top right
- Click logout to sign out

### Search & Filter
- Owners: Search by name, apartment, or email
- Tasks: Filter by status (All, Pending, In Progress, Completed)
- Reports: Select month and year to view specific periods

## 🆘 Troubleshooting

### App won't load?
- Check if the dev server is running (`npm run dev`)
- Try refreshing the page
- Clear browser cache

### Can't access from phone?
- Ensure phone and computer are on same WiFi
- Check firewall settings
- Try the local URL (localhost:3000) if on the same device

### Data disappeared?
- Check if you're using the same browser
- localStorage is browser-specific
- Don't clear browser data

### Build errors?
```bash
# Clear and reinstall
rm -rf .next node_modules
npm install
npm run dev
```

## 📚 Learn More

- **README.md** - Complete documentation
- **DEPLOYMENT.md** - Hosting guide
- **PAYMENT_INTEGRATION.md** - Add payments later
- **PROJECT_SUMMARY.md** - Full feature list

## ✅ You're Ready!

You now know how to:
- ✅ Start the app
- ✅ Create users
- ✅ Add owners
- ✅ Manage tasks
- ✅ Track expenses
- ✅ View reports
- ✅ Install on mobile
- ✅ Deploy to production

## 🎯 Next Steps

1. **Add more data** - Create multiple owners, tasks, and expenses
2. **Test on mobile** - Install as PWA on your phone
3. **Customize** - Edit colors in `globals.css`
4. **Deploy** - Put it online with Vercel
5. **Expand** - Add backend when ready

## 🎉 Have Fun!

Your apartment maintenance system is ready to use. Start managing your property like a pro! 🏢✨

---

**Need help?** Check the documentation files or review the code comments.

**Ready to deploy?** Run `vercel` in the terminal.

**Want to add payments?** See `PAYMENT_INTEGRATION.md`.
