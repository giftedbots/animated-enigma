<template>
  <div>
      <div id="preloader" class="ei-preloader"></div>
      <div class="ei-up">
        <a href="#" id="scrollup" class="ei-scrollup text-center"><i class="fas fa-angle-up"></i></a>
      </div>

      <Header />  
      <BannerSection />
      <PresaleSection />
      <div id="features">
        <Features2 />
        <Features />
      </div>
      <Roadmap />
      <Investors />
      <FAQ />
      <Footer />
  </div>
</template>

<script>

import Header from "../components/Header.vue";
import Footer from "../components/Footer.vue";
import PresaleSection from '../components/PresaleSection.vue';
import BannerSection from "../components/BannerSection.vue";
import FAQ from "../components/FAQ.vue";
import Features from "../components/Features.vue";
import Features2 from "../components/Features2.vue";
import Roadmap from "../components/Roadmap.vue";
import Investors from "../components/Investors.vue";

export default {
  components: {Header, Footer, PresaleSection, BannerSection, FAQ, Features, Features2, Roadmap, Investors }
}
</script>
