<template>
<div class="main-container">
  <div class="vin-container">
    <div class="vin-subcontainer">
    <h3>Please Enter your Licence Plate Number</h3>
        <label>
          <input type="text" placeholder= "4-TFL-22" v-model="vin" class="vin-text" maxlength="8" @keyup="addDashes()" @input="vin = $event.target.value.toUpperCase()"/>
        </label>
        <button class="send" @click="getVinDetails()">send</button>
        <span :class="isVinEmpty ? 'd-block' : 'd-none'" class="error-vin">Please enter valid input!!!!!!!!!!!!!</span>
        <span :class="isVinError ? 'd-block' : 'd-none'" class="error-vin">{{this.vinErrorDetails}}</span>
    </div>
  </div>
  <div class="vin-details" :class="isVinSuccess ? 'd-block' : 'd-none'">
    <div v-if="isVinError">{{this.vinError}}</div>
      <div class="vin-success-container">
      <label class="vin-label">Trade name</label>
      <p class="vin-data">{{this.vehicleDetails.handelsbenaming}}</p>
      <label class="vin-label">Date of first admission</label>
      <p class="vin-data">{{this.vehicleDetails.datum_eerste_toelating}}</p>
      <label class="vin-label">Fuel description</label>
      <p class="vin-data">{{this.fuelDesc}}</p>
    </div>
  </div>

  <div class="image-container" :class="isVinImageSuccess ? 'd-block' : 'd-none'">
    <div>
      <b-carousel
        id="carousel-1" v-model="slide" :interval="3000" controls indicators
        background="#ccc" img-width="1024" img-height="480"
        style="text-shadow: 1px 1px 2px #000;" @sliding-start="onSlideStart"
        @sliding-end="onSlideEnd">
        <b-carousel-slide
          v-for="item in carouselItems"
          :img-src="item.image"></b-carousel-slide>
      </b-carousel>
    </div>
  </div>
</div>
</template>
<script>

import axios from 'axios'

export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  data: function() {
    return{
    vehicleDetails: {},
    vin: '',
    fuelDesc: '',
    isVinError: false,
    isVinEmpty: false,
    isVinSuccess: false,
    isVinImageSuccess: false,
    vinError: '',
    vehicleImageList: [],
    image_one: '',
    carouselItems: [],
    vinErrorDetails: ''
    }
  },
  methods: {
    getVinDetails () {
      if (this.vin.length == 0) {
        this.isVinEmpty = true;
        this.isVinSuccess = false;
        this.isVinImageSuccess = false;
        this.isVinError = false;
      } else {
          axios.get(`https://api.overheid.io/voertuiggegevens/${this.vin}`, {headers: {'ovio-api-key':'3884f4aa1a65159a3d999121857a2eeffa58a538e89991388bca295d55815093'}}).then((response) => {
            this.isVinEmpty = false;
            this.isVinSuccess = true;
            this.isVinError = false;
            this.vehicleDetails = response && response.data ? response.data : '';
            this.fuelDesc = this.vehicleDetails.brandstof[0] && this.vehicleDetails.brandstof[0].brandstof_omschrijving ? this.vehicleDetails.brandstof[0].brandstof_omschrijving : '';
            this.getRelatedImages();
          }).catch((error)=>{
            this.isVinEmpty = false;
            this.isVinSuccess = false;
            this.isVinImageSuccess = false;
            this.isVinError = true;
            this.vinErrorDetails = error.response.data.error;
          })
      }
    },
    getRelatedImages () {
      this.carouselItems = [];
      axios.get(`https://api.shutterstock.com/v2/images/search?query=${this.vehicleDetails.handelsbenaming}&per_page=1`, {headers: {Authorization: 'Bearer ' + 'v2/UXFrczlPOUVDZ1pTRTUwcnROY0NxQXdQNWdMeklZZWYvMjkzMTgxMTA5L2N1c3RvbWVyLzMvQ2dSX2dTVnR5RGFNUzV0WjlFQlZPV1V4Rl9NZzY1M2xfV0tlNTNxanlLSXRIUnFOLTEyYmlBQ2FWdmFsOUtmc05oSFo5SWpTX0dvbXdZUC1wb2FTSXltYlktVEhpS09XS2hRTGpDcVhRSkRleVZ2UkNLb0dEV2daSWtmU05iYl95SHZoTW43ZWdmNWFzSmI5aFRINm41SkpPWC1Ta0YxampKSVdNQnN4TkJjRVFkQmVJX2N5dV9nN19wcjRpdGVId29iYWdjZGFQbG9mbU0wLWhpVm5DQQ',
            }}).then((response) => {
            this.vehicleImageList = response && response.data ? response.data : '';
            this.vehicleImageList.data[0] && this.vehicleImageList.data[0].assets && this.vehicleImageList.data[0].assets.small_thumb ? this.carouselItems.push({image: this.vehicleImageList.data[0].assets.small_thumb.url}) : null;
            this.vehicleImageList.data[0] && this.vehicleImageList.data[0].assets && this.vehicleImageList.data[0].assets.large_thumb ? this.carouselItems.push({image: this.vehicleImageList.data[0].assets.large_thumb.url}) : null;
            this.vehicleImageList.data[0] && this.vehicleImageList.data[0].assets && this.vehicleImageList.data[0].assets.huge_thumb ? this.carouselItems.push({image: this.vehicleImageList.data[0].assets.huge_thumb.url}) : null;
            this.isVinImageSuccess = this.carouselItems.length > 1; 
          }).catch((error)=>{
            
          })
    },
    addDashes() {
      if (this.vin.length == 1) {
          this.vin += "-";
      }
      else if (this.vin.length == 5) {
          this.vin += "-";
      }
    }
  }
}
</script>

<style>
.vin-container {
  background-image: url('../assets/sonata-2.png');
  height: 600px;
  width: 100%;
  background-size: 100% 90%;
  background-repeat: no-repeat;
  display: inline-block;
}
.vin-text {
  width: 150px;
  background-color: yellow;
  height: 40px;
  border: 0;
  padding: 0 15px;
  border-radius: 8px 0 0 8px;
  font-weight: bolder;
}
.send {
  height: 40px;
  border: 0;
  width: 65px;
  border-radius: 0 8px 8px 0;
}
.vin-success-container {
  padding: 20px 0;
}
#app, body {
  margin: 0;
}
.vin-subcontainer {
  height: 100px;
  bottom: -20%;
  position: relative;
  z-index: 9999;
}
.vin-subcontainer h3 {
  color: #fff;
  font-size: 3.5vw;
}
.vin-details {
  position: relative;
    top: -135px;
    width: 70%;
    margin: 0 auto;
    background-color: #fff;
    border-radius: 10px;
}
.vin-data {
  font-weight: bold;
}
.error-vin {
  color: red;
}
@media only screen and (max-width: 500px) {
    .vin-subcontainer h3 {
      font-size:5vw;
    }
}
</style>
