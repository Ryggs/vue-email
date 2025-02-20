---
title: Postmark
description: Learn how to send an email using Vue Email and the Postmark Node.js SDK.
links:
  - label: NPM
    icon: i-simple-icons-npm
    to: https://www.npmjs.com/package/postmark
---

## 1. Install dependencies

Get the [Postmark Node.js SDK](https://www.npmjs.com/package/postmark).

::code-group
```sh [pnpm]
pnpm add postmark
```
```sh [yarn]
yarn add postmark
```
```sh [npm]
npm install postmark
```
::

## 2. Create an email using Vue

Start by building your email template in a `.vue` file.


```vue
// `/emails/welcome.vue`
<template>
  <e-html lang="en">
    <e-button :href="url">
      View on GitHub
    </e-button>
  </e-html>
</template>
<script lang="ts" setup>
defineProps<{ url: string }>();
</script>
```

## Step 3: Convert to HTML and send email

Import the email template you just built, convert into a HTML string, and use the Nodemailer SDK to send it.

::code-group

```ts [Nuxt 3]
// server/api/send-email.post.ts
import { useCompiler } from '#vue-email'
import postmark from 'postmark';

const client = new postmark.ServerClient(process.env.POSTMARK_API_KEY);

export default defineEventHandler(async (event) => {
  const template = await useCompiler('welcome.vue', {
    url: 'https://vuemail.net/',
  })

  const options = {
    From: 'you@example.com',
    To: 'user@gmail.com',
    Subject: 'hello world',
    HtmlBody: template,
  };

  await client.sendEmail(options);
  return { message: 'Email sent' };
});
```

```ts [NodeJs]
import express from 'express';
import postmark from 'postmark';
import { config } from "vue-email/compiler";

const client = new postmark.ServerClient(process.env.POSTMARK_API_KEY);

const app = express();
const vueEmail = config("./emails");

app.use(express.json());

app.post('/api/send-email', async (req, res) => {
  const template = await vueEmail.render("welcome.vue", {
      props: {
        url: 'https://vuemail.net/',
      },
    });

  const options = {
    From: 'you@example.com',
    To: 'user@gmail.com',
    Subject: 'hello world',
    HtmlBody: template,
  };

  await client.sendEmail(options);

  return res.json({ message: "Email sent" });
});

app.listen(3001);
```

::