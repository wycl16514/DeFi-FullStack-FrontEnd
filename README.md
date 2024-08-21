From this section, we will see how to eanble frontend to interact with smart contracts. Normally a web3 application envolves three parties as following:


![DeFi-frontend](https://github.com/user-attachments/assets/4f323966-a64e-42b6-bc16-2928a03bd471)

Solidity developer is responsible for designing smart contract and deloy them to blockchain network. After deploying they will receive meta data which is used for interact with those deployed smart contract. Such meta data will
send to backend system for processing, such as keeping them in database, do some indexing for quick search or other related preprocessing, and frontend developer will desgin web application which is used by users. Users may 
using the frontend to send money or transfer moneny, then frontend will relay such requests to backend, backend will interact with smart contract according to requests from frontend by using those meta data and send the result
of calling smart contract to frontend, then frontend will show whether the transactions are success or not.

Since we are fullstack, we will hanlde all those jobs by ourself. In this section we will ignore the part of backend, we deploy smart contract to network, save the delpoyed meta data in local file, then frontend will try to 
interact with smart contract by using those meta data save at file. First we will see how to write the meta deta of smart contract deployment into file.
