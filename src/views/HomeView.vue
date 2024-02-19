<template>
  <div class="home">
    <div>
      <p>websocket demo</p>
      <button @click="websocketSend">Send Msg</button>
    </div>
  </div>
</template>

<script>
// import WebSocket from 'vue-native-websocket';
export default {
  name: 'HomeView',
  data() {
    return {
      websock: null,
    }
  },
  computed: {
  },
  created() {
    this.initWebSocket();
  },
  mounted() {
    //this.initWebSocket();
  },
  destroyed: function () { // 离开页面生命周期函数
    this.websocketclose();
  },
  methods: {
    initWebSocket: function () {
      var url = "ws://127.0.0.1:8899/ws"
      this.websock = new WebSocket(url);
      this.websock.onopen = this.websocketOnopen;
      this.websock.onerror = this.websocketOnerror;
      this.websock.onmessage = this.websocketOnmessage;
      this.websock.onclose = this.websocketOnclose;
    },
    websocketOnopen: function () {
      console.log("WebSocket连接成功");
      //心跳检测重置
      //this.heartCheck.reset().start();
    },
    websocketOnerror: function (e) {
      console.log("WebSocket连接发生错误");
      this.reconnect();
    },
    websocketOnmessage: function (e) {
      //console.log('监听关闭' + e)
      console.log("- - 接收消息 - -", e.data);
    },
    websocketOnclose: function (e) {
      console.log("connection closed (" + e.code + ")");
      this.reconnect();
    },
    websocketSend(text) { // 数据发送
      const token = "mytoken"
      try {
        this.websock.send("12345",{token});
      } catch (err) {
        console.log("send failed (" + err.code + ")");
      }
    },

    reconnect() {
      var that = this;
      if (that.lockReconnect) return;
      that.lockReconnect = true;
      //没连接上会一直重连，设置延迟避免请求过多
      setTimeout(function () {
        console.info("尝试重连...");
        that.initWebSocket();
        that.lockReconnect = false;
      }, 5000);
    },
  }
}
</script>
