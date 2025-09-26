# Incremento-m-s-quema-anual-
Incrementa valor un 30% el 7 de cada mes y quema 2% anual 

pancakeSwap 

Vamos a desplegar tu ecosistema A²BNB como un verdadero protocolo DeFi! 🚀  
Te guiaré paso a paso por:

1. ✅ Script de Hardhat para desplegar en BSC Testnet y Mainnet  
2. 🖥️ Frontend básico para interactuar con el contrato  
3. 🔗 Registro en Chainlink Keepers para automatizar el rebase mensual  

---

🛠️ 1. Script de Hardhat para despliegue

📁 Estructura del proyecto

`
A2BNB/
├── contracts/
│   └── A2BNB.sol
├── scripts/
│   └── deploy.js
├── hardhat.config.js
├── .env
`

🔐 .env (variables sensibles)

`
PRIVATEKEY=tuclave_privada
BSCTESTNETRPC=https://data-seed-prebsc-1-s1.binance.org:8545/
BSCMAINNETRPC=https://bsc-dataseed.binance.org/
`

📦 hardhat.config.js

`js
require("@nomiclabs/hardhat-ethers");
require("dotenv").config();

module.exports = {
  solidity: "0.8.20",
  networks: {
    bscTestnet: {
      url: process.env.BSCTESTNETRPC,
      accounts: [process.env.PRIVATE_KEY],
    },
    bscMainnet: {
      url: process.env.BSCMAINNETRPC,
      accounts: [process.env.PRIVATE_KEY],
    },
  },
};
`

🚀 scripts/deploy.js

`js
const hre = require("hardhat");

async function main() {
  const routerAddress = hre.network.name === "bscTestnet"
    ? "0x9ac64cc6e4415144c455bd8e4837fea55603e5c3"
    : "0x10ED43C718714eb63d5aA57B78B54704E256024E";

  const A2BNB = await hre.ethers.getContractFactory("A2BNB");
  const contract = await A2BNB.deploy(routerAddress);

  await contract.deployed();
  console.log(A2BNB deployed to: ${contract.address});
}

main().catch((error) => {
  console.error(error);
  process.exitCode = 1;
});
`

---

🖥️ 2. Frontend básico (React + Ethers.js)

📁 Estructura

`
frontend/
├── src/
│   ├── App.js
│   ├── abi.json
│   └── index.js
`

🧠 App.js (interacción básica)

`jsx
import { useState } from "react";
import { ethers } from "ethers";
import abi from "./abi.json";

const CONTRACTADDRESS = "TUCONTRATO_DESPLEGADO";

function App() {
  const [account, setAccount] = useState("");
  const [price, setPrice] = useState("");

  const connectWallet = async () => {
    const [addr] = await window.ethereum.request({ method: "eth_requestAccounts" });
    setAccount(addr);
  };

  const getPriceETH = async () => {
    const provider = new ethers.providers.Web3Provider(window.ethereum);
    const contract = new ethers.Contract(CONTRACT_ADDRESS, abi, provider);
    const price = await contract.getPriceETH();
    setPrice(ethers.utils.formatUnits(price, 8));
  };

  return (
    <div>
      <h1>A²BNB Dashboard</h1>
      <button onClick={connectWallet}>Conectar Wallet</button>
      <button onClick={getPriceETH}>Ver Precio ETH/USD</button>
      <p>Precio ETH: ${price}</p>
    </div>
  );
}

export default App;
`

---

🔗 3. Registro en Chainlink Keepers

🧾 Requisitos

- El contrato debe implementar checkUpkeep y performUpkeep
- Debes tener fondos LINK en el contrato
- Registro se hace en Chainlink Automation UI

✅ Pasos para registrar

1. Ve a automation.chain.link
2. Conecta tu wallet en la red BSC Testnet o Mainnet
3. Haz clic en “Register new upkeep”
4. Ingresa:
   - Name: A²BNB Monthly Rebase
   - Contract address: Dirección del contrato desplegado
   - Gas limit: 500000
   - Check data: 0x (vacío si no usas parámetros)
   - Funding: Deposita LINK para cubrir ejecuciones

5. Una vez registrado, Chainlink ejecutará performUpkeep() automáticamente cada vez que checkUpkeep() retorne true (ej. el día 7 de cada mes).

---
