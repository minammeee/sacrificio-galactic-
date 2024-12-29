<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Página de Sacrificio en Galactic</title>
    <script src="https://cdn.jsdelivr.net/npm/web3@1.6.1/dist/web3.min.js"></script>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f4f4;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
        }
        .container {
            text-align: center;
            background-color: white;
            padding: 2rem;
            border-radius: 8px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        }
        button {
            background-color: #4CAF50;
            color: white;
            padding: 10px 20px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }
        button:hover {
            background-color: #45a049;
        }
        input {
            padding: 10px;
            width: 200px;
            margin: 10px 0;
            border-radius: 5px;
            border: 1px solid #ddd;
        }
    </style>
</head>
<body>

<div class="container">
    <h1>Sacrificio en Galactic</h1>
    <p>Realiza tu sacrificio con la moneda Galactic (ERC-20).</p>
    <input type="text" id="sacrificioAmount" placeholder="Cantidad a sacrificar (en Galactic)">
    <br>
    <button id="connectWalletButton">Conectar Billetera</button>
    <button id="sacrificarButton" disabled>Realizar Sacrificio</button>
    <p id="statusMessage"></p>
</div>

<script>
    let web3;
    let userAccount;
    let galacticContractAddress = "0xYourGalacticTokenAddress";  // Dirección del contrato de Galactic
    let galacticContractABI = [
        // ABI del contrato de Galactic (el ABI del token ERC-20)
        {
            "constant": true,
            "inputs": [
                {
                    "name": "account",
                    "type": "address"
                }
            ],
            "name": "balanceOf",
            "outputs": [
                {
                    "name": "",
                    "type": "uint256"
                }
            ],
            "payable": false,
            "stateMutability": "view",
            "type": "function"
        },
        {
            "constant": false,
            "inputs": [
                {
                    "name": "recipient",
                    "type": "address"
                },
                {
                    "name": "amount",
                    "type": "uint256"
                }
            ],
            "name": "transfer",
            "outputs": [
                {
                    "name": "",
                    "type": "bool"
                }
            ],
            "payable": false,
            "stateMutability": "nonpayable",
            "type": "function"
        }
    ];

    // Detectar billetera (MetaMask)
    window.onload = async () => {
        if (typeof window.ethereum !== 'undefined') {
            web3 = new Web3(window.ethereum);
            document.getElementById('connectWalletButton').addEventListener('click', connectWallet);
        } else {
            alert('Por favor, instala MetaMask o usa una billetera compatible.');
        }
    };

    // Conectar billetera
    async function connectWallet() {
        try {
            const accounts = await window.ethereum.request({ method: 'eth_requestAccounts' });
            userAccount = accounts[0];
            document.getElementById('statusMessage').textContent = "Billetera conectada: " + userAccount;
            document.getElementById('sacrificarButton').disabled = false;
        } catch (error) {
            console.error("Error al conectar la billetera:", error);
        }
    }

    // Realizar sacrificio
    document.getElementById('sacrificarButton').addEventListener('click', async () => {
        const amount = document.getElementById('sacrificioAmount').value;
        if (!amount || isNaN(amount) || amount <= 0) {
            alert("Por favor, ingresa una cantidad válida.");
            return;
        }

        try {
            const contract = new web3.eth.Contract(galacticContractABI, galacticContractAddress);
            const amountInWei = web3.utils.toWei(amount, 'ether'); // Convertir la cantidad a la unidad mínima

            // Llamar al método 'transfer' del contrato Galactic
            const tx = await contract.methods.transfer('0xSacrificeAddress', amountInWei).send({
                from: userAccount
            });

            document.getElementById('statusMessage').textContent = "Sacrificio realizado con éxito!";
            console.log("Transacción exitosa:", tx);
        } catch (error) {
            console.error("Error al realizar el sacrificio:", error);
            document.getElementById('statusMessage').textContent = "Hubo un error, intenta de nuevo.";
        }
    });
</script>

</body>
</html>
