## STRIPE FLOW
1. stripe.paymentIntents.create() with total_amount and 
2. stripe API will return client_secret, grab it. *client_secret is just transaction info  
3. loadup stripe kit and send card info: stripe.confirmCardPayment() with client_secret 




1. function - createPaymentIntent.js 

// this is where you prepare the router 
// the front-end will send you here the info (through POST) about - 
// cart, shipping fee, amount in the event.body 
// then you'd process by stripe.paymentIntents.create() 
// then you'd return clientSecret: paymentIntent.client_secret to the front-end 

2. component - StripeCheckout.js 

// load up stripe with SECRET_KEY, and embed that in Stripe's Elements component (for state/data management in the components) 
// initially, useEffect will invoke createPaymentIntent(), which sends cart, amount, fee info to the function/createPaymentIntent.router 
// receive back the clientSecret: paymentIntent.client_secret, 
// then setClientSecret this data 

  const createPaymentIntent = async () => {
    try {
      const { data } = await axios.post(
        '/.netlify/functions/create-payment-intent',
        JSON.stringify({ cart, shipping_fee, total_amount })
      )

      setClientSecret(data.clientSecret)
    } catch (error) {
      // console.log(error.response)
    }
  }
  
// then, the Stripe's Element has CardElement that takes and process the card info
// user enters this, and then handleSubmit will stripe.confirmCardPayment() and sends it 
// if payment goes through without errors, navigate to the "/" 

  const handleSubmit = async (ev) => {
    ev.preventDefault()
    setProcessing(true)
    const payload = await stripe.confirmCardPayment(clientSecret, {
      payment_method: {
        card: elements.getElement(CardElement),
      },
    })
    if (payload.error) {
      setError(`Payment failed ${payload.error.message}`)
      setProcessing(false)
    } else {
      setError(null)
      setProcessing(false)
      setSucceeded(true)
      setTimeout(() => {
        clearCart()
        history.push('/')
      }, 10000)
    }
  }















