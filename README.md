
![Rootstock](https://chainwire.org/wp-content/uploads/2024/03/Untitled_design_1_1710936225T83eQRnZET.jpg)
# Interact with Smart Contracts using Wagmi

## Introduction
Welcome! This guide will introduce you to developing on Rootstock using the Wagmi library. You'll learn how to interact with smart contracts effectively and leverage the features of Wagmi for seamless blockchain development.

## Table of Contents
1. [Developing on Rootstock](#developing-on-rootstock)
2. [What is Wagmi and Why Use It?](#what-is-wagmi-and-why-use-it)
3. [Wagmi Main Features](#wagmi-main-features)
4. [Wagmi & RainbowKit Synergy](#wagmi--rainbowkit-synergy)
5. [Prerequisites](#prerequisites)
6. [Sample Implementation](#sample-implementation)
7. [Recap and Recommendations](#recap-and-recommendations)

## Developing on Rootstock
Rootstock (RSK) is a smart contract platform secured by the Bitcoin network. It enables the creation of decentralized applications (dApps) with the security and stability of Bitcoin. Developing on Rootstock allows you to leverage its compatibility with the Ethereum Virtual Machine (EVM) and benefit from Bitcoin's robust security model.

### Benefits of Rootstock:
- **Security**: Secured by Bitcoin's hashrate.
- **Compatibility**: Fully compatible with Ethereum, enabling the use of existing Ethereum tools and libraries.
- **Decentralization**: Supports decentralized applications and smart contracts, enhancing the Bitcoin ecosystem.

## What is Wagmi and Why Use It?
**[Wagmi](https://wagmi.sh/)** is a React library designed for Ethereum-based chains, including Rootstock. Here are some of its benefits:

- **Developer Experience**: Simplifies blockchain interactions, reducing boilerplate code and enhancing productivity.
- **Performance**: Optimized for fast and efficient blockchain operations.
- **Feature Coverage**: Comprehensive hooks covering various blockchain functionalities.
- **Stability**: Well-maintained and reliable for production use.

## Wagmi Main Features
1. **Wallet Management**:
   - Easily connect and manage various wallets like MetaMask and WalletConnect.
2. **Custom Hooks**:
   - Provides hooks for blockchain interactions, such as reading from and writing to smart contracts.
3. **TypeScript Ready**:
   - Fully supports TypeScript, ensuring type safety and better developer experience.
4. **Simplified Interaction**:
   - Abstracts complexities of blockchain operations, making it accessible for developers of all levels.

## Wagmi & RainbowKit Synergy
**[RainbowKit](https://www.rainbowkit.com/)** is a React library that simplifies adding wallet connections to your dApp and uses Wagmi under the hood.

- **Easy Integration**: Quickly add wallet connection functionality to your application.
- **User-Friendly**: Provides a seamless and polished user experience for connecting wallets.

## Prerequisites
To get the most out of this guide, you should have basic knowledge of:
- TypeScript
- Node.js
- React
- Blockchain concepts
- npm/yarn/pnpm
- Git

## Sample Implementation
You can scaffold a new Wagmi + RainbowKit + Next.js project by doing:
```bash
yarn create @rainbow-me/rainbowkit
```
or follow this example of how to set up and use Wagmi & Rainbowkit in your react project. In this example is a Vite + React project and the repo is available [here](https://github.com/rsksmart/rsk-wagmi-starter-kit).

### Step 1: Install Dependencies
```bash
yarn add @rainbow-me/rainbowkit wagmi viem@2.x @tanstack/react-query
```
### Step 2: Wrap application in providers
This is the main React file:
```ts
// main.tsx
import ReactDOM from "react-dom/client";
import App from "./App";
import Providers from "./config/providers";
import "./polyfills";
import "@rainbow-me/rainbowkit/styles.css";
import "./index.css";

ReactDOM.createRoot(document.getElementById("root")!).render(
  <React.StrictMode>
    <Providers>
      <App />
    </Providers>
  </React.StrictMode>
);
```
This is the *Providers* component:
```ts
// Providers.tsx
import { WagmiProvider } from "wagmi";
import { RainbowKitProvider, darkTheme } from "@rainbow-me/rainbowkit";
import { QueryClientProvider } from "@tanstack/react-query";
import { QueryClient } from "@tanstack/react-query";
import { rainbowkitConfig } from "./rainbowkitConfig";

const queryClient = new QueryClient();

const Providers: React.FC<{ children: React.ReactNode }> = ({ children }) => (
  <WagmiProvider config={rainbowkitConfig}>
    <QueryClientProvider client={queryClient}>
      <RainbowKitProvider
        theme={darkTheme({
          accentColor: "#f28a0e",
          accentColorForeground: "black",
        })}
      >
        {children}
      </RainbowKitProvider>
    </QueryClientProvider>
  </WagmiProvider>
);

export default Providers;
``` 
And the Rainbowkit configuration file:
```ts
// rainbowkitConfig.ts
import { rsktestnet } from "@/lib/utils/RootstockTestnet";
import { getDefaultConfig } from "@rainbow-me/rainbowkit";
import { rootstock } from "wagmi/chains";

export const rainbowkitConfig = getDefaultConfig({
  appName: "Rootstock Rainbowkit",
  projectId: import.meta.env.VITE_WC_PROJECT_ID,
  chains: [rootstock, rsktestnet],
});
```
For now, you will have to configure the RSK testnet network manually while the integration with [viem](https://viem.sh/) is ready so that you can access the RSK testnet from `wagmi/chains`directly:
```ts
// rootstockTestnet.ts
import { defineChain } from "viem";

export const rsktestnet = defineChain({
  id: 31,
  name: "Rootstock Testnet",
  nativeCurrency: {
    decimals: 18,
    name: "Rootstock Smart Bitcoin",
    symbol: "tRBTC",
  },
  rpcUrls: {
    default: {
      http: ["https://public-node.testnet.rsk.co"],
    },
  },
  blockExplorers: {
    default: { name: "Explorer", url: "https://explorer.testnet.rsk.co" },
  },
  contracts: {
    multicall3: {
      address: "0xca11bde05977b3631167028862be2a173976ca11",
      blockCreated: 2771150,
    },
  },
});
```
Don't forget to add your api key as an environment variable, here called `VITE_WC_PROJECT_ID`, to make sure RainbowKit works fine with [Wallet Connect](https://walletconnect.com/). You can get your api key [here](https://cloud.walletconnect.com/app/account).

### Step 3: Use the hooks & ConnectButton component
The three most important hooks from Wagmi and RainbowKit for this guide are: **useAccount**, **useReadContract** and **useWriteContract**. Also, the main reason we added RainbowKit to the project is due to the **ConnectButton** component. Here is an explanation and sample code of every one of them.

#### ConnectButton
The `ConnectButton` component from RainbowKit provides a user-friendly interface for connecting wallets to your dApp. It handles the wallet connection process and displays the connection status. Here's the usage example:
```ts
import { ConnectButton } from "@rainbow-me/rainbowkit";

export default function Component(): JSX.Element {
  return (
      <ConnectButton />
  );
}
```
Though RainbowKit's component has some default styles, you can customize as most as you want of it. In this example we customized some details for responsive design, but you can change almost everything of it: 
```ts
import { ConnectButton } from "@rainbow-me/rainbowkit";

export default function Component(): JSX.Element {
  return (
      <ConnectButton
        showBalance={false}
        chainStatus={{ smallScreen: "none", largeScreen: "icon" }}
      />
  );
}
```
You can see more about customizing **ConnectButton** component in the [documentation](https://www.rainbowkit.com/docs/custom-connect-button).
 
#### useAccount

This hook lets you get the connected account's information, such as the address and connection status. 

```ts
import { useAccount } from 'wagmi';

function AccountInfo() {
  const { address, isConnected } = useAccount();

  return (
	  {
		  isConnected 
			? 
			  <div>
			    Connected to {address}
			  </div> 
			 : 
			   <div>Not connected</div>
	   }	
  );
}
```

#### useReadContract
The `useReadContract` hook allows you to read data from a smart contract. It is particularly useful for fetching on-chain data, such as token balances, contract states, and more.

```ts
import { useAccount, useReadContract } from "wagmi";

const ERC20_ADDRESS = "your_erc20_address";
const abi = [
  /*Your abi here*/
];

export default function Balance(): JSX.Element {
  const { address } = useAccount();

  const { data, isLoading, isError, refetch } = useReadContract({
    abi,
    address: ERC20_ADDRESS,
    functionName: "balanceOf",
    args: [address!],
  });

  return (
    <p>
      Your Balance:{" "}
      {isLoading ? "Loading..." : Number(data).toLocaleString("en-US")}
    </p>
  );
}
```

In the example above, we make use of `useReadContract` and `useAccount` hooks for calling the function `balanceOf` of an ERC-20 contract. The most important thing in this example is to identify the parameters of `useReadContract` hook. It is an object with these keys:

-  **abi:** contract abi.
- **address**: deployed contract address.
- **functionName**: the name of the function you want to call.
- **args**: the arguments you want to pass as parameters to the function. Note that it is an array and the arguments must be in the order you declared in the contract. 

And the hook returns:

- **data**: what the function returns.
- **isLoading**: loading state of the function call.
- **isError**: error state while the function call.
- **refetch**: you may do `refetch()` anywhere in the component in order to call the function again.

#### useWriteContract
The `useWriteContract` hook allows you to write data to a smart contract. It is particularly useful for performing actions such as transferring tokens, executing contract functions that change state, and more.

In the following example we're going to show you a mix of the previous hooks with `useWriteContract` hook so you can see how you can add them to your projects. With this integration, you'll also see how you can make use of the `refetch` function from `useReadContract` hook:

```ts
import { rainbowkitConfig } from "@/config/rainbowkitConfig";
import { useState } from "react";
import { useAccount, useReadContract, useWriteContract } from "wagmi";
import { waitForTransactionReceipt } from "wagmi/actions";

const ERC20_ADDRESS = "your_erc20_address";
const abi = [
  /*Your abi here*/
];

export default function Faucet(): JSX.Element {
  const [loading, setLoading] = useState<boolean>(false);
  const { address } = useAccount();

  const { data, isLoading, isError, refetch } = useReadContract({
    abi,
    address: ERC20_ADDRESS,
    functionName: "balanceOf",
    args: [address],
  });

  const { writeContractAsync } = useWriteContract();

  const mintTokens = async () => {
    setLoading(true);
    const txHash = await writeContractAsync({
      abi,
      address: ERC20_ADDRESS,
      functionName: "mint",
      args: [address, 100],
    });

    await waitForTransactionReceipt(rainbowkitConfig, {
      confirmations: 1,
      hash: txHash,
    });
    
    setLoading(false);
    refetch();
  };

  return (
    <>
      <button onClick={mintTokens}>
        {loading ? "Loading..." : "Mint Tokens"}
      </button>
      <p>
        Your Balance:{" "}
        {isLoading ? "Loading..." : Number(data).toLocaleString("en-US")}
      </p>
    </>
  );
}
```

Here we make use of `useWriteContract` and `useAccount` hooks for calling the function `mint` of the same ERC-20 contract. As you can see, the hook itself expects no parameters  and returns a function called `writeContractAsync`. 

This async function is the one we're going to call very similar as we did with the `useReadContract` hook. It expects the same arguments: an object with **abi**, **address**, **functionName** and **args**.

On the other side, it returns the transaction hash. With that and the `waitForTransactionReceipt` function from `wagmi/actions` you can check the transaction state and that's very useful for improving **UX**. As you can see, it receives:

- **rainbowkitConfig**: the configuration we defined earlier in this guide.
- an object with:
-- **confirmations:** the number of confirmations you'd like to wait.
-- **hash**: the transaction hash you want to query.

If you want to create your own ERC-20 contract or others to try this guide out use this [RSK + Hardhat Starter Kit](https://github.com/rsksmart/rootstock-hardhat-starterkit).

## Recap and Recommendations
In this guide, we have covered the following key points:
- Developing on Rootstock and leveraging its compatibility with Ethereum tools.
- Understanding the benefits and main features of Wagmi.
- Integrating Wagmi with RainbowKit for enhanced wallet management.
- Implementing sample code to interact with smart contracts using Wagmi hooks and RainbowKit components.

### Recommendations
- **Make the Most of Hooks**: Leverage Wagmi's hooks to simplify your development process. Utilize hooks like `useAccount`, `useReadContract`, and `useWriteContract` to interact seamlessly with your smart contracts.
- **Read the Documentation**: Thoroughly review Wagmi's [documentation](https://wagmi.sh/docs) and RainbowKit's [documentation](https://www.rainbowkit.com/docs) for deeper insights and advanced features.
- **Join our Community**: Engage with our community on [Discord](http://discord.gg/rootstock) to ask questions, share knowledge, and stay updated with the latest developments. Also check the [DevPortal](https://dev.rootstock.io/) for the latest news on development tool releases and more.

Thank you for following along with this guide! We hope you found it helpful and informative. Feel free to reach out if you have any questions or need further assistance in your blockchain development journey on Rootstock.

Happy coding!
