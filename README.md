
# MEGA - Make Email Great Again

Make Email Great Again is a [MJML](https://mjml.io) implementation to build responsive actionable emails in ServiceNow.

Email is a pretty rought place to work in today's technology.  By using MJML it at least gives you team a common language to write and make responsive emails across clients.

## What is MJML

MJML stands for "Mailjet Markup Language".  
According to mjml.io

> MJML has been designed to reduce the pain of coding a responsive email. Leveraging its semantic syntax and a rich standard components library, making your email responsive is not an issue anymore.

## How does this work on my instance

This scoped application contains one table (MJML Templates), and one business rule.

When you save a MJML Template, ServiceNow makes a REST call to https://mjml-netlify.netlify.app/.netlify/functions/mjml with your email template and placeholders for your variables.  What do you mean we're sending it to a place?!  [Here's the code](https://github.com/jacebenson/netlify-mjml/blob/main/src/functions/mjml.js), you can fork this and run your own lambda to do it.  

What is placeholder?  A placeholder is a bit of text surrounded by two curly brackets `{{mjml.test}}`

So your instance sends something like the following to that endpoint

```xml
<mjml>
  <mj-body>
    <mj-section>
      <mj-column>
        <mj-image width="100px" src="https://mjml.io/assets/img/logo-small.png"></mj-image>
        <mj-divider border-color="#F45E43"></mj-divider>
        <mj-text 
            font-size="20px"
            color="#F45E43"
            font-family="helvetica">
            Hello {{current.user_name}} from this {{mjml.brand}}.
        </mj-text>
      </mj-column>
    </mj-section>
  </mj-body>
</mjml>
```

Which you get back as transpiled HTML with those placeholders.  
Then we replace the `{{mjml.*}}` with the template variables.  
Then we replace the remaining placeholders with `${yourTextHere}` so ServiceNow properly can evaluate those things.

## Getting Started

You first need to [fork this repo](https://github.com/MBahrSNC/mega/fork).

Then install that forked repo on your instance.

Your all set unless you want to...

<details>
  <summary>Own your lambda</summary>
    Then you'll need to fork or recreate https://github.com/jacebenson/netlify-mjml/ and host it on aws, netlify, vercel etc.
  </details>
<details>
  <summary>Use the officail mjml.io API</summary>
  <ol>
    <li>Go get the required [API key for MJML](https://mjml.io/api)</li>
    <li>After it's installed set the property `x_298439_mjml.api.key` value to applicationID:publicKey.  So if your applicationID was `checkers1` and your publicKey was `chess2` your value would be `checkers1:chess2`.  You do not need your secret key for this application.</li>
    <li>Lookup how to allow either a bad cert, as this api had that problem.</li>
  </ol>
</details>

## Using MJML Templates

MJML Templates in ServiceNow are entirely an opt-in.
It's a two step process.  
1. Update the target notification to have only this mail script, `${mail_script:mjml_notification_script}`.  (I'd remove the email template while you're setting this.)
2. Create or Update a MJML Template and reference the proper notification.

To Test this go back to the notification record and preview the email.  It should look great.
