<template>
  <div class="mycards_content content__panelmargin">

    <div class="mycards_content_wrapper">
      <div class="mycards_info_wrapper">
        <div class="mycards_card_actions">
        <div class="cards_desktop_types">
          <template v-for="(card_type, index) in CARDS_TYPES">
            <div :class="getTopTabClass(index)" @click="setCurrentCardType(index)">{{ card_type.name }}</div>
          </template>
         

        </div>

        <p class="buy_card_text buy_card_text__margin">{{CARDS_TYPES[currentCardTypeIndex].description}}</p>

        <div class="buy_card_info_button_action mycards__margin">
              <card-action 
              addclass="buy_card_action_button"
              :icon="publicPath + 'assets/imgs/svg/action_buttons/time.svg'" 
              :text="CARDS_TYPES[currentCardTypeIndex].validity.toString()" 
              :subtext="$t('cards.validity')"
              />
              <card-action 
              addclass="buy_card_action_button"
              :icon="publicPath + 'assets/imgs/svg/action_buttons/discounts.svg'" 
              :text="CARDS_TYPES[currentCardTypeIndex].price.toString() + ' P'"
              :subtext="$t('cards.card_price')"
              />

          </div>
        </div>
        <div class="add_number_card_wrapper mycards__margin"  @click="showAddForm" v-if="!addByNumberFlag">
            <span class="add_number_card_text">{{$t('cards.already_card')}}</span>
            <span class="add_number_card_link" > {{$t('cards.bind_by_number')}}</span>
        </div>
        <template v-if="addByNumberFlag">
              <div class="mycards__margin">
                <h3 class="add_card_form_title"> {{ $t('cards.bind_by_number_title')}}</h3>
                <div v-if="BUYCARD_ADDBYNUMBER_ERRORS != null" class="validationtip validationtip_error"><span>{{ $t('errors.error') }}</span> {{ showError(BUYCARD_ADDBYNUMBER_ERRORS) }} </div>   
                <ur-buy-input v-model="card_number" :label="$t('cards.card_number')" :placeholder="$t('cards.buycards_form.required')" @blur="validateCardNumber"/>
                <ur-button 
                :text="$t('cards.card_bind')"
                addclass="mycards__margin"
                @click="activateCard"
                icon="false"
                />
              </div>
        </template>


        <buy-card-form @close="" @send="buycard" addclass="buyform_desktop"/>
      </div>

      <div class="mycards_card_tabs">
         <div @click="setCurrentCardType(index)" :class="['cards_desktop_tab_card',{'cards_desktop_tab_card__active' : currentCardTypeIndex == index  } ]" v-for="(card, index) in CARDS_TYPES" >
            <img :src="storage_path + card.photo" width="100%" />
            <span class="mycards_card_number">0000 0000 0000 0000</span>
          </div>
      </div>






    </div>
   
       
  </div>
</template>

<script>
// @ is an alias to /src
import Urbutton from '@/components/auth_forms/Urbutton.vue'
import UrBuyInput from '@/components/shared/urbuyinput.vue'
import Cardaction from '@/components/mycards/cardaction.vue'
import buycardform from '@/components/buycard/buycard_form.vue'
import { mapGetters, mapActions } from 'vuex';
import { Carousel, CarouselItem } from 'vue-l-carousel'
import { ValidateMixin } from '@/mixins/validateMixin.js'
import { ValidateShowError } from '@/mixins/ValidateShowError.js'
export default {
  name: 'mycardsmobile',
  mixins: [ValidateMixin, ValidateShowError],
  components: {
    "ur-button": Urbutton,
    "card-action": Cardaction,
    "carousel": Carousel,
    "carousel-item": CarouselItem,
    "ur-buy-input" : UrBuyInput,
    "buy-card-form": buycardform 
  },
  data: function () {
    return {
      currentCardTypeIndex: 0,
      publicPath: process.env.BASE_URL,
      addByNumberFlag: false,
      card_number: null,
      storage_path: 'https://storage.ur.discount/source/',
      
    }
  },
  
  computed: {
    ...mapGetters(['TOKEN', 'CARDS_TYPES', 'BUYCARD_ADDBYNUMBER_ERRORS','NOW_BUYCARD_NUMBER']),
  },
   watch:{
    NOW_BUYCARD_NUMBER: function (newValue, Value){
      this.$router.push("/mycards/" + newValue);
    }
  },
  created(){
    if(this.$route.params.type_id != null){
      for(var i=0; i < this.CARDS_TYPES.length; i++){
        if(this.CARDS_TYPES[i].id == this.$route.params.type_id){
          this.currentCardTypeIndex = i
          this.$router.replace("/buycard/" + this.CARDS_TYPES[this.currentCardTypeIndex].id)
          break;
        }
          
      }

    }else{
      this.$router.replace("/buycard/" + this.CARDS_TYPES[this.currentCardTypeIndex].id)
    }
    
  },
  methods:{
    
    ...mapActions(['BUY_CARD','ADD_ERROR','RESET_MYCARDS_FLAG', 'SET_MYCARDS_FLAG', 'SET_REDIRECT_URI', 'ADD_BY_NUMBER_ERROR', 'DELETE_BY_NUMBER_ERROR', 'ACTIVATE_CARD', 'REFRESH_ADD_CARD_NUMBER_ERRORS']),
    buycard(formdata){

      
      formdata.append('card_type_id', this.CARDS_TYPES[this.currentCardTypeIndex].id)
      this.BUY_CARD(formdata)
    },
    getTopTabClass(index){
      if(index == this.currentCardTypeIndex){
        return 'cards_desktop_type cards_desktop_type_active'
      }else{
        return 'cards_desktop_type'
      }
      
    },
    activateCard(){
      this.validateCardNumber()
      let formdata = new FormData()
      if(this.BUYCARD_ADDBYNUMBER_ERRORS == null){
        formdata.append('number', this.card_number)
        this.ACTIVATE_CARD(formdata)
      }
    },
    setCurrentCardType(index){
      this.$router.replace("/buycard/" + this.CARDS_TYPES[index].id)
      this.currentCardTypeIndex = index;
    },


    changeCard(){

    },
    goBack(){
      this.$router.go(-1)
    },
    showBuyForm(){
      this.buy_form_flag = true;
      this.add_form_flag = false;
    },
    showAddForm(){
      if(this.TOKEN != null){
        this.addByNumberFlag = true;
      }else{
        this.$router.replace('/login')
        this.SET_MYCARDS_FLAG()
        this.SET_REDIRECT_URI('/buycard')
        // добавить редирект Uri  и Включить флаг
      }
      
    },
    closeBuyForm(){
      this.addByNumberFlag = false;
    }
  },

}
</script>
