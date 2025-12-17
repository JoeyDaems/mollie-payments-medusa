# Mollie Payments for Medusa

A comprehensive payment provider plugin that enables [Mollie](https://www.mollie.com/gb/) payments on [Medusa](https://medusajs.com/) V2 projects.

## Table of Contents

- [Features](#features)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
  - [Configuration Options](#configuration-options)
  - [Environment Variables](#environment-variables)
- [Usage](#usage)
- [Client-Side Integration](#client-side-integration)
- [Supported Payment Methods](#supported-payment-methods)
- [Extending the Plugin](#extending-the-plugin)
- [Local Development and Customization](#local-development-and-customization)
- [License](#license)

## Features

- **Multiple Payment Methods**: Supports a wide range of Mollie payment methods including:

  - Mollie Hosted Checkout
  - iDEAL
  - Bancontact
  - Credit Card
  - PayPal
  - Apple Pay
  - Gift Card

- **Easily Extendable**: The modular architecture makes it easy to add support for additional Mollie payment methods.

- **Webhook Support**: Full support for Mollie webhooks for real-time payment status updates.

- **Automatic Capture**: Configurable automatic capture of payments.

> [!WARNING] > _This plugin has not been tested on a live store. Please conduct thorough testing before using it in a production environment. I am not responsible for any missed or failed payments resulting from the use of this plugin. If you encounter any issues, please report them [here](https://github.com/VariableVic/mollie-payments-medusa/issues)._

## Prerequisites

- Medusa server v2.3.0 or later
- Node.js v20 or later
- A [Mollie](https://www.mollie.com/gb/) account and API key with payment methods enabled.

> [!NOTE] > _You can get an API key from your Mollie dashboard: click Browse > Developers > API keys_

## Installation

```bash
pnpm add @variablevic/mollie-payments-medusa
```

## Configuration

Add the provider to the `@medusajs/payment` module in your `medusa-config.ts` file:

```typescript
modules: [
  // ... other modules
  {
    resolve: "@medusajs/payment",
    options: {
      providers: [
        // ... other providers
        {
          resolve: "@variablevic/mollie-payments-medusa/providers/mollie",
          id: "mollie",
          options: {
            apiKey: process.env.MOLLIE_API_KEY,
            redirectUrl: process.env.MOLLIE_REDIRECT_URL,
            medusaUrl: process.env.MEDUSA_URL,
          },
        },
      ],
    },
  },
];
```

## Configuration Options

| Option        | Description                                  | Default                            |
| ------------- | -------------------------------------------- | ---------------------------------- |
| `apiKey`      | Your Mollie API key                          | Required                           |
| `redirectUrl` | The URL to redirect to after payment         | Required                           |
| `medusaUrl`   | The URL of your Medusa server                | Required                           |
| `autoCapture` | Whether to automatically capture payments    | `true`                             |
| `description` | The description that appears on the payment. | `Mollie payment created by Medusa` |
| `debug`       | Whether to enable debug mode                 | `false`                            |

## Environment Variables

Create or update your `.env` file with the following variables:

```bash
MOLLIE_API_KEY=your_mollie_api_key
MOLLIE_REDIRECT_URL=https://your-store.com/checkout/payment
MEDUSA_URL=https://your-medusa-server.com
```

## Usage

Once installed and configured, the Mollie payment methods will be available in your Medusa admin. To enable them, log in to you Medusa Admin, browse to Settings > Regions, add or edit a region and select the desired Mollie providers from the dropdown.

![Screenshot 2025-03-10 at 14 14 43](https://github.com/user-attachments/assets/6aad3edb-7370-4aa8-9bc1-1cf35572d2e0)

Make sure that the selected payment methods are enabled in your Mollie origanization settings as well.

### Client-Side Integration

To integrate with your storefront, you'll need to implement the payment flow according to Mollie's and Medusa's documentation. Here's a basic example:

1. Create a payment session in your checkout flow
2. Redirect the customer to the Mollie payment page
3. Handle the webhook notifications to update the payment status

_Example integration using the [Medusa Next.js Starter](https://github.com/medusajs/nextjs-starter-medusa):_

https://github.com/user-attachments/assets/742ee261-5e41-4e33-9a72-faf1a424fc52

### Supported Payment Methods

The plugin currently supports the following Mollie payment methods:

| Payment Method  | Provider ID                        |
| --------------- | ---------------------------------- |
| Hosted Checkout | `pp_mollie-hosted-checkout_mollie` |
| iDEAL           | `pp_mollie-ideal_mollie`           |
| Credit Card     | `pp_mollie-card_mollie`            |
| Bancontact      | `pp_mollie-bancontact_mollie`      |
| Gift Card       | `pp_mollie-giftcard_mollie`        |
| PayPal          | `pp_mollie-paypal_mollie`          |
| Apple Pay       | `pp_mollie-apple-pay_mollie`       |

## Extending the Plugin

To add support for additional Mollie payment methods, create a new service in `src/providers/mollie/services` that extends the `MollieBase` class:

```typescript
import { PaymentMethod } from "@mollie/api-client";
import MollieBase from "../core/mollie-base";
import { PaymentOptions, PaymentProviderKeys } from "../types";

class MollieNewMethodService extends MollieBase {
  static identifier = "mollie-new-method";

  get paymentCreateOptions(): PaymentOptions {
    return {
      method: PaymentMethod.newMethod,
    };
  }
}

export default MollieNewMethodService;
```

Make sure to replace `new method` with the actual Mollie payment method ID.

Export your new service from `src/providers/mollie/services/index.ts`. Then add your new service to the list of services in `src/providers/mollie/index.ts`.

## Local development and customization

In case you want to customize and test the plugin locally, refer to the [Medusa Plugin docs](https://docs.medusajs.com/learn/fundamentals/plugins/create#3-publish-plugin-locally-for-development-and-testing).

## License

TBD
