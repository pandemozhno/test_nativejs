<template>
  <div :class="['place_dropdown',{'place_dropdown_show': open_flag}]">
    <div class="place_dropdown_header" @click="show(showClose())">
      <div><img :src="publicPath + 'assets/imgs/svg/' + item.icon" height="20px" width="auto" /></div>
      <div  v-if="showClose()" class="place_dropdown_header_button">
        <img v-if="!open_flag" :src="publicPath + 'assets/imgs/svg/dropdown_open.svg'" width="13px" heigth="" />
        <img v-if="open_flag" :src="publicPath + 'assets/imgs/svg/dropdown_close.svg'" width="13px" heigth="" />
      </div>
    </div>
    <div class="place_dropdown_content_wrapper">
        <template v-if="item.type == 'stocks'">
          <template v-if="CARDS_TYPES != null">
          <div class="place_dropdown_content_item" v-for="(data, index) in PARTNER_VIEW.stocks" >
            <span class="place_dropdown_content_item_title">{{$t('place.discount')}} {{data.discount}}%</span>
            <span class="place_dropdown_content_item_hint">{{$t('place.for_card')}} {{getCardName(data.card_type_id)}}</span>
          </div>   
          </template>
          <template v-else>
            <!-- PRELOAD -->
          </template>      
        </template>
        <template v-if="item.type == 'invoice'">

          <div class="place_dropdown_content_item" >
            <span class="place_dropdown_content_item_title">{{ $t('place.avg_invoice')}}</span>
            
                    
                <template >
                  <span class="place_dropdown_content_item_hint">{{PARTNER_VIEW.avg_invoice}} {{ $t('cards.R')}}</span>
                </template>
           
           
          
          </div>   

        </template>

        <template v-if="item.type == 'addresses'">

          <div class="place_dropdown_content_item" v-for="(data, index) in PARTNER_VIEW.adresses" >
            <span class="place_dropdown_content_item_title">{{data.address}}</span>
            <template v-if="checkCount(PARTNER_VIEW.adresses)">
                <template v-if="open_flag">
                  <a :href="'tel:' + getPhoneNumber(data.phone)" class="place_dropdown_content_item_link">{{data.phone}}</a>
                </template>                
                <template v-else>
                  <span class="place_dropdown_content_item_hint">ещё {{ getCount(PARTNER_VIEW.adresses)}} адреса</span>
                </template>
            </template>
            <template v-else>
              <a :href="'tel:' + getPhoneNumber(data.phone)" class="place_dropdown_content_item_link">{{data.phone}}</a>
            </template>
            
          </div>   

        </template>
        <template v-if="item.type == 'website'">
          <div class="place_dropdown_content_item">
            <span class="place_dropdown_content_item_title">{{$t('place.site')}}</span>
            <a :href="getURL(PARTNER_VIEW.website)" target="_blank" class="place_dropdown_content_item_link">{{getHumanUrl(PARTNER_VIEW.website)}}</a>
          </div>         
        </template>
        <template v-if="item.type == 'instagram'">
          <div class="place_dropdown_content_item">
            <span class="place_dropdown_content_item_title">Instagram</span>
            <a :href="PARTNER_VIEW.instagram" target="_blank" class="place_dropdown_content_item_link">@{{ getHumanUrlInsta(PARTNER_VIEW.instagram)}}</a>
          </div>         
        </template>
        <template v-if="item.type == 'worktime'">

          <div class="place_dropdown_content_item" v-for="(data, index) in PARTNER_VIEW.work_time.config" >
            <span class="place_dropdown_content_item_title" style="text-transform: uppercase;">{{data.day}}</span>
            <span class="place_dropdown_content_item_link">{{ data.from }}<span v-if="data.break != ''">-{{ data.break }}</span><template v-if="data.to != ''">-</template>{{data.to}}</span>
          </div>   

        </template>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import { mapGetters, mapActions } from 'vuex';
export default {
  name: 'placedropdown',
  props: {
    item: Object,
  },
  data: function () {
    return {
      publicPath: process.env.BASE_URL,
      open_flag: false,
      countItems:0,
    }
  },
  
  computed: {
   ...mapGetters(['CARDS_TYPES', 'PARTNER_VIEW'])
  },
  methods:{
    getURL(url){
      if(url.indexOf('://') != -1){
        return url
      }else{
        return "http://" + url
      }
    },
    getCardName(id){
      for(var i= 0; i < this.CARDS_TYPES.length; i++){
        if(this.CARDS_TYPES[i].id == id){
          return this.CARDS_TYPES[i].name
        }
      }
    },
    getCount(array){
      let count = 0;
       for(var i =0; i < array.length; i++){
          count += 1;
        }
      
        return count
        
    },
    checkCount(array){
     
      let count = 0;
        for(var i = 0; i < array.length; i++){
          count += 1;
        }
        if(count > 1){
          return true
        }else{
          return false
        }
    },
    getPhoneNumber(tel){
      return tel.toString().split('-').join('').toString();
    },
    getHumanUrlInsta(url){
      let parts;
      parts = url.split('/');
      return parts[3]
    },
    getHumanUrl(url){
      let newUrl;
      if(url.indexOf('://') != -1){
        
        newUrl = url.split('://');
        newUrl = newUrl[1]
        if(newUrl[newUrl.length-1] == "/"){
          newUrl = newUrl.slice(0, -1)
        } 
        return  newUrl
      }else{
        return url
      }

      
    },
    showClose(){
      switch(this.item.type) {
          case 'website':  // if (x === 'value1')
            return false
            break;

          case 'instagram':  // if (x === 'value2')
            return false
            break;

          case 'stocks':
            return this.checkCount(this.PARTNER_VIEW.stocks)
          case 'addresses':
            return this.checkCount(this.PARTNER_VIEW.adresses)
            break;
          case 'worktime':
            return this.checkCount(this.PARTNER_VIEW.work_time.config)
            break;

        }



      
    
    },
    counter(index){
      this.countItems += 1;
      return index;
    },
    show(param){
    
      if(param != false){
        this.open_flag = !this.open_flag
      }
      
    }
  },
  created(){
  
  }
}
</script>