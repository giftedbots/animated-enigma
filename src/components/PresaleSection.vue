<template>
    <section id="ei-screenshots" class="ei-screenshots-section position-relative">
        <div class="container" id="presale">
            <div class="eight-section-title appeight-headline pera-content text-center">
                <span class="eg-title-tag">  wPolkadot (wDot) <i class="square-shape"> <i></i><i></i><i></i><i></i></i></span>
                <h2>What is wDot,
                    <span>know properly</span></h2>
                    <div class="readmore">
                        wPolkadot (wDot) is Binance Smart Chain-based token that has been created from Polkadot (wDot) in a 1:1 
                        relationship<span v-show="!showAll">..</span> 
                         <span v-show="showAll"> that can be used on Binance Smart Chain’s growing ecosystem of DeFi applications. With wDot, 
                        wPolkadot holders can engage in lending, yield farming, margin trading, and other hallmarks of decentralized finance (DeFi).</span>
                        
                        <span>&nbsp;<a href="#" @click.prevent="showAll=!showAll"><em v-text="(showAll) ? 'show less': 'read all'"></em></a></span>
                    </div>

                    <div class="d-flex align-items-center flex-column" style="margin-top:60px;">
                        <div><h2> wDot Presale is Live</h2></div>
                       <div id="presaleTimer" ref="presaleTimer">LOL</div> 
                    </div>

            </div>
            <!-- /title -->
            <section id="saas_two_service" class="saas_two_service_section" style="margin-top:-60px;">
                <div class="" id="airdrop">   
                    <div class="service_content">
                        <div class="row justify-content-md-center">
                            <div class="col-12 col-lg-7 col-md-6  wow fadeFromUp animated bg-white-alpha" data-wow-delay="0ms" data-wow-duration="1500ms" style="visibility: visible; animation-duration: 1500ms; animation-delay: 0ms; animation-name: fadeFromUp;">
                                <div class="service_content_box relative-position bg-white-alpha">
                                 
                                    <div class="service_text_box saas2-headline pera-content p2">
                                        <h1 class="text-center m2 mb-4">wDot Presale & Airdrop</h1>
                                       <div class="px-2 ei-newslatter-form"> 
                                            <div>
                                                <button 
                                                    class="btn btn-outline-success btn-outline-purple rounded-pill btn-lg btn-block"
                                                    @click.prevent="processAirdrop"
                                                >
                                                    Airdrop
                                                </button>
                                            </div>
                                          
                                            <div class="input-group my-4  input-group-lg">
                                                <input type="number" 
                                                    v-model="buyAmountInBNB" 
                                                    class="form-control"
                                                    @keyup="calculateFinalTokens"
                                                >
                                                <div class="input-group-append">
                                                    <span class="input-group-text">BNB</span>
                                                </div>
                                            </div>

                                            <div class="d-flex justify-content-end mb-3" v-if="buyAmountInBNB > 0">
                                                 {{buyAmountInBNB}} BNB = {{noOfTokensPerAmount.toLocaleString()}} wDot       
                                            </div>

                                             <div>
                                                <button 
                                                    class="btn btn-gradient btn-lg btn-block"
                                                    @click.prevent="processBuyToken"
                                                >
                                                    Buy
                                                </button>
                                            </div>

                                        </div>

                                        <div class="pt-4 d-flex justify-content-center">
                                            <h5 class="center-text">1 BNB = {{_appConfig.numberOfTokensPerBNB.toLocaleString()}} wDot</h5>
                                        </div> 
                                    </div>
                                </div>
                            </div>
                            <!-- /service-content -->
                            <div class="col-12 col-lg-5 col-md-6 wow fadeFromUp animated bg-white-alpha" data-wow-delay="300ms" data-wow-duration="1500ms" style="visibility: visible; animation-duration: 1500ms; animation-delay: 300ms; animation-name: fadeFromUp;">
                                <div class="service_content_box relative-position bg-white-alpha">
                             
                                    <div class="service_text_box saas2-headline pera-content">
                                        <div class="text-center my-2">
                                            <h1>Referral & get</h1>
                                            <h3 style='color:#A425B3;'>30% BNB + 70% wDot</h3>
                                        </div>
                                        <div class="px-2 ei-newslatter-form">
                                             <div class="form-group">
                                                <label for="referral_code">Get Your Referral Link</label>
                                                <input 
                                                    type="text" id="referral_code" 
                                                    style="font-size:16px !important;"  
                                                    v-model="affLinkAddress" 
                                                    class="form-control form-control-lg  ref-form"
                                                >
                                            </div>

                                            <div><button @click.prevent="copyAffLink" class="btn btn-gradient btn-lg btn-block">Copy Link</button></div>
                                        </div>
                                        <div style="height: 113px;"></div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
                </section>
        </div>
        
    </section>
</template>

<script>

import FlipClock from 'flipclock'
require ("flipclock/src/scss/flipclock.scss");

import Swal from 'sweetalert2/dist/sweetalert2.js'

import 'sweetalert2/src/sweetalert2.scss'

import appConfig from "../config/app"

import WalletProvider from "@libertypie/wallet-provider"

import { utils as ethersUtils, Contract, providers as ethersProviders } from "ethers";

import contractAbi from "../data/contractAbi.json";

import WalletConnect from "@walletconnect/web3-provider";


export default {
    
    data(){return {
        _appConfig: appConfig,
        showAll: false,
        buyAmountInBNB: appConfig.minBuyAmountInBNB,
        _walletProvider: null,
        isConnected: false,
        walletAccount: null,
        web3Provider: null,
        web3: null,
        contractInstance: null,
        affLinkAddress: "",
        affLink: this.getCurHost(),
        noOfTokensPerAmount: 0
    }},

    mounted(){

        let _this = this;

        window.addEventListener("connect-wallet", () => {
            _this.connectToWallet();
        })

        this.runCountDownTimer();
    
        this.connectToWallet();

        this.calculateFinalTokens();

    },

    

    watch: {

        affLinkAddress(){
            //lets get address
            if(this.affLinkAddress == "" || !ethersUtils.isAddress(this.affLinkAddress)) return;

            //let addressB64 =  window.btoa(this.affLinkAddress);

            this.affLink = `${this.getCurHost()}/${this.affLinkAddress}`
        },

        isConnected() {
            this.updateState();
        },

        walletAccount() {
            this.affLinkAddress = this.walletAccount;
             this.updateState();
        }
    },



    methods: {

        calculateFinalTokens() {
            this.noOfTokensPerAmount = this.buyAmountInBNB * appConfig.numberOfTokensPerBNB;
        },

        getVisitorsReferrer(){

            let refAddress = this.$route.params.affId || "";

            if(refAddress.length > 0 && ethersUtils.isAddress(refAddress)){
                return refAddress;
            }

            return appConfig.defaultReferralAddres;
        },

        getCurHost(){
            let port = window.location.port;
            let url = `${window.location.hostname}`;

            if(port.toString() != "" && ![80,443].includes(port)){
                url = `${url}:${port}`;
            }

            return url;
        },

        copyAffLink() {
            
            if(!this.isConnected){
                this.connectToWallet();
                return false;
            }

            if(this.affLink == ""){
                this.affLink = this.getCurHost();
            }

            navigator.clipboard.writeText(this.affLink);

            Swal.fire(
                '',
                'Affiliate link copied to clipboard',
                'success'
            )
        },
        
        updateState(){
            
            this.$store.commit("setWalletConnectInfo",{
                isWalletConnected: this.isConnected,
                walletInfo: this.walletAccount
            });
        },

        async connectToWallet(){

              let providers = {
                "web3_wallets": {
                    connect_text: "Connect with Metamask or TrustWallet"
                },
                "binance_chain_wallet": {
                    connect_text: "Connect with  Binance Chain Wallet"
                },

                walletconnect: {
                    connect_text: "Sync with WalletConnect",
                    name: "WalletConnect",
                    package: WalletConnect,
                    options: {
                        rpc: {
                            56: "https://bsc-dataseed.binance.org/"
                        },
                        mobileLinks: [
                            "rainbow",
                            "metamask",
                            "argent",
                            "trust",
                            "imtoken",
                            "pillar",
                        ],
                    }
                }
            };

            this._walletProvider = new WalletProvider({
                cacheProvider: true,
                providers,
                debug: true
            });

            this.handleWalletProviderEvents();

            await  this._walletProvider.connect();

        },

        handleWalletProviderEvents() {

            this._walletProvider.on("connect",({provider,chainId,account})=>{
                
                if(!this.isSupportedChain(chainId)){
                    Swal.fire({
                        icon: 'error',
                        title: 'Oops...',
                        text: 'Unsupported Chain, Kindly Switch to Binance Smart Chain',
                     })

                    this.isConnected = false;
                    this.web3Provider = null;
                    this.walletAccount = null;

                    return false;
                } //end if

                this.web3Provider = provider;
                
                this.isConnected = true;

                this.walletAccount = account;

                let signer = (new ethersProviders.Web3Provider(provider)).getSigner();

                //lets initialize contract
                this.contractInstance = new Contract( appConfig.contractAddress , contractAbi , signer );

            });  

            this._walletProvider.on("accountsChanged",(accountsArray)=>{
                this.walletAccount = accountsArray[0];
            })

            //wallet's current chain is changed
            //@param Array<string> accounts
            this._walletProvider.on("chainChanged",(chainId)=>{
                if(!this.isSupportedChain(chainId)){
                    Swal.fire({
                        icon: 'error',
                        title: 'Oops...',
                        text: 'Unsupported Chain, Kindly Switch to Binance Smart Chain',
                     })
                    
                    this.isConnected = false;
                    this.walletAccount = null;

                    return false;
                }

                this._walletProvider.connect();
            })

            //wallet or web3 disconnected
            this._walletProvider.on("disconnect",(error)=>{
                window.location.reload();
            })  
        },

        isSupportedChain(chainId){
            return (chainId == appConfig.supportedChainId);
        },

        async processAirdrop(){

            if(this.contractInstance == null){
                this.connectToWallet();
                return;
            }

            let airdropFee = (appConfig.pricePerAirdropInBNB || 0).toString()

            let airdropFeeWei = ethersUtils.parseEther(airdropFee);

            Swal.fire({
                title: 'Processing Airdrop',
                text: 'Confirm from your wallet',
                timerProgressBar: true,
                didOpen: () => Swal.showLoading()
            })

            try{

                let result = await this.contractInstance.airdrop( this.getVisitorsReferrer(), {value: airdropFeeWei})

                Swal.close();

                 Swal.fire({
                    icon: 'success',
                    title: 'Hurray !',
                    text: `Airdrop completed successfully`,
                })
            } catch (e){

                Swal.close();
                
                Swal.fire({
                    icon: 'error',
                    title: 'Oops...',
                    text: `Airdrop Failed to complete (${e.message})`,
                })
                    	
                console.log("Airdrop Error", e, e.stack)
            }
        }, //end airdrop
    
        async processBuyToken() {


            if(this.contractInstance == null){
                this.connectToWallet();
                return;
            }

            if(this.buyAmountInBNB < appConfig.minBuyAmountInBNB){
                Swal.fire({icon: 'error', text: `Amount cannot be less than ${appConfig.minBuyAmountInBNB} BNB`})
                return;
            }

            try {

                console.log(this.buyAmountInBNB)
                let amountToBuyInBNBWei = ethersUtils.parseEther(this.buyAmountInBNB.toString());

                Swal.fire({
                    title: 'Processing Request',
                    text: 'Confirm from your wallet',
                    timerProgressBar: true,
                    didOpen: () => Swal.showLoading()
                })

                console.log(this.getVisitorsReferrer())

                 let result = await this.contractInstance.buy( this.getVisitorsReferrer(), { value: amountToBuyInBNBWei })

                Swal.close();

                Swal.fire({ icon: 'success',title: 'Hurray !', text: `Token Buy Successful` })

            } catch (e){

                Swal.close();
                
                Swal.fire({
                    icon: 'error',
                    title: 'Oops...',
                    text: `Airdrop Failed to complete (${e.message})`,
                })
                    	
                console.log("Airdrop Error", e, e.stack)
            }
        }, //end
        
        runCountDownTimer() {

            //lets get
            let countDownDate = (new Date(appConfig.presaleEndDate)).getTime();

            let _presaleTimer = this.$refs.presaleTimer;

            let intval = setInterval(function() {
                

                 // Get today's date and time
                var now = new Date().getTime();

                // Find the distance between now and the count down date
                var distance = countDownDate - now;

                let days, hours, minutes, seconds;

                if(distance <= 0) {
                    
                    days = hours = minutes = seconds = 0;

                } else {
                    
                    days = Math.floor(distance / (1000 * 60 * 60 * 24));
                    hours = Math.floor((distance % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
                    minutes = Math.floor((distance % (1000 * 60 * 60)) / (1000 * 60));
                    seconds = Math.floor((distance % (1000 * 60)) / 1000);
                }

                let result = `<div class='item'><span class='time'>${days}</span><span   class='label'>Days</span></div>`+
                            `<div class='item'><span class='time'>${hours}</span><span   class='label'>Hours</span></div>`+
                            `<div class='item'><span class='time'>${minutes}</span><span class='label'>Minutes</span></div>`+
                            `<div class='item'><span class='time'>${seconds}</span><span class='label'>Seconds</span></div>` 

                
                _presaleTimer.innerHTML = result;

                if(distance <= 0) clearInterval(intval);

            }, 1000);
        }
    }
}
</script>

<style lang="scss">

    #presaleTimer {

        max-width: 100%;
        display: flex;
        flex-direction: row;
        flex-wrap: wrap;

        .item {
            
            border-radius: 10px;
            margin: 10px;
            display: flex;
            flex-direction: column;
            padding: 20px;
            
            @media screen and (max-width: 500) {
                padding: 10px;
            }

            .time{
                font-size: 32px;
                font-weight: bold;
                text-align: center;
            }

            .label {
                font-size: 14px;
                text-align: center;
            }
        }
    }

    #ei-screenshots{
        background: url('/assets/img/app-landing/shape/ss-shape1.png'),  url('/assets/img/app-landing/shape/ss-shape2.png');
        background-repeat: no-repeat, no-repeat;
        background-position: left 500px, right bottom;
    }
    

    .btn, .input-group , .ref-form{
        height: 60px !important;
        border-radius: 60px !important;
        font-size: 26px !important;
    }


    .btn-gradient {
        background: linear-gradient(30deg, #6b3fc0 0%, #f400a1 100%);
        color: #fff !important; 
    }

    .btn-outline-purple{
        border-color: #6b3fc0 !important;
        color: #6b3fc0;
    }

    .btn-outline-purple:hover {
        background: #6b3fc0;
        color: #fff;
    }

    .service_content_box{ background: #fff !important; z-index: 999 !important; }
</style>