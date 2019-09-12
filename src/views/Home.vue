<template>
  <div class="home">
    <div class="headerSpan">
      <span class="spanLeft">origin request body</span>
      <span class="spanRight">AI processed request body</span>
    </div>
    <CodeDiff
      :output-format="outputFormat"
      :old-string="leftHtml"
      :new-string="rightHtml"
      :context="100"
    />
  </div>
</template>

<script>
import CodeDiff from "../code-diff/CodeDiff";
import config from "../config";
export default {
  name: "Home",
  components: {
    CodeDiff
  },
  data() {
    return {
      leftHtml: "raw body",
      rightHtml: "processed body",
      websocket: null,
      isActive: false,
      outputFormat: "side-by-side"
    };
  },
  computed: {
    compiledMarkdownLeft: function() {
      return this.leftHtml;
    },
    compiledMarkdownRight: function() {
      return this.rightHtml;
    }
  },
  mounted() {
    this.initWebSocket();
  },
  methods: {
    initWebSocket: function() {
      this.websocket = new WebSocket(config.url);
      this.websocket.onopen = this.webSocketOnOpen;
      this.websocket.onmessage = this.webSocketOnMessage;
      this.websocket.onclose = this.webSocketClosed;
    },
    webSocketOnOpen: function() {
      console.log("Socket open");
    },
    webSocketOnMessage: function(msg) {
      this.isActive = true;
      let responseData = JSON.parse(msg.data);
      if (responseData.msgType == 0) {
        this.leftHtml = responseData.msg;
      } else {
        this.rightHtml = responseData.msg;
      }
    },
    webSocketClosed: function(msg) {
      console.log("socket closed" + msg);
    }
  }
};
</script>

<style scoped>
.headerSpan {
  width: 100%;
  height: 5%;
  font-size: 1.5em;
  margin-top: 1%;
  margin-bottom: 0.3%;
  font-family: Arial, Helvetica, sans-serif;
  font-variant: small-caps;
}
.spanLeft {
  float: left;
  width: 50%;
  text-align: center;
}
.spanRight {
  float: right;
  width: 50%;
  text-align: center;
}
.home {
  width: 100%;
  height: 100%;
}
</style>
<style>
#app > div > div.left > pre {
  margin-top: 0;
}
#app > div > div.right > pre {
  margin-top: 0;
}
pre {
  display: block;
  font-family: monospace;
  white-space: pre-wrap;
  margin: 1em 0px;
}
</style>
