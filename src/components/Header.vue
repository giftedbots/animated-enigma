<template>
    <!-- Start of header section
        ============================================= -->
        <header id="header-eight" class="main-header-eight">
            <div class="appheader-content">
                <div class="site-logo float-left">
                    <a href="#"><img src="assets/app/logo.png" alt=""></a>
                </div>
                <nav class="navigation-eight ul-li">
                    <Menu />
                </nav>
                <div class="h-eight-social ul-li float-right clearfix">
                    <ul>
                        <li><a href="#"><i class="fab fa-telegram"></i></a></li>
                        <li><a href="#"><i class="fab fa-twitter"></i></a></li>
                    </ul>
                </div>
                <div class="sign-up-btn-eight text-center float-right clearfix" style="min-width: 160px !important;">
                    <a href="#" @click.prevent="connectToWallet" v-show="!isWalletConnected">Connect</a>
                    <a href="#" @click.prevent v-show="isWalletConnected">{{addressShort}}</a>
                </div>
            </div>
            <!-- /desktop-menu -->
            <div class="appi-ei-mobile_menu relative-position">
                <div class="appi-ei-mobile_menu_button appi-ei-open_mobile_menu">
                    <i class="fas fa-bars"></i>
                </div>
                <div class="appi-ei-mobile_menu_wrap">
                    <div class="mobile_menu_overlay appi-ei-open_mobile_menu"></div>
                    <div class="appi-ei-mobile_menu_content">
                        <div class="appi-ei-mobile_menu_close appi-ei-open_mobile_menu">
                            <i class="far fa-times-circle"></i>
                        </div>
                        <div class="m-brand-logo text-center">
                            <img src="assets/img/app-landing/logo/f-logo.png" alt="">
                        </div>
                        <nav class="appi-ei-mobile-main-navigation  clearfix ul-li">
                            <Menu  id="main-nav" class="navbar-nav text-capitalize clearfix" />
                        </nav>
                    </div>
                </div>
            </div>
            <!-- /mobile-menu -->
        </header>
    <!-- End of header section
        ============================================= -->

</template>

<script>
import Menu from './Menu.vue'
import {mapState} from "vuex"

export default {
  components: { Menu },

    data(){return{
        addressShort: ""
    }},

    watch: {
        isWalletConnected(){
            this.getShortenedAddr();
        }
    },

    computed: {
        ...mapState([
            "isWalletConnected",
            "walletInfo"
        ])
    },

  methods: {
      connectToWallet(){
          window.dispatchEvent(new CustomEvent('connect-wallet'));
      },

       getShortenedAddr(){

            if(!this.isWalletConnected) return "";

            let addr = this.walletInfo;

            this.addressShort = `${addr.substr(0,4)}..${addr.substr(-4)}`

            return this.addressShort;
        },

  } 
}
</script>

<style>

</style>