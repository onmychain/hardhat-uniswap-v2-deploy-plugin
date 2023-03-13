[![Build](https://github.com/onmychain/hardhat-uniswap-v2-deploy-plugin/actions/workflows/build.yml/badge.svg)](https://github.com/onmychain/hardhat-uniswap-v2-deploy-plugin/actions/workflows/build.yml)
[![Node.js Package](https://github.com/onmychain/hardhat-uniswap-v2-deploy-plugin/actions/workflows/publish.yml/badge.svg)](https://github.com/onmychain/hardhat-uniswap-v2-deploy-plugin/actions/workflows/npm-publish.yml)

# hardhat-uniswap-v2-deploy-plugin

Simple hardhat plugin that allows you to deploy UniswapV2 (Factory, Router, WETH9) to your network.

[Hardhat](https://hardhat.org) plugin. 

## What

This plugin extends your Hardhat Runtime Environment, allowing you to deploy in one command the full UniswapV2 stack (Factory, Router, WETH9). You can use it to test features such as pair creation, liquidity provisioning, and swaps.

Additionally, you can use the Interfaces (IUniswapV2Factory, IUniswapV2Router, and IUniswapV2Pair) to communicate directly with testnet / mainnet.

Feedback, improvment suggestions are welcome.

## Installation

Install the plugin.

```bash
npm install --save-dev @onmychain/hardhat-uniswap-v2-deploy-plugin
```

or 

```bash
yarn add --dev @onmychain/hardhat-uniswap-v2-deploy-plugin
```

Import the plugin in your `hardhat.config.js`:

```js
require("@onmychain/hardhat-uniswap-v2-deploy-plugin");
```

Or if you are using TypeScript, in your `hardhat.config.ts`:

```ts
import "@onmychain/hardhat-uniswap-v2-deploy-plugin";
```

## Environment extensions

The UniswapV2Deployer extension allows you to deploy the UniswapV2 stack on your network.

This plugin extends the Hardhat Runtime Environment by adding an `UniswapV2Deployer` field
whose type is `UniswapV2Deployer`.

## Usage

It's so easy.

```ts
import { ethers, UniswapV2Deployer } from "hardhat"

async function main() {
  [signer] = await this.hre.ethers.getSigners();
  const deployer = new UniswapV2Deployer();
  const { factory, router, weth9 } = await deployer.deploy(signer);

  // Now you can interact with the different UniswapV2 contracts
  // ...
}

// We recommend this pattern to be able to use async/await everywhere
// and properly handle errors.
main().catch((error) => {
  console.error(error);
  process.exitCode = 1;
});
```

There are no additional steps you need to take for this plugin to work.

## Detailed Usage Example

Please see [project.test.ts](test/project.test.ts) file. It shows you how to deploy and interact with the contracts i.e. adding liquidity, swapping tokens.

Below is a full example of the usage.

```typescript
const [signer] = await ethers.getSigners()
const { weth9, factory, router } = await UniswapV2Deployer.deploy(signer)
const Token = await ethers.getContractFactory("Token")
const token = await Token.deploy(eth(1000))
await token.deployed()

await weth9.approve(router.address, eth(1000))
await token.approve(router.address, eth(1000))

await router.addLiquidityETH(
    token.address,
    eth(1000),
    eth(1000),
    eth(100),
    signer.address,
    constants.MaxUint256,
    { value: eth(1000) }
)

await router.swapExactETHForTokens(
    0,
    [weth9.address, token.address],
    signer.address,
    constants.MaxUint256,
    { value: eth(1) }
);
```
