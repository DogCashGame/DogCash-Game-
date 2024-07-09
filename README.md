// Configuraciones
const config = {
    dshPerTap: 1, // DSH ganado por toque
    dshPerFollow: 1, // DSH ganado por seguir en redes sociales
    dshPerReferral: 1, // DSH ganado por referencia
    predictionFee: 1, // DSH cobrado por participar en el juego de predicción
    minAmount: 1, // Mínimo de retiro y depósito
    predictionTimeLimit: 60000, // Tiempo límite de la predicción en milisegundos (1 minuto)
    tradingViewSymbol: 'BINANCE:BTCUSDT', // Símbolo de TradingView para el gráfico de Bitcoin
    socialMediaUrls: {
        telegram: 'https://t.me/tu_canal',
        twitter: 'https://twitter.com/tu_perfil',
        youtube: 'https://youtube.com/tu_canal',
        website: 'https://tu_pagina_web.com'
    }
};

let balance = 0;
let referralBalance = {}; // Para rastrear los saldos de los usuarios referidos
let predictionPool = 0;

// Integración con Web3
let web3;
let dshContract;
const dshAddress = '0x88ceAB0aB654032B3175592c26140C820700905c'; // Dirección del contrato DSH
const dshAbi = [/* ABI del contrato DSH */];
const predictionWallet = '0xYOUR_PREDICTION_WALLET_ADDRESS'; // Dirección de la billetera de predicción

async function initializeWeb3() {
    if (window.ethereum) {
        web3 = new Web3(window.ethereum);
        await window.ethereum.enable();
    } else {
        alert("Necesitas instalar Metamask para interactuar con esta aplicación.");
    }
    dshContract = new web3.eth.Contract(dshAbi, dshAddress);
    updateBalance();
}

async function getAccount() {
    const accounts = await web3.eth.getAccounts();
    return accounts[0];
}

async function getDSHBalance(address) {
    const balanceInWei = await dshContract.methods.balanceOf(address).call();
    return web3.utils.fromWei(balanceInWei, 'ether');
}

async function sendDSH(address, amount) {
    const account = await getAccount();
    const amountInWei = web3.utils.toWei(amount.toString(), 'ether');
    return dshContract.methods.transfer(address, amountInWei).send({ from: account });
}

async function verifyHuman() {
    return new Promise((resolve, reject) => {
        grecaptcha.ready(function() {
            grecaptcha.execute('RECAPTCHA_SITE_KEY', { action: 'submit' }).then(function(token

