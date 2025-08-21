# USI-PAY-WooCommerce-Plugin
WooCommerce payments with USDC &amp; USI on Base

USI PAY — WooCommerce payments with USDC & USI on Base

USI PAY is a production-ready WooCommerce gateway that lets customers pay with USDC, USI, and other ERC-20 tokens on Base (chainId 8453). It includes a seamless wallet checkout (ethers.js) and secure server-side on-chain verification that confirms payments via the transaction receipt and Transfer logs, then auto-completes WooCommerce orders after the required confirmations.

Highlights

Base network native: prompts wallet to switch/add Base Mainnet (8453) automatically.

Tokens out of the box: preloaded with USDC (Base) and USI (0x987603A52d8B966E10FBD29DcB1A574049E25B07). You can add more tokens.

Accurate token quoting: configurable pricing sources

GoldRush (Covalent) (default) — by token address on Base

CoinGecko (by Base contract)

Manual per-token USD (great for USDC = 1.0)

Secure settlement: cron verifier parses Transfer(address,address,uint256) logs to your merchant address and matches amounts with configurable slippage and confirmations.

Customer UX: Connect wallet → get quote → transfer() → place order. Thank You page links to Basescan.

WooCommerce-native: Orders are set On-Hold until on-chain confirmation, then move to Processing/Completed automatically.

Requirements

WordPress 5.8+ / WooCommerce 5.0+

PHP 7.4+ (compatible with PHP 8.x)

A Base-compatible wallet (MetaMask, Base Wallet, Trust Wallet, etc.)

Optional: GoldRush API key (recommended as default pricing source)

Installation

Download the latest release ZIP from the Releases page (or the link above).

WordPress Admin → Plugins → Add New → Upload Plugin → select the ZIP → Install → Activate.

WooCommerce → Settings → Payments → USI PAY → Enable.

Configuration

In WooCommerce → Settings → Payments → USI PAY:

Merchant Wallet (Base): your receive address.

Base RPC URL: defaults to https://mainnet.base.org (use your own Alchemy/Infura for scale).

Accepted Tokens (JSON): prefilled with USDC + USI. Example:

[
  {
    "symbol": "USDC",
    "name": "USD Coin (Base)",
    "address": "0x833589fCD6eDb6E08f4c7C32D4f71B54b268cB6B",
    "decimals": 6,
    "manual_usd": 1.0
  },
  {
    "symbol": "USI",
    "name": "USIC Token",
    "address": "0x987603A52d8B966E10FBD29DcB1A574049E25B07",
    "decimals": 18,
    "manual_usd": 0.01
  }
]


Pricing Mode:

GoldRush (default): enter your API key for live USD quoting by address.

CoinGecko: live USD quoting by Base contract (if listed).

Manual: set manual_usd per token in the JSON.

Slippage (bps): default 100 (1%).

Confirmations: default 3 blocks.

How Checkout Works

Customer chooses USI PAY at checkout and clicks Connect Wallet.

Plugin ensures wallet is on Base 8453, fetches a quote for the selected token.

Customer clicks Pay with Wallet → ERC-20 transfer() is sent to your merchant address.

After 1 block, the form is populated with the tx hash; customer clicks Place order.

Order is set On-Hold.

Background cron verifier checks the receipt:

Confirms success

Verifies a Transfer from customer to merchant address

Matches the amount (± slippage)

Waits the configured confirmations

Order transitions to Processing/Completed; Thank You page shows Basescan link.

Security & Reliability

Server-side verification uses your configured Base RPC (consider dedicated providers for production).

Amount-matching supports basis-point slippage tolerance.

Verifier does not rely on client-side claims; it independently inspects on-chain logs to your merchant address.

Development

Built with ethers.js on the frontend and standard WordPress/WooCommerce hooks on the backend.

PHP big-number handling includes BCMath support when available; falls back safely when not.

Code organized under:

includes/class-usipay-utils.php (settings, token pricing)

includes/class-usipay-rest.php (REST endpoints: quote, latest block)

includes/class-usipay-cron.php (on-chain verifier)

includes/class-wc-gateway-usi.php (Woo gateway)

assets/js/checkout.js, assets/css/checkout.css

Roadmap

Support for permit2 / gas-optimized transfers when available

Auto-detect USDC price from multiple sources with failover

Optional EIP-712 signed invoices and order binding

License

GPLv2 or later.
