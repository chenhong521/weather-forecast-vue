<template>
  <div class="day">
    <mu-container style="padding: 0; background-color: white">
      <mu-row class="d2" @click="back()" v-if="list.length" :style="{backgroundImage:'url('+list[0].results[0].now.imageurl+dayOrNight+'.jpg'}">
        <mu-col span="9">
          <div class="d3">
            &nbsp;{{list[0].results[0].now.text}}
          </div>
          <div class="d4">
            {{list[0].results[0].location.name}}
          </div>
        </mu-col>
        <mu-col span="3">
          <div class="d5">
            {{list[0].results[0].now.temperature}}
          </div>
        </mu-col>
      </mu-row>
      <slip-del
        v-for="(item, i) in list"
        :key="i"
        ref="slipDel"
        @slip-open="slipOpen"
        @del-click="del(i)"
        v-if="i!=0"
      >
        <div class="demo-item">
          <mu-row class="d2" @click="navToFav(item.results[0].location.name)" :style="{backgroundImage:'url('+item.results[0].now.imageurl+dayOrNight+'.jpg'}">
            <mu-col span="9">
              <div class="d3">
                &nbsp;{{item.results[0].now.text}}
              </div>
              <div class="d4">
                {{item.results[0].location.name}}
              </div>
            </mu-col>
            <mu-col span="3">
              <div class="d5">
                {{item.results[0].now.temperature}}
              </div>
            </mu-col>
          </mu-row>
        </div>
        <div slot="del">
          <mu-icon size="32" value="delete" style="display: block; margin: 0 auto;"></mu-icon>
        </div>
      </slip-del>
      <mu-button icon style="float: right" @click="choose">
        <mu-icon value="add_circle_outline"></mu-icon>
      </mu-button>
    </mu-container>
  </div>
</template>

<script>
  import SlipDel from 'vue-slip-delete'
  export default {
    name: "CityComponent",
    components: {SlipDel},
    data() {
      return {
        list: []
      }
    },
    computed:{
      dayOrNight:function () {
        var d = new Date();
        if (d.getHours()>=6&&d.getHours()<=18)
          return 1
        else
          return 2
      },
      defaultPic:function () {
        if (d.getHours()>=6&&d.getHours()<=18)
          return "https://friendly-wilson-dbafa5.netlify.com/default_city_day.jpg"
        else
          return "https://friendly-wilson-dbafa5.netlify.com/default_city_night.jpg"

      }
    },
    methods: {
      choose(){
        this.$router.push({
          path: '/CitySelect',
          name: 'CitySelect'
        })
      },
      navToFav(cityName) {
        console.log(cityName);
        localStorage.setItem("choosen",cityName)
        this.$router.push({
          path: '/Favourite',
          name: 'Favourite'
        });
      },
      back() {
        this.$router.push({path: '/'});
      },
      getData() {
        var _this = this;
        var curr = localStorage.getItem('currentCity');
        var cities = curr;
        var c = JSON.parse(localStorage.getItem('cityList'));
        //console.log(c);
        if (c!=undefined) {
          for (var ind = 0; ind < c.length; ind++){
            cities = cities +','+ c[ind];
          }
        }
        var url = 'apis/getlistnowweather?locationlist='+cities;
        this.$http.get(url).then(function (res) {
          _this.list = res.data.info;
          console.table(_this.list);
        });
      },
      slipOpen(vm) {
        // 无需手动关闭
      },
      del(i) {
        var cities = JSON.parse(localStorage.getItem('cityList'));
        cities.splice(i-1,1);
        this.list.splice(i,1);
        localStorage.setItem('cityList',JSON.stringify(cities));
      }
    },
    mounted(){
      this.getData();
    }
  }
</script>

<style scoped>
  /*白天主题*/
  .day {
    background-color: white;
    border: 0px;
  }

  /*夜晚主题*/
  .night {
    background-color: #1C2626;
    border: 0px;
  }

  .d2 {
    /*background-image: url("http://pde0lokle.bkt.clouddn.com/%E6%88%90%E9%83%BD1.jpg");*/
    background-size: 100% auto;
    background-repeat: no-repeat;
  }

  .d3 {
    color: white;
    font-size: 1.3rem;
    padding-top: 2.5rem;
    padding-left: 0.5rem;
    text-shadow: 0.1em 0.1em 0.08em black
  }

  .d4 {
    color: white;
    font-size: 2.2rem;
    padding-left: 0.7rem;
    margin-top: -0.5rem;
    text-shadow: 0.1em 0.1em 0.08em black
  }

  .d5 {
    color: white;
    font-size: 4.4rem;
    margin-top: 0.87rem;
    padding-right: 0.5rem;
    float: right;
    text-shadow: 0.05em 0.05em 0.05em black
  }

  .d6 {
    margin-top: 0.5rem;
    margin-left: 21.1rem;
    width: 30px;
    height: 30px;
  }

  .divwrap{
    height: 400px;
    overflow-y: auto;
    position: fixed;
    left: 0;
    bottom: 0;
    width: 100%;
    z-index: 1000;
  }
  .divwrap>>>.distpicker-address-wrapper{
    color: #999;
  }
  .divwrap>>>.address-header{
    position: fixed;
    bottom: 400px;
    width: 100%;
    background: rgba(255,255,255,0.9);
    color:black;
  }
  .divwrap>>>.address-header ul li{
    flex-grow: 1;
    text-align: center;
  }
  .divwrap>>>.address-header .active{
    color: black;
    border-bottom:rgba(255,255,255,0.9) solid 8px
  }
  .divwrap>>>.address-container .active{
    color: #000;
  }

</style>
