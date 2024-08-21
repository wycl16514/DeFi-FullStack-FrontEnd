From this section, we will see how to eanble frontend to interact with smart contracts. Normally a web3 application envolves three parties as following:


![DeFi-frontend](https://github.com/user-attachments/assets/4f323966-a64e-42b6-bc16-2928a03bd471)

Solidity developer is responsible for designing smart contract and deloy them to blockchain network. After deploying they will receive meta data which is used for interact with those deployed smart contract. Such meta data will
send to backend system for processing, such as keeping them in database, do some indexing for quick search or other related preprocessing, and frontend developer will desgin web application which is used by users. Users may 
using the frontend to send money or transfer moneny, then frontend will relay such requests to backend, backend will interact with smart contract according to requests from frontend by using those meta data and send the result
of calling smart contract to frontend, then frontend will show whether the transactions are success or not.

Since we are fullstack, we will hanlde all those jobs by ourself. In this section we will ignore the part of backend, we deploy smart contract to network, save the delpoyed meta data in local file, then frontend will try to 
interact with smart contract by using those meta data save at file. First we will see how to write the meta deta of smart contract deployment into file. There are two kinds of meta data we need to send to frontend, one is 
smart contract ABI, the other is the deployed address of the smart contract.

Since the ABI of the smart contract is saved at the compiled result file in the artifacts folder, and we can get the deployed address from the deploy script, then we can write these two kinds of info local file, which is located
at the path of ./src/frontend/contracts, then we create a frontend/contracts folder at the src path first and change the deploy.js as following:

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

After having aboved changes, run "npx hardhat run script/deploy.js", then we will see at path ./src/frontend/contracts/, there are two files with name SimpleDefiToken-address.json and SimpleDefiToken.json,
