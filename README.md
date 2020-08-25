# Writing your first dHedge bot

Documentation for dHedge is coming soon®, but we'd like to give everyone a chance to write their own integration already (if they can work with ABIs directly). So here it is: a quick start.

Any bot could do a direct read call to the pool smart contract. If bot's address has manager access, it can also perform trades/rebalances.

## Executing synth trades

The call to trade looks like this: `exchange(bytes32 sourceKey, bytes32 sourceAmount, bytes32 destinationKey)`.
You provide source currency, the amount and the destination currency. This will perform the trade at the rate provided by Synthetix platform on their next Oracle update.

All synthetic assets are also ERC20 compatible, here's the documentation for all the tokens: https://docs.synthetix.io/tokens/list/
Note that, sourceKey and destinationKey are byte32 representations of token names: "sUSD", "sETH", "iETH". So a call `exchange(sETH, 100400000000000000000, iETH)`
will look like this in hex:

`exchange(7345544800000000000000000000000000000000000000000000000000000000, 0000000000000000000000000000000000000000000000057154740ed9380000, 6945544800000000000000000000000000000000000000000000000000000000)`.

Here's the ABI for this function:

```
	{
		"constant": false,
		"inputs": [
			{
				"internalType": "bytes32",
				"name": "sourceKey",
				"type": "bytes32"
			},
			{
				"internalType": "uint256",
				"name": "sourceAmount",
				"type": "uint256"
			},
			{
				"internalType": "bytes32",
				"name": "destinationKey",
				"type": "bytes32"
			}
		],
		"name": "exchange",
		"outputs": [],
		"payable": false,
		"stateMutability": "nonpayable",
		"type": "function"
	}
```

Another call that the bot will most likely need is a current balance of the pool. Calling `getFundComposition()` will return three matching lists.
First list represents `assets` in the pool (sETH, sUSD, etc), second shows their corresponding `balances`, and last is current sUSD `rates` of the assets (only for the ones with non-zero balance). Here's the ABI:

```
	{
		"constant": true,
		"inputs": [],
		"name": "getFundComposition",
		"outputs": [
			{
				"internalType": "bytes32[]",
				"name": "",
				"type": "bytes32[]"
			},
			{
				"internalType": "uint256[]",
				"name": "",
				"type": "uint256[]"
			},
			{
				"internalType": "uint256[]",
				"name": "",
				"type": "uint256[]"
			}
		],
		"payable": false,
		"stateMutability": "view",
		"type": "function"
	}
```

## Getting synth rates

Your bot may need some live Synth prices to make decisions from.

For the Ropsten Testnet, you can use the following dHedge GraphQL API:

`https://api.dhedge.org/graphql`

```
{
  assetRates {
    id
    name
    rate
    isFrozen
  }
}
```

That's it for now. We'll be improving this documentation from now on and should shortly be able to share some example bots.

Happy Coding!

dHedge Team
