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
                       <div ref="flipClock" class="presaleTimer"></div> 
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
                                                <label for="referral_code">Get Your Airdrop Referral Link</label>
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

export default {
    
    data(){return {
        _appConfig: appConfig,
        showAll: false,
        buyAmountInBNB: 0.1,
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

        $(function(){
            
            let date =  new Date(appConfig.presaleEndDate);

            var today = new Date();

            var dif = date.getTime()/1000 - today.getTime()/1000;
            var timeLeft = Math.abs(dif);


             var clock = $('.presaleTimer').FlipClock({
                autoStart: false,
                clockFace: 'DailyCounter',
                countdown: true
            });

            clock.setTime(timeLeft);
            clock.start(); 
            
        });
    
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

            return this.walletAccount;
        },

        getCurHost(){
            let port = window.location.port;
            let url = `${window.location.protocol}://${window.location.hostname}`;

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

            if(this.buyAmountInBNB <= 0){
                Swal.fire({icon: 'error', text: `Amount cannot be less than 0 BNB`})
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
        } //end 
    }
}
</script>

<style>

    #ei-screenshots{
        background: url('/assets/img/app-landing/shape/ss-shape1.png'),  url('/assets/img/app-landing/shape/ss-shape2.png');
        background-repeat: no-repeat, no-repeat;
        background-position: left 500px, right bottom;
    }
    .flip-clock-wrapper ul   {
        
        width: 50px !important;
        font-size: 20px !important;
        font-weight: normal;
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