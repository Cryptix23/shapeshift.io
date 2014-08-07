FORMAT: 1A
HOST: http://shapeshift.io

# ShapeShift.io

For any of the api calls if there is a problem the error message will be returned as the following JSON:

        {
            error: "Error message"
        }

Many of the requests require a 'coin pair'.  Valid coin pairs are listed here (the list should grow as we add more):

- btc_ltc
- ltc_btc

# Group Exchange Rate
## Rate [/rate/{pair}]
### Retrieve Current Exchange Rate [GET]

Gets the current rate exchange offered by Shapeshift.  This is an estimate because the rate can occasionally change rapidly depending on the markets. The rate is also a 'use-able' rate not a direct market rate.  Meaning multiplying your input coin amount times the rate should give you a close approximation of what will be sent out. This rate does not include the transaction (miner) fee taken off every transaction.

+ Parameters
    + pair (required, string, `btc_ltc`) ... String `btc_ltc` of the coin pair used for the exchange.

+ Response 200 (application/json)

        {
            "pair" : "btc_ltc",
            "rate" : "70.1234"
        }

# Group Deposit Limit
## Deposit Limit [/limit/{pair}]
Gets the current deposit limit set by Shapeshift.  Amounts deposited over this limit will be returned to the sending address. This is an estimate because a sudden market swing could move the limit.

+ Parameters
    + pair (required, string, `btc_ltc`) ... String `btc_ltc` of the coin pair used for the exchange.

### Retrive Current Deposit Limit [GET]

+ Response 200 (application/json)

        {
            "pair" : "btc_ltc",
            "limit" : "1.2345"
        }

# Group Transactions
## Transactions Collection [/recenttx/{max}]
Get a list of the most recent transactions.

### Retrive Recent Transactions [GET]

+ Parameters
    + max (optional, number, `5`) ... Numberic `max` number of transactions to return. If `max` is not specified this will return 5 transactions and `max` must be a number between 1 and 50 (inclusive).

+ Response 200 (application/json)

        [
            {
            curIn : <currency input>,
            curOut: <currency output>,
            amount: <amount>,
            timestamp: <time stamp>     //in seconds
            },
            ...
        ]

## Status of deposit to address [/txStat/{address}]
This returns the status of the most recent deposit transaction to the address.

- No Deposits Received
- Received (we see a new deposit but have not finished processing it)
- Complete
- Failed

### Retrive status of deposit [GET]

+ Parameters
    + address (optional, string, `address`) ... String of deposit `address` to look up.

+ Response 200 (application/json)
Status: No Deposits Received

    + Body

            {
                status : "no_deposit|received|complete|failed",
                address: <address>,
                withdraw: <withdrawal address>,
                incomingCoin: <amount deposited>,
                incomingType: <coin type of deposit>,
                outgoingCoin: <amount sent to withdrawal address>,
                outgoingType: <coin type of withdrawal>,
                transaction: <transaction id of coin sent to withdrawal address>
            }


# Group Shift
## Shift Conduit [/shift]
This is the primary data input into ShapeShift.

### Request Shift [POST]

+ Request (application/json)

        {
            "withdrawal": "AAAAAAAAAAAAAAAAAAA", // the address for resulting coin to be sent to
            "pair": "btc_ltc" // what coins are being exchanged in the form <input coin>_<output coin>  ie btc_ltc
        }

+ Response 200 (application/json)

        {
            deposit:<Deposit Address>,
            depositType:<Deposit Type>,         //BTC
            withdrawal:<Withdrawal Address>,    // <-- will match address submitted in post
            withdrawalType:<Withdrawal Type>    //LTC
        }


# Group Receipt
## Email Receipt [/mail]
This call requests a receipt for a transaction.  The email address will be added to the conduit associated with that transaction as well. (Soon it will also send receipts to subsequent transactions on that conduit)

### Request email receipt [POST]

+ Request (application/json)

        {
            "email": "mail@example.com", // the address for receipt email to be sent to
            "txid":"123ABC" // the transaction id of the transaction TO the user (ie the txid for the withdrawal NOT the deposit) example data
        }

+ Response 200 (application/json)

        {
            "email": {
                "status": "success",
                "message": "Email receipt sent"
            }
        }
