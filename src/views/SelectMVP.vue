<template>
  <div>
    <van-nav-bar
      title="选择MVP"
    />
    <div class="showAllPlayers">
      <div v-for="player in allPlayers" :key="player.id" class="singlePlayer" @click="selectOn=player.id">
        <img src="../assets/people.png">
        <!--        玩家名称-->
        <span>{{ player.name }}</span>
        <br/>
        <!--        该玩家角色名-->
        <span>{{ player.roleName }}</span>
        <div class="rate" v-show="btnClickedCount===0">
          <van-rate icon="like" void-icon="like-o" v-show="selectOn==player.id" v-model="value" :count="3" readonly/>
        </div>
      </div>
    </div>
    <van-button
      color="linear-gradient(to right, #4bb0ff, #6149f6)"
      :style="{ marginTop: '20%', marginBottom: '20%'}" @click="comfirmMVP" type="info">
      确定
    </van-button>
  </div>
</template>

<script>
  import {Dialog} from 'vant';

  export default {
    name: "selectMVP.vue",
    data: function () {
      return {
        playerId: JSON.parse(window.localStorage.getItem("roleID")),//该房间当前玩家uuid
        selectOn: '',   //玩家选择的MVP的id
        value: 3, //星的数量
        //allPlayers: this.$store.state.Players,  //该局游戏里的所有玩家
        btnClickedCount: 0,   //提交mvp的次数，只能选一次MVP
        timer: 0
      }
    },
    computed: {
      allPlayers() {
        return this.$store.state.playersAll
      }
    },
    methods: {
      //轮询
      loop() {
        const playerId = this.playerId;
        if (this.$route.path === '/selectMVP') {
          this.$store.state.timer = setInterval(() => {
            this.$store.dispatch("loopStatusInMVP", playerId)
          }, 3000);
        }
      },
      comfirmMVP() {  //确定选择的MVP，轮询，进入排行榜页面
        // this.$router.push('/ranklist');  //测试，直接跳转
        console.log(this.btnClickedCount);
        if (this.btnClickedCount === 0) {   //如果是第一次提交MVP
          this.btnClickedCount++;
          let uuid = this.playerId;
          let voterId = this.selectOn;
          console.log('选择的mvp' + voterId);
          //向服务器发请求，选好了mvp
          this.$store.dispatch('selectMVP', {uuid, voterId});
          //然后轮询，当所有玩家都选好后，跳转到排行榜
          this.loop();
        } else {
          Dialog({message: '不可重复选择MVP'});
        }
      },
    },
  }
</script>

<style scoped>
  .showAllPlayers {
    width: auto;
    height: auto;
    background-color: #fff;
    border-radius: 8px;
    /*border: 2px solid transparent;*/
    border-image: linear-gradient(to top, #F80, #2ED);
    border-image-slice: 10;
    margin-top: 10%;
  }

  .singlePlayer {
    width: 20%;
    height: 35%;
    display: inline-block;
    margin: 3%;
    position: relative;
  }

  .singlePlayer > img {
    width: 100%;
    height: 100%;
  }

  .rate {
    position: absolute;
    top: 30%;
  }
</style>
