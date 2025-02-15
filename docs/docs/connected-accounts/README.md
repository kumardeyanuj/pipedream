# Connected Accounts

<VideoPlayer title="Connecting accounts to Pipedream" url="https://www.youtube.com/embed/xmDD1wRWnp0" />

Pipedream allows you to connect accounts for various apps and services within our UI. Once you connect an account, you can link that account to any step of a workflow, and use the OAuth access tokens, API key, or other auth info to make API requests to the desired service.

[[toc]]

## Supported Apps

Pipedream supports [{{$site.themeConfig.PUBLIC_APPS}}+ apps](https://pipedream.com/apps), with more added every day.

If we don't support a service you need, please [request an app here](#requesting-a-new-app-or-service).

## Connecting accounts

### From an action

Prebuilt actions that connect to a specific service require you connect your account for that service before you run your workflow. Click the **Connect [APP]** button to get started.

Depending on the integration, this will either:

- Open the OAuth flow for the target service, prompting you to authorize Pipedream to access your account, or
- Open a modal asking for your API credentials for key-based services

If you've already connected an account for this app, you'll also see a list of existing accounts to select from.

### From the HTTP Request action

Craft a custom HTTP request in a workflow with a connected account _without code_.

In a new step, select the **Send any HTTP Request** to start a new HTTP Request action.

![Starting a new HTTP request action in a workflow](https://res.cloudinary.com/pipedreamin/image/upload/v1672947285/docs/CleanShot_2023-01-05_at_14.34.25_wi8rcc.png)

Then, within the new HTTP request, open the **Authorization Type** dropdown to select a **Select an app**:

![Opening the HTTP Request Authorization Type dropdown](https://res.cloudinary.com/pipedreamin/image/upload/v1673535917/docs/CleanShot_2023-01-12_at_10.05.02_vmttbf.png)

This will open a new prompt to select an app to connect with. Once you select an app, the HTTP request will be updated with the correct headers to authenticate with that app's API.

![Select an account](https://res.cloudinary.com/pipedreamin/image/upload/v1673536044/docs/CleanShot_2023-01-12_at_10.07.06_rejzyy.gif)

Once you connect the selected app account Pipedream will autmatically include your account's authentication keys in the request in the headers, as well as update the URL to match the selected service.

Now you can modify the request path, method, body or query params to perform an action on the endpoint with your authenticated account.

### From a code step

You can connect accounts to code steps by using an `app` prop. Refer to the [connecting apps in Node.js documentation](/code/nodejs/auth/).

For example, you can connect to Slack from Pipedream (via their OAuth integration), and use the access token Pipedream generates to authorize requests:

```javascript
import { WebClient } from '@slack/web-api';

// Sends a message to a Slack Channel
export default defineComponent({
  props: {
    slack: {
      type: 'app',
      app: 'slack'
    }
  },
  async run({ steps, $ }) {
    const web = new WebClient(this.slack.$auth.oauth_access_token)
    return await web.chat.postMessage({
      text: "Hello, world!",
      channel: "#general",
    })
  })
});
```

## Managing Connected Accounts

Visit your [Accounts Page](https://pipedream.com/accounts) to see a list of all your connected accounts.

On this page you can:

- Connect your account for any integrated app
- [View and manage access](#access-control) for your connected accounts
- Delete a connected account
- Reconnect an account
- Change the nickname associated with an account

You'll also see some data associated with these accounts:

- For many OAuth apps, we'll list the scopes for which you've granted Pipedream access
- The workflows that are using the account

### Connecting a new account

- To get started, click the "Connect an app" button at the top right of the [Accounts page](https://pipedream.com/accounts) and select the app you'd like to connect

### Reconnecting an account

If you encounter errors in a step that appear to be related to credentials or authorization, you can reconnect your account:

1. Visit [https://pipedream.com/accounts](https://pipedream.com/accounts)
2. Search for your account
3. Click on the *...* next to your account, on the right side of the page
4. Select the option to **Reconnect** your account

## Access Control

**New connected accounts are private by default** and can only be used by the person who added it.
::: warning Accounts connected before August 2023

Prior to this change, all connected accounts were accessible to all workspace members. You can now manage access to new and existing connected accounts.

:::


### Managing access
- Find the account on the Accounts page and click the 3 dots on the far right of the row
- Select "Manage Access"

![Selecting Manage Access](https://res.cloudinary.com/pipedreamin/image/upload/v1691617725/manage-access-button_bgnebz.png)

- You may be prompted to reconnect your account first to verify ownership of the account
- You can enable access to the entire workspace or individual members

![Managing Access for a Connected Account](https://res.cloudinary.com/pipedreamin/image/upload/v1691614603/manage-access-modal_crmx3f.gif)


### Collaborating with others

Even if a workspace member doesn't have access to a private connected account, you can still collaborate together on the same workflows.

Workspace members who don't have access to a connected account **can perform the following actions** on workflows:
- Reference step exports
- Inspect prop inputs, step logs, and errors
- Test any step, so they can effectively develop and debug workflows end to end

Workspace members who do **not** have access to a given connected account **cannot modify prop inputs or edit any code** with that account.

![Read only action](https://res.cloudinary.com/pipedreamin/image/upload/v1691622307/read-only-action_uvdh1p.png)

![Read only code step](https://res.cloudinary.com/pipedreamin/image/upload/v1691621275/read-only-code-step_ijqvjc.png)

<br>

To make changes to steps that are locked in read-only mode, you can:
- Ask the account owner to [grant access](#managing-access)
- Click "More Actions" and change the connected account to one that you have access to (note that this may remove some prop configurations)

### Explanation of access
Access to connected accounts is enforced at the step-level within workflows and is designed with security and control in mind. 

When you connect an account in Pipedream, you are the owner of that connected account, and you always have full access. You can:
- Manage access
- Delete
- Reconnect
- Add to any step or trigger

<br>

How workspace members can use connected accounts that are **private**:

<img src="https://res.cloudinary.com/pipedreamin/image/upload/v1691644328/private-auth_ecm7jx.png" width="100px" />

| Operations | Workspace Owner & Admin | Other Members |
| --  | :--: | :--: | :--: |
| View on [Accounts](https://pipedream.com/accounts) | :white_check_mark: | :x: |
| Add to a new trigger or step | :x: | :x: |
| Modify existing steps | :x: | :x: |
| Test exising steps | :white_check_mark: | :white_check_mark: |
| Manage access | :x: | :x: |
| Reconnect | :x: | :x: |
|  Delete  |  :white_check_mark: |  :x: |

<br>

How workspace members can use connected accounts that are **shared**:

<img src="https://res.cloudinary.com/pipedreamin/image/upload/v1691644328/shared-auth-workspace_usdwod.png" width="240px" />

| Operations | Workspace Owner & Admin | Other Members |
| --  | :--: | :--: | :--: |
| View on [Accounts](https://pipedream.com/accounts) | :white_check_mark: | :white_check_mark: |
| Add to a new trigger or step | :white_check_mark: | :white_check_mark: |
| Modify existing steps | :white_check_mark: | :white_check_mark: |
| Test exising steps | :white_check_mark: | :white_check_mark: |
| Manage access | :x: | :x: |
| Reconnect | :x: | :x: |
|  Delete  |  :white_check_mark: |  :x: |

### Frequently Asked Questions

#### What is the "Owner" column?
The owner column on the Accounts page indicates who in the workspace originally connected the account (that is the only person who has permissions to manage access).

#### Why is there no "Owner" for certain connected accounts?
Accounts that were connected before August 2023 don't have an owner associated with them, and are shared with the entire workspace. In order to manage access for any of those accounts, we'll first prompt you to reconnect.

#### How can I restrict access to a connected account that's already shared with the workspace?
See above for info on [managing access](#managing-access).

#### Can I still work with other people on a single workflow, even if I don't want them to have access to my connected account?
Yes! See the section on [collaborating with others](#collaborating-with-others).

## Types of Integrations

### OAuth

For services that support OAuth, Pipedream operates an OAuth application that mediates access to the service so you don't have to maintain your own app, store refresh and access tokens, and more.

When you connect an account, you'll see a new window open where you authorize the Pipedream application to access data in your account. Pipedream stores the OAuth refresh token tied to your authorization grant, automatically generating access tokens you can use to authorized requests to the service's API. You can [access these tokens in code steps](/code/nodejs/auth/).

### Key-based

We also support services that use API keys or other long-lived tokens to authorize requests.

For those services, you'll have to create your keys in the service itself, then add them to your connected accounts in Pipedream.

For example, if you add a new connected account for **Sendgrid**, you'll be asked to add your Sendgrid API key.

## Account Security

[See our security docs](/privacy-and-security/#third-party-oauth-grants-api-keys-and-environment-variables) for details on how Pipedream secures your connected accounts.

## Requesting a new app or service

Please request new apps by [visiting our Support page](https://pipedream.com/support), and selecting "App / Integration questions" from the dropdown toward the bottom of the page.

<Footer />
