# 💳 Payment Gateway Integration Guide

This guide will help you integrate payment gateways into your Apartment Maintenance Manager app when you're ready.

## 🎯 Recommended Payment Gateways

### 1. Stripe (Recommended) ⭐
- **Best for:** International payments
- **Fees:** 2.9% + $0.30 per transaction
- **Features:** Cards, ACH, wallets, subscriptions
- **Website:** [stripe.com](https://stripe.com)

### 2. PayPal
- **Best for:** Familiar brand, buyer protection
- **Fees:** 2.9% + $0.30 per transaction
- **Features:** PayPal balance, cards, PayPal Credit
- **Website:** [paypal.com](https://paypal.com)

### 3. Razorpay (India)
- **Best for:** Indian market
- **Fees:** 2% per transaction
- **Features:** UPI, cards, net banking, wallets
- **Website:** [razorpay.com](https://razorpay.com)

### 4. Square
- **Best for:** Small businesses
- **Fees:** 2.6% + $0.10 per transaction
- **Features:** Cards, digital wallets
- **Website:** [squareup.com](https://squareup.com)

---

## 🚀 Stripe Integration (Step-by-Step)

### Step 1: Install Dependencies

```bash
npm install stripe @stripe/stripe-js
```

### Step 2: Get API Keys

1. Sign up at [stripe.com](https://stripe.com)
2. Go to Developers → API Keys
3. Copy your **Publishable key** and **Secret key**

### Step 3: Add Environment Variables

Create `.env.local`:
```env
NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY=pk_test_...
STRIPE_SECRET_KEY=sk_test_...
```

### Step 4: Create Payment API Route

Create `app/api/create-payment-intent/route.js`:

```javascript
import { NextResponse } from 'next/server'
import Stripe from 'stripe'

const stripe = new Stripe(process.env.STRIPE_SECRET_KEY)

export async function POST(request) {
  try {
    const { amount, description } = await request.json()

    const paymentIntent = await stripe.paymentIntents.create({
      amount: Math.round(amount * 100), // Convert to cents
      currency: 'usd',
      description: description,
      automatic_payment_methods: {
        enabled: true,
      },
    })

    return NextResponse.json({ 
      clientSecret: paymentIntent.client_secret 
    })
  } catch (error) {
    return NextResponse.json(
      { error: error.message },
      { status: 500 }
    )
  }
}
```

### Step 5: Create Payment Component

Create `app/components/PaymentForm.js`:

```javascript
'use client'

import { useState } from 'react'
import { loadStripe } from '@stripe/stripe-js'
import {
  Elements,
  PaymentElement,
  useStripe,
  useElements,
} from '@stripe/react-stripe-js'

const stripePromise = loadStripe(process.env.NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY)

function CheckoutForm({ amount, description, onSuccess }) {
  const stripe = useStripe()
  const elements = useElements()
  const [loading, setLoading] = useState(false)
  const [error, setError] = useState(null)

  const handleSubmit = async (e) => {
    e.preventDefault()

    if (!stripe || !elements) return

    setLoading(true)
    setError(null)

    try {
      const { error: submitError } = await stripe.confirmPayment({
        elements,
        confirmParams: {
          return_url: `${window.location.origin}/payment-success`,
        },
      })

      if (submitError) {
        setError(submitError.message)
      } else {
        onSuccess()
      }
    } catch (err) {
      setError(err.message)
    } finally {
      setLoading(false)
    }
  }

  return (
    <form onSubmit={handleSubmit} style={{ maxWidth: '500px', margin: '0 auto' }}>
      <PaymentElement />
      
      {error && (
        <div style={{ color: 'red', marginTop: '1rem' }}>
          {error}
        </div>
      )}

      <button
        type="submit"
        disabled={!stripe || loading}
        className="btn btn-primary"
        style={{ width: '100%', marginTop: '1rem' }}
      >
        {loading ? 'Processing...' : `Pay $${amount.toFixed(2)}`}
      </button>
    </form>
  )
}

export default function PaymentForm({ amount, description, onSuccess }) {
  const [clientSecret, setClientSecret] = useState('')

  useState(() => {
    fetch('/api/create-payment-intent', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ amount, description }),
    })
      .then((res) => res.json())
      .then((data) => setClientSecret(data.clientSecret))
  }, [amount, description])

  const options = {
    clientSecret,
    appearance: {
      theme: 'stripe',
      variables: {
        colorPrimary: '#3b82f6',
      },
    },
  }

  return (
    <div className="card" style={{ padding: '2rem' }}>
      <h2>Complete Payment</h2>
      <p style={{ marginBottom: '2rem' }}>
        Amount: <strong>${amount.toFixed(2)}</strong>
      </p>
      
      {clientSecret && (
        <Elements stripe={stripePromise} options={options}>
          <CheckoutForm 
            amount={amount} 
            description={description}
            onSuccess={onSuccess}
          />
        </Elements>
      )}
    </div>
  )
}
```

### Step 6: Add Payment to Expenses

Update `app/components/Expenses.js` to include payment option:

```javascript
// Add to the expense form
<div className={styles.inputGroup}>
  <label>Payment Status</label>
  <select
    className="input"
    value={formData.paymentStatus}
    onChange={(e) => setFormData({...formData, paymentStatus: e.target.value})}
  >
    <option value="pending">Pending</option>
    <option value="paid">Paid</option>
    <option value="processing">Processing</option>
  </select>
</div>

{formData.paymentStatus === 'pending' && (
  <button
    type="button"
    onClick={() => setShowPayment(true)}
    className="btn btn-success"
  >
    💳 Pay Now
  </button>
)}

{showPayment && (
  <PaymentForm
    amount={parseFloat(formData.amount)}
    description={formData.description}
    onSuccess={() => {
      setFormData({...formData, paymentStatus: 'paid'})
      setShowPayment(false)
      alert('Payment successful!')
    }}
  />
)}
```

---

## 🔐 Security Best Practices

### 1. Never expose secret keys
```javascript
// ❌ WRONG - Don't do this
const stripe = new Stripe('sk_live_...')

// ✅ CORRECT - Use environment variables
const stripe = new Stripe(process.env.STRIPE_SECRET_KEY)
```

### 2. Validate amounts server-side
```javascript
// In your API route
if (amount < 0.50) {
  return NextResponse.json(
    { error: 'Minimum amount is $0.50' },
    { status: 400 }
  )
}
```

### 3. Use webhooks for confirmation
```javascript
// app/api/webhooks/stripe/route.js
import { headers } from 'next/headers'
import Stripe from 'stripe'

const stripe = new Stripe(process.env.STRIPE_SECRET_KEY)

export async function POST(request) {
  const body = await request.text()
  const signature = headers().get('stripe-signature')

  let event
  try {
    event = stripe.webhooks.constructEvent(
      body,
      signature,
      process.env.STRIPE_WEBHOOK_SECRET
    )
  } catch (err) {
    return new Response(`Webhook Error: ${err.message}`, { status: 400 })
  }

  // Handle the event
  switch (event.type) {
    case 'payment_intent.succeeded':
      const paymentIntent = event.data.object
      // Update your database
      console.log('Payment succeeded:', paymentIntent.id)
      break
    case 'payment_intent.payment_failed':
      // Handle failed payment
      console.log('Payment failed')
      break
  }

  return new Response(JSON.stringify({ received: true }))
}
```

---

## 💡 Use Cases for Your App

### 1. Maintenance Fee Collection
```javascript
// Monthly maintenance fees
const monthlyFee = {
  amount: 500.00,
  description: 'Monthly Maintenance Fee - January 2024',
  ownerId: owner.id,
  apartmentNumber: owner.apartmentNumber
}
```

### 2. One-time Repairs
```javascript
// Repair costs
const repairPayment = {
  amount: 150.00,
  description: 'Plumbing repair - Apartment 301',
  taskId: task.id,
  ownerId: owner.id
}
```

### 3. Utility Bills
```javascript
// Utility payments
const utilityBill = {
  amount: 75.50,
  description: 'Electricity Bill - December 2024',
  category: 'utilities',
  ownerId: owner.id
}
```

### 4. Special Assessments
```javascript
// Building improvements
const assessment = {
  amount: 1000.00,
  description: 'Roof Repair Special Assessment',
  dueDate: '2024-03-31',
  installments: 4 // Optional: split into payments
}
```

---

## 📊 Payment Tracking Features

### Add to your data model:

```javascript
const expenseWithPayment = {
  id: '...',
  description: '...',
  amount: 500.00,
  
  // Payment fields
  paymentStatus: 'paid', // pending, processing, paid, failed
  paymentMethod: 'stripe',
  paymentId: 'pi_...',
  paidAt: '2024-01-15T10:30:00Z',
  paidBy: owner.id,
  
  // Receipt
  receiptUrl: 'https://stripe.com/receipts/...',
  invoiceNumber: 'INV-2024-001'
}
```

---

## 🔄 Recurring Payments (Subscriptions)

For monthly maintenance fees:

```javascript
// Create subscription
const subscription = await stripe.subscriptions.create({
  customer: customerId,
  items: [{ price: 'price_monthly_maintenance' }],
  payment_behavior: 'default_incomplete',
  payment_settings: { save_default_payment_method: 'on_subscription' },
  expand: ['latest_invoice.payment_intent'],
})
```

---

## 📱 Mobile Payment Options

### Apple Pay & Google Pay

Stripe automatically enables these when available:

```javascript
const paymentIntent = await stripe.paymentIntents.create({
  amount: amount * 100,
  currency: 'usd',
  automatic_payment_methods: {
    enabled: true, // Enables Apple Pay, Google Pay, etc.
  },
})
```

---

## 🧪 Testing

### Test Card Numbers (Stripe)

```
Success: 4242 4242 4242 4242
Decline: 4000 0000 0000 0002
Insufficient funds: 4000 0000 0000 9995
3D Secure: 4000 0025 0000 3155

Expiry: Any future date
CVC: Any 3 digits
ZIP: Any 5 digits
```

---

## 📈 Next Steps

1. **Start with test mode** - Use test API keys
2. **Implement basic payment** - Single payments first
3. **Add webhooks** - For payment confirmation
4. **Test thoroughly** - All payment scenarios
5. **Go live** - Switch to live API keys
6. **Monitor** - Use Stripe Dashboard

---

## 🆘 Support Resources

- **Stripe Docs:** [stripe.com/docs](https://stripe.com/docs)
- **Stripe Support:** Available 24/7
- **Test Mode:** Practice without real money
- **Stripe CLI:** For local webhook testing

---

## ✅ Checklist Before Going Live

- [ ] Test all payment flows
- [ ] Implement error handling
- [ ] Add payment confirmation emails
- [ ] Set up webhook endpoints
- [ ] Configure receipt generation
- [ ] Add refund functionality
- [ ] Test on mobile devices
- [ ] Review Stripe Dashboard
- [ ] Set up monitoring/alerts
- [ ] Comply with PCI requirements

---

## 💰 Pricing Comparison

| Gateway | Transaction Fee | Setup Fee | Monthly Fee |
|---------|----------------|-----------|-------------|
| Stripe | 2.9% + $0.30 | $0 | $0 |
| PayPal | 2.9% + $0.30 | $0 | $0 |
| Square | 2.6% + $0.10 | $0 | $0 |
| Razorpay | 2% | $0 | $0 |

---

## 🎯 When to Integrate Payments

Integrate payments when:
- ✅ Your app has active users
- ✅ You need to collect maintenance fees
- ✅ You want to automate billing
- ✅ You're ready to handle real money
- ✅ You have proper legal/tax setup

**For now:** Your app works perfectly without payments. Add this feature when you're ready to monetize or collect fees.

---

**Note:** This is a future enhancement. Your current app is fully functional for tracking expenses manually. Payment integration can be added anytime when needed.
