The payOS library provides convenient access to the payOS API from applications written in server-side JavaScript.

## Documentation

See the [payOS API docs](https://payos.vn/docs/api/) for more infomation.

## Installation

Install the package with:

```bash
bun add @meewmeew/payos-bun
```

## Usage

### Initialize

You need to initialize the PayOS object with the Client ID, Api Key and Checksum Key of the payment channel you created.

```javascript
import PayOS from "@meewmeew/payos-bun";

const payos = new PayOS(
  "YOUR_PAYOS_CLIENT_ID",
  "YOUR_PAYOS_API_KEY",
  "YOUR_PAYOS_CHECKSUM_KEY"
);
```

### Methods included in the PayOS object

- **createPaymentLink**

Create a payment link for the order data

Syntax:

```javascript
await payos.createPaymentLink(requestData);
```

Parameter data type:

```javascript
{
  orderCode: number;
  amount: number;
  description: string;
  cancelUrl: string;
  returnUrl: string;
  signature?: string;
  items?: { name: string; quantity: number; price: number }[];
  buyerName?: string;
  buyerEmail?: string;
  buyerPhone?: string;
  buyerAddress?: string;
  expiredAt?: number;
}
```

Return data type:

```javascript
{
  bin: string;
  accountNumber: string;
  accountName: string;
  amount: number;
  description: string;
  orderCode: number;
  paymentLinkId: string;
  status: string;
  checkoutUrl: string;
  qrCode: string;
}
```

Example:

```javascript
const requestData = {
  orderCode: 234234,
  amount: 1000,
  description: "Thanh toan don hang",
  items: [
    {
      name: "Mì tôm hảo hảo ly",
      quantity: 1,
      price: 1000,
    },
  ],
  cancelUrl: "https://your-domain.com",
  returnUrl: "https://your-domain.com",
};
const paymentLinkData = await payos.createPaymentLink(requestData);
```

- **getPaymentLinkInfomation**

Get payment information of an order that has created a payment link.

Syntax:

```javascript
await payos.getPaymentLinkInfomation(id);
```

Parameters:

- `id`: Store order code (`orderCode`) or payOS payment link id (`paymentLinkId`). Type of `id` is string or number.

Return data type:

```javascript
{
  id: string;
  orderCode: number;
  amount: number;
  amountPaid: number;
  amountRemaining: number;
  status: string;
  createdAt: string;
  transactions: TransactionType[];
  cancellationReason: string | null;
  canceledAt: string | null;
}
```

Transaction type:

```javascript
{
  reference: string;
  amount: number;
  accountNumber: string;
  description: string;
  transactionDateTime: string;
  virtualAccountName: string | null;
  virtualAccountNumber: string | null;
  counterAccountBankId: string | null;
  counterAccountBankName: string | null;
  counterAccountName: string | null;
  counterAccountNumber: string | null;
}
```

Example:

```javascript
const paymentLinkInfo = await payos.getPaymentLinkInfomation(1234);
```

- **cancelPaymentLink**

Cancel the payment link of the order.

Syntax:

```javascript
await payos.cancelPaymentLink(orderCode, cancellationReason);
```

Parameters:

- `id`: Store order code (`orderCode`) or payOS payment link id (`paymentLinkId`). Type of `id` is string or number.

- `cancellationReason`: Reason for canceling payment link (optional).

Return data type:

```javascript
{
  id: string;
  orderCode: number;
  amount: number;
  amountPaid: number;
  amountRemaining: number;
  status: string;
  createdAt: string;
  transactions: TransactionType[];
  cancellationReason: string | null;
  canceledAt: string | null;
}
```

Example:

```javascript
const orderCode = 123;
const cancellationReason = "reason";

const cancelledPaymentLinkInfo = await payos.cancelPaymentLink(
  orderCode,
  cancellationReason
);

// If you want to cancel the payment link without reason:
const cancelledPaymentLinkInfo = await payos.cancelPaymentLink(orderCode);
```

- **confirmWebhook**

Validate the Webhook URL of a payment channel and add or update the Webhook URL for that Payment Channel if successful.

Syntax:

```javascript
await payos.confirmWebhook("https://your-webhook-url/");
```

- **verifyPaymentWebhookData**

Verify data received via webhook after payment.

Syntax:

```javascript
const webhookBody = req.body;
const paymentData = payos.verifyPaymentWebhookData(webhookBody);
```

Return data type:

```javascript
{
  orderCode: number;
  amount: number;
  description: string;
  accountNumber: string;
  reference: string;
  transactionDateTime: string;
  paymentLinkId: string;
  code: string;
  desc: string;
  counterAccountBankId?: string | null;
  counterAccountBankName?: string | null;
  counterAccountName?: string | null;
  counterAccountNumber?: string | null;
  virtualAccountName?: string | null;
  virtualAccountNumber?: string | null;
}
```
