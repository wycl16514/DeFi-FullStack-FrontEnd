From this section, we will see how to eanble frontend to interact with smart contracts. Normally a web3 application envolves three parties as following:


![DeFi-frontend](https://github.com/user-attachments/assets/4f323966-a64e-42b6-bc16-2928a03bd471)

Solidity developer is responsible for designing smart contract and deloy them to blockchain network. After deploying they will receive meta data which is used for interact with those deployed smart contract. Such meta data will
send to backend system for processing, such as keeping them in database, do some indexing for quick search or other related preprocessing, and frontend developer will desgin web application which is used by users. Users may 
using the frontend to send money or transfer moneny, then frontend will relay such requests to backend, backend will interact with smart contract according to requests from frontend by using those meta data and send the result
of calling smart contract to frontend, then frontend will show whether the transactions are success or not.

Since we are fullstack, we will hanlde all those jobs by ourself. In this section we will ignore the part of backend, we deploy smart contract to network, save the delpoyed meta data in local file, then frontend will try to 
interact with smart contract by using those meta data save at file. First we will see how to write the meta deta of smart contract deployment into file. There are two kinds of meta data we need to send to frontend, one is 
smart contract ABI, the other is the deployed address of the smart contract.

Since the ABI of the smart contract is saved at the compiled result file in the artifacts folder, and we can get the deployed address from the deploy script, then we can write these two kinds of info local file, which is 
located at the path of ./src/frontend/contracts, then we create a frontend/contracts folder at the src path first and change the deploy.js as following:

```js
function savedContractToFrontend(contract, name) {
    const contractsDir = __dirname + "/../src/frontend/contracts"
    //check the path exists or not, if not create it
    if (!fs.existsSync(contractsDir)) {
        fs.mkdirSync(contractsDir)
    }

    const contractArtifact = artifacts.readArtifactSync(name);

    //write address as json format
    fs.writeFileSync(
        contractsDir + `/${name}-address.json`,
        JSON.stringify({ address: contract.target},
            undefined, 2)
    )

    //write ABI info as json format
    fs.writeFileSync(contractsDir + `/${name}.json`,
        JSON.stringify(contractArtifact, null, 2))

}

async function main() {
    ....
    savedContractToFrontend(token, 'SimpleDeFiToken')
}
```

After having aboved changes, run "npx hardhat run script/deploy.js", then we will see at path ./src/frontend/contracts/, there are two files with name SimpleDefiToken-address.json and SimpleDefiToken.json, and they will save
related info for later usage.

When frontend want to interact with smart contract, it first need to get adress and abi of the smart contract, then it need to use wallet as medium to connect with smart contract. The architechture would like following:


![DeFi-frontend (1)](https://github.com/user-attachments/assets/31c2172b-fe50-4f53-8dbe-26d33a7c188d)

From aboved image we can see, the frontend application can't connect to blockchain network directly, all interaction with smart contract will deligate to wallet such as authentication, and wallets are not directly interact with
smart contract, they rely on connector to deligate their requests, different wallet may choose different connector, connector will deligate its request to provider and provider will rely on rpc endpoint to send messages to 
smart contract on the blockchain networks.

Why create such layers, that's because to meet all kinds of complicate business concers, we will put them here and go back to it at later time. Before we going to start our new project, we need to install packages by following
command:
```js
npm i @ethersproject/providers
npm i @web3-react/core@6.19
npm i @web3-react/injected-connector
```

Then in App.js, we change its code a little bit as following:
```js
import logo from './logo.svg';
import './App.css';
import { Web3ReactProvider } from '@web3-react/core'
import { getLibrary } from './components/wallet';
function App() {
  return (
    <Web3ReactProvider getLibrary={getLibrary}>
      <div className="App">
        <header className="App-header">
          <img src={logo} className="App-logo" alt="logo" />
          <p>
            Edit <code>src/App.js</code> and save to reload.
          </p>
          <a
            className="App-link"
            href="https://reactjs.org"
            target="_blank"
            rel="noopener noreferrer"
          >
            Learn React
          </a>
        </header>
      </div>
    </Web3ReactProvider >
  );
}

export default App;

```
Then run npm start, if you can see the page bring up successfully then our code configuration is ok. Now it is time to inject connector, in index.js we add following:
```js
import { InjectedConnector } from "@web3-react/injected-connector"

export const ETHEREUM_NETWORK_ID = 1
export const SEPOLIA_NETWORK_ID = 11155111;
export const LOCAL_NETWORK_ID = 31337

export const injectedConnector = new InjectedConnector({
    supportedChainIds: [
        ETHEREUM_NETWORK_ID,
        SEPOLIA_NETWORK_ID,
        LOCAL_NETWORK_ID,
    ]
})

...
```

Then start the hardhat local network, and open your metamask plugin, click the three points at the right top, click setting, click network, click add new network, in the popup page, click "add network by hand", in the following
page, fill in following setting:

![截屏2024-08-22 12 59 45](https://github.com/user-attachments/assets/064e2b0d-6478-40a1-a946-b7514f0bcd45)

Then click the up left corner button to switch current network to hardhat, and click button at the middle to add a new account, and select import account, go to the hardhat console, copy an account private key and past to it,
click ok then you will see the given account is import into metamask:

![截屏2024-08-22 13 02 43](https://github.com/user-attachments/assets/476106d6-902f-48e8-8383-52eabf3ddb4b)

All aboved steps process normally, we can go to the development stage.
