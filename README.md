# how-to-mailgun-for-small-biz
Basic setup for using mailgun to handle your company email addresses

## You will need...

* A (free) mailgun account
* Ownership of a domain and permission to edit its DNS records

## Set up your domain - mailgun

We'll be using `custom.com` as the example domain

* In the sidebar, go to `sending > domains` and hit "Add new domain"
* Mailgun suggests using a subdomain - `mg.custom.com`
![image](https://user-images.githubusercontent.com/11595920/58003821-8b980b00-7ad9-11e9-97dc-7bcf1a232fac.png)
* Choose EU or US servers as appropriate for your business before submitting
* Now submit and you'll be taken to the DNS records you need to add on your DNS provider's site

## Set up your domain - DNS provider

* Go to your DNS provider's domain settings page
* Enter the TXT records:
![image](https://user-images.githubusercontent.com/11595920/58004291-d9614300-7ada-11e9-83b8-ada848f4b6ae.png)
* Then the MX records:
![image](https://user-images.githubusercontent.com/11595920/58004343-ef6f0380-7ada-11e9-8ae8-9b4451dadd40.png)
* And finally the CNAME record if you want to track opens / clicks etc.
![image](https://user-images.githubusercontent.com/11595920/58004613-8fc52800-7adb-11e9-8511-bcc5d27f8d9f.png)


## Wait for DNS to update
![image](https://user-images.githubusercontent.com/11595920/58004529-57254e80-7adb-11e9-8649-5c38cf972808.png)

## Set up your routes (forwarding addresses)

* In the sidebar, go to `receiving` and hit "Add route" at top right

### Catch-all

First we'll set up a catch-all email address. If an inbound email is received and mailgun can't match it to a route you've set up, this tells mailgun what to do with it. This is especially useful because you don't want to miss an email just because someone's mistyped an address.

* Under "Expression type" select "Catch all"
* Add your preferred forwarding address under "forwarding" (making sure to hit the tickbox)
  * This could be an external address, or an address under your own domain (e.g. `info@custom.com`), in which case you'll need to set that route up as well (see next section)
* Under priority, set it to 999 - we want this to be the last route that's evaluated, so it's only reached if no other matches are found.
  * (You could also hit the **Stop** tickbox, but this should be redundant if there are no routes with a lower priority than this one)
* Now create the route and you're done with the catch-all
  * At this point, any emails sent to your domain will be dealt with by the catch-all, because that's the only route we've set up

### Specific recipients

Now we'll add a company email address and tell mailgun to forward matching messages to a personal email address. You can do this as many times as you need.

* Under "Expression type", select "Match Recipient"
* Add your preferred company address as the recipient  - e.g. `rubie@custom.com`
* Add your preferred forwarding address under "forwarding" (making sure to hit the tickbox)
* Your settings should now look like this:
![image](https://user-images.githubusercontent.com/11595920/58005695-21ce3000-7ade-11e9-8760-0235f718d1ef.png)
* Tick the **Stop** box
  * Mailgun goes through all routes and checks whether the inbound email matches a recipient / header / catch-all. If there are multiple matches, mailgun will forward the message multiple times. So a message sent to `rubie@custom.com` will be sent to `rubie@personal.com` AND to the catch-all forwarding address (e.g. `hello@custom.com`). This is why I suggest ticking the **Stop** box
![image](https://user-images.githubusercontent.com/11595920/58006380-e2084800-7adf-11e9-8207-3b556edf5f53.png)
* Hit "Create Route" at the bottom and once the DNS records have finished propagating, messages sent to your company address will be forwarded to your forwarding address!

### info@ hello@ support@ etc
Depending on your team size / responsibilities / etc., it might be a good idea to set support@ to go to the developers, and hello@ to go to the marketing team. (Since there are only two of us in my biz, we just send everything to both of us - which is useful for us but might be annoying for others.)

### Advanced setup
You can also 
* Set mailgun to store inbound messages for you to access via the API
* Set the forwarding address to a URL as well as an email address (e.g. if you use Zapier to trigger an action when you receive a support query)

## Sending mail from your newly set-up account

(This is for gmail - you may need to check your email provider's docs, but it shouldn't be *too* different)

* Go to Settings > Accounts & Import
* Under "Send mail as", hit "Add another email address"
* Enter your display name and email address, and go to the next step
* The SMTP server is smtp.mailgun.org
* The port is 587, using TLS
* The login is `postmaster@mg.custom.com` - i.e. `postmaster@` + the subdomain you set up on mailgun
* The password is available at Mailgun - Sidebar > Domain Settings > SMTP Credentials
* Now submit this and google will send a test email to make sure you actually do have access to the address you're claiming, and then you're done!
